# Running XMR (Monero) Full Node

[![Monero](https://img.shields.io/badge/Monero-v0.18.4.2-FF6600?style=flat&logo=monero&logoColor=white)](https://www.getmonero.org/)
[![Ubuntu](https://img.shields.io/badge/Ubuntu-20.04%20|%2022.04-E95420?style=flat&logo=ubuntu&logoColor=white)](https://ubuntu.com/)
[![License](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

A complete guide and automation script for deploying a **Monero (XMR) Full Node** on Ubuntu Linux. This repository provides both automated and manual deployment options with GPG signature verification for security.

---

## Table of Contents

- [Features](#features)
- [Requirements](#requirements)
- [Quick Start](#quick-start)
- [Manual Deployment](#manual-deployment)
- [Configuration](#configuration)
- [Ports](#ports)
- [Useful Commands](#useful-commands)
- [Monitoring](#monitoring)
- [Troubleshooting](#troubleshooting)
- [Contributing](#contributing)

---

## Features

- **One-command deployment** via automated bash script
- **GPG signature verification** for binary authenticity
- **Systemd integration** for automatic startup and recovery
- **UFW firewall configuration** included
- **Detailed logging** with configurable log levels
- **Restricted RPC** enabled for secure wallet connections
- **Pruning support** (optional) to save disk space

---

## Requirements

### Hardware

| Component | Minimum | Recommended |
|-----------|---------|-------------|
| **CPU** | 2 cores | 4+ cores |
| **RAM** | 4 GB | 8 GB |
| **Disk** | 160 GB (pruned) | 400+ GB (full) |
| **Network** | Broadband | Stable connection |

### Software

- **OS**: Ubuntu 20.04 LTS or Ubuntu 22.04 LTS
- **User**: Root access required for installation

---

## Quick Start

### Automated Deployment

Clone this repository and run the deployment script:

```bash
# Clone the repository
git clone https://github.com/jaspreetsingh17/Running-XMR-Monero-Full-Node.git
cd Running-XMR-Monero-Full-Node

# Make script executable and run
chmod +x xmr.sh
sudo ./xmr.sh
```

The script will:
1. Update your system packages
2. Install required dependencies
3. Download Monero binaries (v0.18.4.2)
4. Verify GPG signatures and checksums
5. Install binaries to `/usr/local/bin/`
6. Create configuration at `/etc/monero/monerod.conf`
7. Set up systemd service
8. Configure UFW firewall rules
9. Start the Monero daemon

---

## Manual Deployment

For step-by-step manual installation, see [Deploy_Manually.md](Deploy_Manually.md).

For detailed requirements and prerequisites, see [Requirements.md](Requirements.md).

---

## Configuration

The main configuration file is located at `/etc/monero/monerod.conf`:

```conf
# Blockchain data location
data-dir=/data/monero

# Enable pruning (uncomment to save disk space)
#prune-blockchain=1
#sync-pruned-blocks=1

# Logging
log-file=/data/monero/logs/monero.log
log-level=0
max-log-file-size=2147483648

# RPC Settings
rpc-restricted-bind-ip=0.0.0.0
rpc-restricted-bind-port=18089
rpc-ssl=autodetect

# Network tuning
out-peers=12
in-peers=48
limit-rate-up=1048576
limit-rate-down=1048576
```

### Enable Pruning (Optional)

To reduce disk usage (~65% savings), uncomment these lines in the config:

```conf
prune-blockchain=1
sync-pruned-blocks=1
```

---

## Ports

| Port | Protocol | Purpose | Binding |
|------|----------|---------|---------|
| **18080** | TCP | P2P Network | 0.0.0.0 |
| **18089** | TCP | Restricted RPC | 0.0.0.0 |
| **18081** | TCP | Unrestricted RPC | 127.0.0.1 |
| **18083** | TCP | ZMQ Publisher | 127.0.0.1 |

---

## Useful Commands

### Service Management

```bash
# Check status
systemctl status monerod

# Start/Stop/Restart
systemctl start monerod
systemctl stop monerod
systemctl restart monerod

# Enable/Disable on boot
systemctl enable monerod
systemctl disable monerod
```

### Logs

```bash
# View systemd logs
journalctl -u monerod -f

# View Monero logs
tail -f /data/monero/logs/monero.log
```

### Node Information

```bash
# Check version
monerod --version

# Get sync status (via RPC)
curl -X POST http://127.0.0.1:18089/json_rpc \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":"0","method":"get_info"}'

# Check blockchain height
monerod status
```

---

## Monitoring

For production deployments, consider setting up:

- **Prometheus** + **Grafana** for metrics dashboards
- **Node Exporter** for system metrics
- **Custom Monero exporter** for:
  - Blockchain height
  - Peer count
  - Transaction pool size
  - Sync progress

---

## Troubleshooting

### Node won't start

```bash
# Check for errors
journalctl -u monerod -n 50 --no-pager

# Verify config syntax
monerod --config-file /etc/monero/monerod.conf --check-updates disabled
```

### Slow sync

- Ensure adequate disk I/O (SSD recommended)
- Check network connectivity
- Increase `out-peers` in config
- Consider using `--fast-block-sync=1` flag

### Disk full

Enable pruning or expand your `/data` partition:

```bash
# Check disk usage
df -h /data

# Enable pruning (requires resync)
# Add to config: prune-blockchain=1
```

### Connection issues

```bash
# Verify firewall rules
ufw status

# Check if ports are open
ss -tlnp | grep -E '18080|18089'
```

---

## Repository Structure

```
├── README.md              # This file
├── Requirements.md        # Detailed requirements
├── Deploy_Manually.md     # Manual deployment guide
└── xmr.sh                 # Automated deployment script
```

---

## Contributing

Contributions are welcome! Feel free to:

1. Fork this repository
2. Create a feature branch (`git checkout -b feature/improvement`)
3. Commit your changes (`git commit -am 'Add new feature'`)
4. Push to the branch (`git push origin feature/improvement`)
5. Open a Pull Request

---

## License

This project is open source and available under the [MIT License](LICENSE).

---

## Resources

- [Monero Official Website](https://www.getmonero.org/)
- [Monero GitHub Repository](https://github.com/monero-project/monero)
- [Monero Documentation](https://monerodocs.org/)
- [Monero Stack Exchange](https://monero.stackexchange.com/)

---

<p align="center">
  <b>Support the Monero Network - Run Your Own Node!</b>
</p>
