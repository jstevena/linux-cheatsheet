# SSH dan Manajemen User

> **Untuk:** Generate dan pasang SSH key, konfigurasi SSH, dan bikin user baru.

## Generate SSH Key
```bash
ssh-keygen -t ed25519 -C "core@server"
```

## Setup / Konfigurasi SSH
```bash
# Edit config SSH (misal ganti port)
sudo nano /etc/ssh/sshd_config

# Generate key (di sisi client)
ssh-keygen -t ed25519 -C "linux@pc"

# Salin public key ke server
ssh-copy-id -p 6969 -i ~/.ssh/id_ed25519.pub johan@10.0.0.60

# Atau tambahkan manual di server
nano ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

**Ringkasan:**
- `id_ed25519.pub` → di **CLIENT**
- `authorized_keys` → di **SERVER**

Router OpenWrt (Dropbear) beda path: [OpenWrt_SSH_Key](../homelab-cheatsheet/OpenWrt_SSH_Key.md).

## User Baru
```bash
sudo adduser namabaru
sudo usermod -aG sudo namabaru
su - namabaru
sudo whoami
```

## Wake On LAN
Pindah ke file sendiri: [Wake_on_LAN](Wake_on_LAN.md).
