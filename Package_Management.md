# Package Management

> **Untuk:** Install, update, dan bersihin paket per distro (Arch, Debian, Fedora) plus Flatpak.

## Arch - Pacman
```bash
# Update dan upgrade semua paket
sudo pacman -Syu

# Install paket
sudo pacman -S <package>

# Hapus paket + dependency yang tidak terpakai
sudo pacman -Rns <package>

# Bersihkan cache (simpan 3 versi terakhir)
sudo paccache -r

# Hapus paket yatim (orphan)
sudo pacman -Rns $(pacman -Qtdq)
```

**Aktifkan multilib** (dibutuhkan aplikasi 32-bit / Steam) - edit `/etc/pacman.conf`:
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

# Bersihkan cache AUR
paru -Scc
```

## Arch - Paket yang Umum Dipasang
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
# Update dan upgrade (tambah -y supaya tanpa konfirmasi)
sudo apt update && sudo apt upgrade

# Fix sudo di instalasi baru (sudo belum terinstal / user belum masuk grup sudo)
apt update
apt install sudo
su -
usermod -aG sudo <username>      # logout/login lagi supaya grup aktif

# Bersih-bersih
sudo apt autoclean               # hapus cache paket yang sudah usang
sudo apt autoremove --purge      # hapus paket dan dependency yang tidak terpakai (+ config-nya)
sudo apt clean                   # hapus semua cache paket
```
- Alias `cleanup` (apt clean + autoremove --purge + vacuum journal 2 minggu): [Bash_Aliases](Bash_Aliases.md)
- Update otomatis bulanan lewat cron: [System_Setup](System_Setup.md)
- Update Armbian (ada langkah `armbian-sync` / `armbian-update`): [Recovery_Armbian](../homelab-cheatsheet/Recovery_Armbian.md)

## Fedora - DNF
```bash
# Paket tambahan yang berguna
sudo dnf install libgda

# Install Brave Browser
sudo dnf install dnf-plugins-core
sudo dnf config-manager addrepo \
  --from-repofile=https://brave-browser-rpm-release.s3.brave.com/brave-browser.repo
sudo dnf install brave-browser

# Install driver NVIDIA (dengan Secure Boot)
sudo dnf install https://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm \
  https://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
sudo dnf install kmodtool akmods mokutil openssl
sudo kmodgenca -a
sudo mokutil --import /etc/pki/akmods/certs/public_key.der
sudo dnf install akmod-nvidia
sudo dnf install xorg-x11-drv-nvidia-cuda
modinfo -F version nvidia

# Hapus bloatware Fedora Workstation
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
# Tambah remote Flathub (per user)
flatpak remote-add --if-not-exists --user flathub https://flathub.org/repo/flathub.flatpakrepo

# Install aplikasi
flatpak install flathub \
  com.discordapp.Discord \
  com.github.tchx84.Flatseal \
  com.obsproject.Studio \
  com.github.wwmm.easyeffects
```
