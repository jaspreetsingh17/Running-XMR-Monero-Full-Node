# === Monero Daemon Configuration ===

# blockchain location
data-dir=/data/monero

# pruning (saves space)
prune-blockchain=1
sync-pruned-blocks=1

# disable update checks
check-updates=disabled
enable-dns-blocklist=1

# logs
log-file=/data/monero/logs/monero.log
log-level=0
max-log-file-size=2147483648

# restricted RPC (wallet connections)
rpc-restricted-bind-ip=127.0.0.1
rpc-restricted-bind-port=18089
rpc-ssl=disabled

# ZMQ (internal apps only)
zmq-pub=tcp://127.0.0.1:18083

# networking
disable-rpc-ban=1
out-peers=12
in-peers=48
limit-rate-up=1048576
limit-rate-down=1048576
max-txpool-weight=2684354560
