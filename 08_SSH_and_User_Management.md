# SSH & User Management

## Generate SSH Keys
```bash
ssh-keygen -t ed25519 -C "core@server"
```

## SSH Setup / Config
```bash
# Edit SSH config (e.g. change port)
sudo nano /etc/ssh/sshd_config

# Generate key (on the client side)
ssh-keygen -t ed25519 -C "linux@pc"

# Copy public key to server
ssh-copy-id -p 6969 -i ~/.ssh/id_ed25519.pub johan@10.0.0.60

# Or add manually on the server
nano ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

**Summary:**
- `id_ed25519.pub` → on the **CLIENT**
- `authorized_keys` → on the **SERVER**

## New User
```bash
sudo adduser namabaru
sudo usermod -aG sudo namabaru
su - namabaru
sudo whoami
```

## Wake On LAN
```bash
# 1. Install ethtool
sudo pacman -S ethtool

# 2. Check interface name
ip link

# 3. Check WoL status
ethtool enp3s0

# 4. Enable WoL temporarily
sudo ethtool -s enp3s0 wol g

# 5. Confirm
sudo ethtool enp3s0 | grep "Wake-on"
```

**6. Create service to enable WoL automatically on boot** - `/etc/systemd/system/wol.service`:
```ini
[Unit]
Description=Enable Wake-on-LAN
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/bin/ethtool -s enp3s0 wol g

[Install]
WantedBy=multi-user.target
```

```bash
sudo systemctl enable --now wol.service
```
