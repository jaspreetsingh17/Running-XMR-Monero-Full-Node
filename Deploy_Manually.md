# Deployment of a Monero Full Node (Manually)

## 1. Update and prepare the system
Updated package lists and applied upgrades:
```bash
apt update && apt upgrade -y
```

Installed required tools:
```bash
apt install -y curl wget gnupg bzip2 tar ufw
```

## 2. Prepare directories
Verified that the additional disk was mounted at `/data`.

Created folders for blockchain data and logs:
```bash
mkdir -p /data/monero/logs
```

## 3. Download Monero CLI binaries
Navigated to `/tmp` and downloaded the Linux x64 release (`v0.18.4.2`)[Change version accordingly]:
```bash
cd /tmp
curl -LO https://downloads.getmonero.org/cli/monero-linux-x64-v0.18.4.2.tar.bz2
```

## 4. Verify authenticity
Imported the official maintainer’s GPG key:
```bash
curl https://raw.githubusercontent.com/monero-project/monero/master/utils/gpg_keys/binaryfate.asc | gpg --import
```

Downloaded the official hashes:
```bash
curl -s https://www.getmonero.org/downloads/hashes.txt -o /tmp/hashes.txt
```

Verified the signature of the hashes file:
```bash
gpg --verify /tmp/hashes.txt
```

Checked the SHA-256 checksum of my tarball against the trusted list:
```bash
sha256sum monero-linux-x64-v0.18.4.2.tar.bz2
```

## 5. Extract and install binaries
Extracted the archive:
```bash
tar -xvf monero-linux-x64-v0.18.4.2.tar.bz2
```

Moved the binaries (`monerod`, `monero-wallet-cli`, etc.) into `/usr/local/bin`:
```bash
mv monero-x64-linux-gnu-v0.18.4.2/* /usr/local/bin/
```

## 6. Create configuration file
Made a new folder for configs:
```bash
mkdir -p /etc/monero
```

Created and edited `/etc/monero/monerod.conf` with the following content:
```conf
# blockchain location
data-dir=/data/monero

# pruning (optional to save disk space)
#prune-blockchain=1
#sync-pruned-blocks=1

check-updates=disabled
enable-dns-blocklist=1

# logs
log-file=/data/monero/logs/monero.log
log-level=0
max-log-file-size=2147483648

# restricted RPC (wallet connections)
rpc-restricted-bind-ip=0.0.0.0
rpc-restricted-bind-port=18089
rpc-ssl=autodetect

# ZMQ (optional)
zmq-pub=tcp://127.0.0.1:18083

disable-rpc-ban=1
out-peers=12
in-peers=48
limit-rate-up=1048576
limit-rate-down=1048576
max-txpool-weight=2684354560
```

## 7. Create systemd service
Added a new service file at `/etc/systemd/system/monerod.service` with:
```ini
[Unit]
Description=Monero Daemon
After=network-online.target

[Service]
ExecStart=/usr/local/bin/monerod --detach --config-file /etc/monero/monerod.conf --pidfile /run/monerod.pid
ExecStartPost=/bin/sleep 0.1
PIDFile=/run/monerod.pid
Type=forking
Restart=on-failure
RestartSec=30
RuntimeDirectory=monero
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target
```

Reloaded systemd and enabled the service:
```bash
systemctl daemon-reload
systemctl enable --now monerod
```

## 8. Configure firewall
Opened required ports:
```bash
ufw allow 18080/tcp   # P2P
ufw allow 18089/tcp   # RPC
ufw reload
```

## 9. Verify the deployment
Checked service status:
```bash
systemctl status monerod
```

Followed logs:
```bash
journalctl -u monerod -f
tail -f /data/monero/logs/monero.log
```

Confirmed version:
```bash
monerod --version
```

Verified sync progress and peers connected.
