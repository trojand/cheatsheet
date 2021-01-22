# Running Systemctl with user privileges

* Warning: I have experienced times where my application/script crashes after 4+ hours
* Template below

```bash
cat >  ~/.config/systemd/user/custom_script.service <<EOF
Description=Custom Script service
After=network-online.target

[Service]
Environment="SCRIPT_LOG_PATH=/home/custom_script_log/"
ExecStart=/bin/bash /home/user/custom_script.sh
Restart=on-failure
RestartSec=5
SuccessExitStatus=3 4
RestartForceExitStatus=3 4

# Hardening
SystemCallArchitectures=native
MemoryDenyWriteExecute=true
NoNewPrivileges=true

[Install]
WantedBy=default.target
EOF
systemctl --user daemon-reload
systemctl --user start custom_script.service
systemctl --user enable custom_script.service
```
