# Package Management

## Arch - Pacman
```bash
# Update & upgrade all packages
sudo pacman -Syu

# Install a package
sudo pacman -S <package>

# Remove package + unused dependencies
sudo pacman -Rns <package>

# Clean cache (keep last 3 versions)
sudo paccache -r

# Remove orphaned packages
sudo pacman -Rns $(pacman -Qtdq)
```

**Enable multilib** (required for 32-bit apps / Steam) - edit `/etc/pacman.conf`:
```
[multilib]
Include = /etc/pacman.d/mirrorlist
```

## Arch - Paru (AUR Helper)
```bash
# Install paru
sudo pacman -S --needed base-devel git
git clone https://aur.archlinux.org/paru.git
cd paru
makepkg -si

# Clean AUR cache
paru -Scc
```

## Arch - Common Packages
```bash
# Pacman
sudo pacman -S ethtool nwg-look flatpak unzip ufw freerdp \
  qemu-full virt-manager virt-viewer dnsmasq vde2 bridge-utils iptables-nft \
  waybar libvirt edk2-ovmf ffmpeg samba noto-fonts-emoji sbctl mpv \
  ffmpegthumbs bashtop mission-center polkit-gnome cava kalk swww cliphist \
  cowsay partitionmanager wayvnc gwenview kvantum hyprshot hyprlock \
  system-config-printer zsh cups fastfetch sshfs pavucontrol noto-fonts-cjk \
  veracrypt kdeconnect ddcutil bind qbittorrent ttf-roboto jq steam

# AUR (paru)
paru -S brave-bin wlogout nwg-dock-hyprland catppuccin-gtk-theme-mocha \
  ttf-ms-fonts onlyoffice-bin netbeans-bin visual-studio-code-bin sunshine \
  realvnc-vnc-viewer epson-inkjet-printer-escpr peazip qdirstat hdsentinel \
  balena-etcher
```

## Debian - APT
```bash
# Update & upgrade
sudo apt update && sudo apt upgrade

# Fix sudo on fresh install
apt install sudo
su -
usermod -aG sudo <username>

# Clean up
sudo apt autoclean    # remove outdated cached packages
sudo apt autoremove   # remove unused packages and dependencies
sudo apt clean        # remove all cached packages
```

## Fedora - DNF
```bash
# Useful extra package
sudo dnf install libgda

# Install Brave Browser
sudo dnf install dnf-plugins-core
sudo dnf config-manager addrepo \
  --from-repofile=https://brave-browser-rpm-release.s3.brave.com/brave-browser.repo
sudo dnf install brave-browser

# Install NVIDIA Driver (with Secure Boot)
sudo dnf install https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm \
  https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
sudo dnf install kmodtool akmods mokutil openssl
sudo kmodgenca -a
sudo mokutil --import /etc/pki/akmods/certs/public_key.der
sudo dnf install akmod-nvidia
sudo dnf install xorg-x11-drv-nvidia-cuda
modinfo -F version nvidia

# Remove Fedora Workstation bloatware
sudo dnf remove libreoffice\*
sudo dnf remove gnome-maps gnome-weather gnome-boxes gnome-contacts \
  gnome-tour gnome-papers gnome-logs gnome-disk-utility gnome-abrt
sudo dnf remove firefox\*
sudo dnf remove yelp yelp-xsl baobab papers
sudo dnf remove abrt abrt-gui abrt-tui abrt-cli abrt-addon-ccpp \
  abrt-addon-kerneloops abrt-addon-pstoreoops abrt-addon-vmcore abrt-addon-xorg
```

## Flatpak
```bash
# Add Flathub remote (per user)
flatpak remote-add --if-not-exists --user flathub https://flathub.org/repo/flathub.flatpakrepo

# Install apps
flatpak install flathub \
  com.discordapp.Discord \
  com.github.tchx84.Flatseal \
  com.obsproject.Studio \
  com.github.wwmm.easyeffects
```
