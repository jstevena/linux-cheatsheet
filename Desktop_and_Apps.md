# Desktop dan Aplikasi

> **Untuk:** Printer Epson dan fix Brave/keyring di Arch + Hyprland.

## Printer (Arch)
```bash
# Install CUPS dan tool konfigurasi GUI
sudo pacman -S system-config-printer cups
sudo systemctl enable --now cups

# Install driver Epson (AUR)
paru -S epson-inkjet-printer-escpr

# Perbaiki permission driver Epson
sudo chown -R root:root /opt/epson-inkjet-printer-202101w/
sudo find /opt/epson-inkjet-printer-202101w/ -type d -exec chmod 755 {} \;
sudo find /opt/epson-inkjet-printer-202101w/ -type f -exec chmod 644 {} \;
sudo chmod 755 /opt/epson-inkjet-printer-202101w/cups/lib/filter/epson_inkjet_printer_filter

# Lokasi driver: /opt/epson-inkjet-printer-202101w/
```

### Printer Jaringan via Samba
```bash
sudo pacman -S samba
```

Edit `/etc/samba/smb.conf`:
```ini
[global]
   workgroup = WORKGROUP
   server string = Samba Server
   security = user
   printing = cups
   printcap name = cups
```

```bash
sudo systemctl restart smb nmb
```

Contoh path printer jaringan: `smb://Dad-PC/l3210`

## Fix Brave Browser (Login / Keyring) - Arch + Hyprland/SDDM

Masalah: Brave tidak bisa menyimpan login karena konflik KWallet vs GNOME Keyring.

```bash
# 1. Install GNOME Keyring
sudo pacman -S gnome-keyring libsecret seahorse

# 2. Backup dan nonaktifkan KWallet
mv ~/.local/share/kwalletd ~/.local/share/kwalletd.bak
mv ~/.config/kwalletrc ~/.config/kwalletrc.bak
```

Buat `~/.config/kwalletrc` baru untuk menonaktifkan KWallet:
```ini
[Wallet]
Enabled=false
```

Edit `/etc/pam.d/sddm` - tambahkan baris berikut:
```
auth       optional     pam_gnome_keyring.so
session    optional     pam_gnome_keyring.so auto_start
```

Contoh file lengkap:
```
#%PAM-1.0
auth        include     system-login
auth        optional    pam_gnome_keyring.so

account     include     system-login

password    include     system-login
password    optional    pam_gnome_keyring.so use_authtok

session     optional    pam_keyinit.so force revoke
session     include     system-login
session     optional    pam_gnome_keyring.so auto_start
```

```bash
# 4. Aktifkan daemon
systemctl --user enable --now gnome-keyring-daemon.service

# 5. Pastikan keyring berjalan
ps aux | grep gnome-keyring

# 6. Tes simpan dan baca secret
secret-tool store --label="Test" test-key test-value
secret-tool lookup test-key
```
