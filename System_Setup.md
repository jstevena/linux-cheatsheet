# Setup Sistem

> **Untuk:** Setup awal sistem: timezone, user, shell, display manager, update otomatis, dan konfigurasi desktop.

## Timezone dan Waktu
```bash
sudo timedatectl set-ntp true
sudo timedatectl set-timezone Asia/Jakarta
timedatectl set-local-rtc 0
```

## User
```bash
# Ganti nama lengkap user
sudo chfn -f "Johanes Steven" johan
```

## Shell - Zsh
```bash
# Jadikan Zsh shell default
chsh -s $(which zsh)
# atau
chsh -s /bin/zsh

# Install Oh My Posh (tema prompt)
curl -s https://ohmyposh.dev/install.sh | bash -s
```

## Performa
```bash
# Auto CPU Frequency (Arch/AUR)
paru -S auto-cpufreq
sudo systemctl enable --now auto-cpufreq.service
```

## Secure Boot (Arch)
```bash
sudo pacman -S sbctl
sbctl create-keys
sbctl enroll-keys -m
sbctl verify
mkinitcpio -P
```

## Display Manager - Ly (Debian)
Ly adalah display manager berbasis TUI.

```bash
# 1. Tambah repo dan install dependency
curl -sS https://debian.griffo.io/EA0F721D231FDD3A0A17B9AC7808B4DD62C41256.asc \
  | sudo gpg --dearmor --yes -o /etc/apt/trusted.gpg.d/griffo.gpg
echo "deb https://debian.griffo.io/apt $(lsb_release -sc) main" \
  | sudo tee /etc/apt/sources.list.d/debian-griffo.list
sudo apt update
sudo apt install zig build-essential libpam0g-dev libxcb-xkb-dev xauth \
  xserver-xorg brightnessctl git

# 2. Build dari source
git clone https://github.com/fairyglade/ly.git
cd ly
zig build
sudo zig build installexe -Dinit_system=systemd

# 3. Aktifkan service
sudo systemctl enable ly
sudo systemctl enable ly@tty2.service
sudo systemctl disable getty@tty2.service

# 4. Nonaktifkan display manager lama (contoh: SDDM)
sudo systemctl disable sddm
```

## Automated Updates (Debian, cron)
Update paket otomatis sebulan sekali lewat `/etc/cron.monthly`. Isi script `/etc/cron.monthly/auto-update`:
```sh
#!/bin/sh
apt-get update
apt-get -y upgrade
apt-get -y autoclean
apt-get -y autoremove
```
Bikin executable:
```bash
sudo chmod +x /etc/cron.monthly/auto-update
```
- Nama file di `/etc/cron.*` **nggak boleh pakai titik** (misal `auto-update.sh`), karena `run-parts` mengabaikannya.
- Cek script terdeteksi: `run-parts --test /etc/cron.monthly`
- Pastikan cron jalan: `systemctl status cron`
- `upgrade` otomatis termasuk kernel dan Docker. Reboot tetap manual.

## Lain-lain
```text
# Lokasi binary user
/home/johan/.local/bin/

# Config aplikasi default
~/.config/mimeapps.list

# Lokasi plugin OBS (Flatpak)
/home/johan/.var/app/com.obsproject.Studio/config/obs-studio/plugins/
```

```bash
# Fix menu "Open With" kosong di Dolphin (Arch + Hyprland)
sudo pacman -S archlinux-xdg-menu
XDG_MENU_PREFIX=arch- kbuildsycoca6
# Tambahkan ke ~/.config/hypr/hyprland.conf:
env = XDG_MENU_PREFIX,arch-

# yt-dlp (salin ke folder binary user)
cp /usr/bin/yt-dlp /home/johan/.local/bin/yt-dlp

# DDCUtil (atur brightness monitor lewat DDC/CI)
sudo modprobe i2c-dev
echo i2c-dev | sudo tee /etc/modules-load.d/i2c-dev.conf
```
