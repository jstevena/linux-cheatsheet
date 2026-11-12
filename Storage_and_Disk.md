# Storage dan Disk

> **Untuk:** Format dan cek HDD, passthrough disk ke VM Proxmox, dan mount cloud storage (rclone).

## Format HDD
```bash
# 1. Lihat daftar disk
lsblk

# 2. Unmount disk
sudo umount /dev/sdb1

# 3. Hapus semua signature filesystem
sudo wipefs -a /dev/sdb

# 4. Buat partisi baru
sudo fdisk /dev/sdb

# 5. Format ke ext4
sudo mkfs.ext4 /dev/sdb1
```

## fsck (Cek dan Perbaiki Filesystem)
```bash
# Pastikan disk tidak ter-mount
sudo lsof /mnt/HDD
sudo umount /mnt/HDD

# Cek saja (read-only, tidak mengubah apa pun)
sudo fsck -n /dev/sdXn

# Cek dan perbaiki otomatis
sudo fsck -y /dev/sdXn
```

## Passthrough HDD ke VM (Proxmox)
```bash
# Lihat ID disk
ls -n /dev/disk/by-id/

# Pasang disk ke VM (contoh: VM ID 101, slot scsi1)
/sbin/qm set 101 -scsi1 /dev/disk/by-id/ata-ST500DM002-1BD142_Z3TVLJD7
```

## Rclone - Mount Cloud Storage
```bash
# Buat direktori mount sebelum menjalankan service
mkdir -p /home/johan/Cloud/OneDrive
mkdir -p "/home/johan/Cloud/Google Drive"
mkdir -p /home/johan/Cloud/.cache
```

**Service: OneDrive** - `/etc/systemd/system/rclone-onedrive.service`:
```ini
[Unit]
Description=Rclone Mount for OneDrive
AssertPathIsDirectory=/home/johan/Cloud/OneDrive
After=network-online.target

[Service]
Type=simple
ExecStart=/usr/bin/rclone mount OneDrive: /home/johan/Cloud/OneDrive \
    --vfs-cache-mode=writes \
    --cache-dir=/home/johan/Cloud/.cache/
ExecStop=/bin/fusermount -u /home/johan/Cloud/OneDrive
Restart=on-failure
User=johan

[Install]
WantedBy=multi-user.target
```

**Service: Google Drive (Terenkripsi)** - `/etc/systemd/system/rclone-gdrive.service`:
```ini
[Unit]
Description=Rclone Mount for Google Drive Encrypted
AssertPathIsDirectory=/home/johan/Cloud/Google Drive
After=network-online.target

[Service]
Type=simple
ExecStart=/usr/bin/rclone mount "Google Drive Encrypted:" "/home/johan/Cloud/Google Drive" \
    --vfs-cache-mode=full \
    --cache-dir=/home/johan/Cloud/.cache/
ExecStop=/bin/fusermount -u "/home/johan/Cloud/Google Drive"
Restart=on-failure
User=johan

[Install]
WantedBy=multi-user.target
```

```bash
# Reload systemd dan aktifkan
sudo systemctl daemon-reload
sudo systemctl enable --now rclone-onedrive
sudo systemctl enable --now rclone-gdrive

# Cek status
sudo systemctl status rclone-onedrive
sudo systemctl status rclone-gdrive
```
