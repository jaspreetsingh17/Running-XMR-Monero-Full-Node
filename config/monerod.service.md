[Unit]
Description=Monero Daemon
After=network-online.target
Wants=network-online.target

[Service]
User=monero
Group=monero
ExecStart=/usr/local/bin/monerod --config-file /etc/monero/monerod.conf --non-interactive
Type=simple
Restart=on-failure
RestartSec=30
RuntimeDirectory=monero

# Hardening
NoNewPrivileges=yes
ProtectSystem=full
ProtectHome=yes
PrivateTmp=yes
ProtectKernelModules=yes
ProtectKernelTunables=yes
LockPersonality=yes
MemoryDenyWriteExecute=yes

StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target
