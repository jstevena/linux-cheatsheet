# Desktop & Apps

## Printer (Arch)
```bash
# Install CUPS and GUI configuration tool
sudo pacman -S system-config-printer cups
sudo systemctl enable --now cups

# Install Epson driver (AUR)
paru -S epson-inkjet-printer-escpr

# Fix Epson driver permissions
sudo chown -R root:root /opt/epson-inkjet-printer-202101w/
sudo find /opt/epson-inkjet-printer-202101w/ -type d -exec chmod 755 {} \;
sudo find /opt/epson-inkjet-printer-202101w/ -type f -exec chmod 644 {} \;
sudo chmod 755 /opt/epson-inkjet-printer-202101w/cups/lib/filter/epson_inkjet_printer_filter

# Driver location: /opt/epson-inkjet-printer-202101w/
```

### Network Printer via Samba
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

Example network printer path: `smb://Dad-PC/l3210`

## Fix Brave Browser (Login / Keyring) - Arch + Hyprland/SDDM

Problem: Brave can't save logins due to KWallet vs GNOME Keyring conflict.

```bash
# 1. Install GNOME Keyring
sudo pacman -S gnome-keyring libsecret seahorse

# 2. Backup and disable KWallet
mv ~/.local/share/kwalletd ~/.local/share/kwalletd.bak
mv ~/.config/kwalletrc ~/.config/kwalletrc.bak
```

Create new `~/.config/kwalletrc` to disable KWallet:
```ini
[Wallet]
Enabled=false
```

Edit `/etc/pam.d/sddm` - add these lines:
```
auth       optional     pam_gnome_keyring.so
session    optional     pam_gnome_keyring.so auto_start
```

Full file example:
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
# 4. Enable the daemon
systemctl --user enable --now gnome-keyring-daemon.service

# 5. Verify keyring is running
ps aux | grep gnome-keyring

# 6. Test store and read a secret
secret-tool store --label="Test" test-key test-value
secret-tool lookup test-key
```
