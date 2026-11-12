# Referensi Cepat

> **Untuk:** Cheatsheet cepat: Vim, command Linux umum, dan path penting.

## Vi / Vim - Cheatsheet
```
Simpan tanpa keluar:
  Esc -> :w -> Enter

Simpan dan keluar:
  Esc -> :wq -> Enter

Keluar tanpa menyimpan:
  Esc -> :q! -> Enter
```

## Command Linux yang Umum
```bash
# Lihat disk dan partisi
lsblk
fdisk -l

# Cek penggunaan disk
df -h
du -sh *

# Cek proses yang berjalan
ps aux
htop / btop / bashtop

# Cek port yang terbuka
ss -tuln
ss -lp 'sport = :domain'

# Systemctl
sudo systemctl status <service>
sudo systemctl enable --now <service>
sudo systemctl disable <service>
sudo systemctl restart <service>
sudo systemctl daemon-reload

# Log journal
journalctl -xe
journalctl -u <service> -f

# Cek network interface
ip link
ip addr

# Mount / unmount
sudo mount /dev/sdXn /mnt/point
sudo umount /mnt/point

# Cek file apa yang sedang terbuka di mount point
sudo lsof /mnt/point
```

## Path Penting
| Item | Path |
|---|---|
| Binary user | `/home/johan/.local/bin/` |
| Aplikasi default | `~/.config/mimeapps.list` |
| SSH key (client) | `~/.ssh/id_ed25519` & `~/.ssh/id_ed25519.pub` |
| SSH authorized keys | `~/.ssh/authorized_keys` (di server) |
| Plugin OBS (Flatpak) | `/home/johan/.var/app/com.obsproject.Studio/config/obs-studio/plugins/` |
| Driver Epson | `/opt/epson-inkjet-printer-202101w/` |
