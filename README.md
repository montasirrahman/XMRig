## Oracle Linux 8 Setup

Run the following as `root`:

```bash
# Install screen, git, and jq
dnf install -y https://yum.oracle.com/repo/OracleLinux/OL8/developer/EPEL/x86_64/getPackage/screen-4.6.2-10.el8.x86_64.rpm
dnf install -y git jq

# Clone XMRig (custom repo)
if [ ! -d /etc/XMRig ]; then
    git clone https://github.com/montasirrahman/XMRig.git /etc/XMRig
    rm -rf /etc/XMRig/.git/ /etc/XMRig/README.md
else
    echo "Directory /etc/XMRig already exists. Skipping clone."
fi

# Set rig-id to hostname in config.json
HOSTNAME=$(hostname)
jq --arg host "$HOSTNAME" '.pools[0]."rig-id" = $host' /etc/XMRig/config.json > /etc/XMRig/config_tmp.json && \
mv -f /etc/XMRig/config_tmp.json /etc/XMRig/config.json

# Make xmrig executable
chmod +x /etc/XMRig/xmrig

# Create systemd service
cat << 'EOF' > /etc/systemd/system/xmrig.service
[Unit]
Description=XMRig miner service inside screen
After=network.target

[Service]
Type=forking
ExecStart=/bin/bash -c "/bin/screen -dmS xmrig /etc/XMRig/xmrig"
Restart=always
User=root

[Install]
WantedBy=multi-user.target
EOF

# Enable and start the service
systemctl daemon-reexec
systemctl daemon-reload
systemctl enable xmrig.service
systemctl start xmrig.service

# Check status
systemctl status xmrig.service

# Clear history
cat /dev/null > ~/.bash_history
rm -f ~/.bash_history
history -c

```



## Oracle Linux 7 Setup

Run the following as `root`:

```bash
# Install screen, git, and jq
yum install -y https://yum.oracle.com/repo/OracleLinux/OL7/latest/x86_64/getPackage/screen-4.1.0-0.27.20120314git3c2946.el7_9.x86_64.rpm
yum install -y git jq

# Clone XMRig (custom repo)
if [ ! -d /etc/XMRig ]; then
    git clone https://github.com/montasirrahman/XMRig.git /etc/XMRig
    rm -rf /etc/XMRig/.git/ /etc/XMRig/README.md
else
    echo "Directory /etc/XMRig already exists. Skipping clone."
fi

# Set rig-id to hostname in config.json
HOSTNAME=$(hostname)
jq --arg host "$HOSTNAME" '.pools[0]."rig-id" = $host' /etc/XMRig/config.json > /etc/XMRig/config_tmp.json && \
mv -f /etc/XMRig/config_tmp.json /etc/XMRig/config.json

# Make xmrig executable
chmod +x /etc/XMRig/xmrig

# Create systemd service
cat << 'EOF' > /etc/systemd/system/xmrig.service
[Unit]
Description=XMRig miner service inside screen
After=network.target

[Service]
Type=forking
ExecStart=/bin/bash -c "/bin/screen -dmS xmrig /etc/XMRig/xmrig"
Restart=always
User=root

[Install]
WantedBy=multi-user.target
EOF

# Enable and start the service
systemctl daemon-reexec
systemctl daemon-reload
systemctl enable xmrig.service
systemctl start xmrig.service

# Check status
systemctl status xmrig.service

# Clear history
cat /dev/null > ~/.bash_history
rm -f ~/.bash_history
history -c
```



## Ubuntu Setup

Run the following as `root`:

```bash
#!/bin/bash

# Install screen, git, and jq
apt update -y
apt install -y screen git jq

# Clone XMRig (custom repo)
if [ ! -d /etc/XMRig ]; then
    git clone https://github.com/montasirrahman/XMRig.git /etc/XMRig
    rm -rf /etc/XMRig/.git/ /etc/XMRig/README.md
else
    echo "Directory /etc/XMRig already exists. Skipping clone."
fi

# Set rig-id to hostname in config.json
HOSTNAME=$(hostname)
jq --arg host "$HOSTNAME" '.pools[0]."rig-id" = $host' /etc/XMRig/config.json > /etc/XMRig/config_tmp.json && \
mv -f /etc/XMRig/config_tmp.json /etc/XMRig/config.json

# Make xmrig executable
chmod +x /etc/XMRig/xmrig

# Create systemd service
cat << 'EOF' > /etc/systemd/system/xmrig.service
[Unit]
Description=XMRig miner service inside screen
After=network.target

[Service]
Type=forking
ExecStart=/bin/bash -c "/usr/bin/screen -dmS xmrig /etc/XMRig/xmrig"
Restart=always
User=root

[Install]
WantedBy=multi-user.target
EOF

# Enable and start the service
systemctl daemon-reexec
systemctl daemon-reload
systemctl enable xmrig.service
systemctl start xmrig.service

# Check status
systemctl status xmrig.service

# Clear history
cat /dev/null > ~/.bash_history
rm -f ~/.bash_history
history -c

```



## Remove All (Uninstall XMRig)

```bash
# Stop and disable the service
systemctl stop xmrig.service
systemctl disable xmrig.service

# Kill any running XMRig process
pkill xmrig

# Remove screen session
screen -wipe

# Remove systemd service and XMRig files
rm -f /etc/systemd/system/xmrig.service
rm -rf /etc/XMRig

# Reload systemd
systemctl daemon-reload

echo "🧹 XMRig and its service have been fully removed."

# Clear history
cat /dev/null > ~/.bash_history
rm -f ~/.bash_history
history -c

# Clear history
cat /dev/null > ~/.bash_history
rm -f ~/.bash_history
history -c

```

