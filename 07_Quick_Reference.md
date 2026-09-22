# Quick Reference

## Vi / Vim - Cheatsheet
```
Save without exiting:
  Esc -> :w -> Enter

Save and exit:
  Esc -> :wq -> Enter

Exit without saving:
  Esc -> :q! -> Enter
```

## Common Linux Commands
```bash
# View disks and partitions
lsblk
fdisk -l

# Check disk usage
df -h
du -sh *

# Check running processes
ps aux
htop / btop / bashtop

# Check open ports
ss -tuln
ss -lp 'sport = :domain'

# Systemctl
sudo systemctl status <service>
sudo systemctl enable --now <service>
sudo systemctl disable <service>
sudo systemctl restart <service>
sudo systemctl daemon-reload

# Journal logs
journalctl -xe
journalctl -u <service> -f

# Check network interfaces
ip link
ip addr

# Mount / unmount
sudo mount /dev/sdXn /mnt/point
sudo umount /mnt/point

# Check which files are open on a mount point
sudo lsof /mnt/point
```

## Important Paths
| Item | Path |
|---|---|
| User binaries | `/home/johan/.local/bin/` |
| Default apps | `~/.config/mimeapps.list` |
| SSH keys (client) | `~/.ssh/id_ed25519` & `~/.ssh/id_ed25519.pub` |
| SSH authorized keys | `~/.ssh/authorized_keys` (on the server) |
| OBS plugins (Flatpak) | `/home/johan/.var/app/com.obsproject.Studio/config/obs-studio/plugins/` |
| Epson driver | `/opt/epson-inkjet-printer-202101w/` |
