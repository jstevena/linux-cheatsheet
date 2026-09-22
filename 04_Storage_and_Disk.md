# Storage & Disk

## Format HDD
```bash
# 1. List disks
lsblk

# 2. Unmount disk
sudo umount /dev/sdb1

# 3. Wipe all filesystem signatures
sudo wipefs -a /dev/sdb

# 4. Create new partition
sudo fdisk /dev/sdb

# 5. Format to ext4
sudo mkfs.ext4 /dev/sdb1
```

## fsck (Check & Repair Filesystem)
```bash
# Make sure the disk is not mounted
sudo lsof /mnt/HDD
sudo umount /mnt/HDD

# Check only (read-only, no changes)
sudo fsck -n /dev/sdXn

# Check and auto-repair
sudo fsck -y /dev/sdXn
```

## HDD Passthrough to VM (Proxmox)
```bash
# List disk IDs
ls -n /dev/disk/by-id/

# Attach disk to VM (example: VM ID 101, slot scsi1)
/sbin/qm set 101 -scsi1 /dev/disk/by-id/ata-ST500DM002-1BD142_Z3TVLJD7
```

## Rclone - Mount Cloud Storage
```bash
# Create mount directories before starting the service
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

**Service: Google Drive (Encrypted)** - `/etc/systemd/system/rclone-gdrive.service`:
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
# Reload systemd and enable
sudo systemctl daemon-reload
sudo systemctl enable --now rclone-onedrive
sudo systemctl enable --now rclone-gdrive

# Check status
sudo systemctl status rclone-onedrive
sudo systemctl status rclone-gdrive
```
