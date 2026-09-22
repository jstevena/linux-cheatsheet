# System Setup

## Timezone & Time
```bash
sudo timedatectl set-ntp true
sudo timedatectl set-timezone Asia/Jakarta
timedatectl set-local-rtc 0
```

## User
```bash
# Change user full name
sudo chfn -f "Johanes Steven" johan
```

## Shell - Zsh
```bash
# Set Zsh as default shell
chsh -s $(which zsh)
# or
chsh -s /bin/zsh

# Install Oh My Posh (prompt theme)
curl -s https://ohmyposh.dev/install.sh | bash -s
```

## Performance
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
Ly is a TUI-based display manager.

```bash
# 1. Add repo and install dependencies
curl -sS https://debian.griffo.io/EA0F721D231FDD3A0A17B9AC7808B4DD62C41256.asc \
  | sudo gpg --dearmor --yes -o /etc/apt/trusted.gpg.d/griffo.gpg
echo "deb https://debian.griffo.io/apt $(lsb_release -sc) main" \
  | sudo tee /etc/apt/sources.list.d/debian-griffo.list
sudo apt update
sudo apt install zig build-essential libpam0g-dev libxcb-xkb-dev xauth \
  xserver-xorg brightnessctl git

# 2. Build from source
git clone https://github.com/fairyglade/ly.git
cd ly
zig build
sudo zig build installexe -Dinit_system=systemd

# 3. Enable service
sudo systemctl enable ly
sudo systemctl enable ly@tty2.service
sudo systemctl disable getty@tty2.service

# 4. Disable old display manager (example: SDDM)
sudo systemctl disable sddm
```

## Automated Updates (Debian, cron)
```bash
#!/bin/sh
apt-get update
apt-get -y upgrade
apt-get -y autoclean
apt-get -y autoremove
```
Taruh script di atas di `/etc/cron.monthly/auto-update`, lalu:
```bash
sudo chmod +x /etc/cron.monthly/auto-update
```

## Misc
```text
# User binary location
/home/johan/.local/bin/

# Default apps config
~/.config/mimeapps.list

# OBS plugins location (Flatpak)
/home/johan/.var/app/com.obsproject.Studio/config/obs-studio/plugins/
```

```bash
# Fix empty "Open With" in Dolphin (Arch + Hyprland)
sudo pacman -S archlinux-xdg-menu
XDG_MENU_PREFIX=arch- kbuildsycoca6
# Add to ~/.config/hypr/hyprland.conf:
env = XDG_MENU_PREFIX,arch-

# yt-dlp (copy to user binary folder)
cp /usr/bin/yt-dlp /home/johan/.local/bin/yt-dlp

# DDCUtil (control monitor brightness via DDC/CI)
sudo modprobe i2c-dev
echo i2c-dev | sudo tee /etc/modules-load.d/i2c-dev.conf
```
