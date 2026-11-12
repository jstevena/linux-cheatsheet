# Mergerfs Storage Pool

> **Untuk:** Gabungin beberapa HDD jadi satu pool (mergerfs) dengan aturan tulis `epmfs`.

Dua HDD (`/mnt/disk1`, `/mnt/disk2`) digabung jadi satu pool di `/mnt/pool`. Data di disk tetap filesystem biasa (ext4), mergerfs cuma layer penggabung. Reinstall OS aman selama disk nggak diformat.

## Install
```bash
sudo apt update
sudo apt install mergerfs
```
Kalau versinya terlalu lama, ambil `.deb` dari https://github.com/trapexit/mergerfs/releases:
```bash
wget https://github.com/trapexit/mergerfs/releases/download/<versi>/mergerfs_<versi>.debian-<codename>_amd64.deb
sudo dpkg -i mergerfs_*.deb
```
Codename Debian: `lsb_release -cs`.

## Cek disk
```bash
lsblk -f
```

## Mount point
```bash
sudo mkdir -p /mnt/disk1 /mnt/disk2 /mnt/pool
```

## /etc/fstab
```
# hdd 1
UUID=051c08c6-c163-4084-8501-96548117819a /mnt/disk1 ext4 defaults,noatime,nofail 0 2

# hdd 2
UUID=92763e5e-0947-4ec5-acd1-4010b4ad89c8 /mnt/disk2 ext4 defaults,noatime,nofail 0 2

# mergerfs
/mnt/disk1:/mnt/disk2 /mnt/pool fuse.mergerfs defaults,nonempty,allow_other,use_ino,cache.files=partial,dropcacheonclose=true,category.create=epmfs,minfreespace=5G 0 0
```

Opsi mergerfs:
- `allow_other`: user/service lain (Samba, Docker, Jellyfin) bisa akses pool
- `use_ino`: inode konsisten, penting buat hardlink (arr)
- `cache.files=partial` + `dropcacheonclose=true`: cache file dibuang saat ditutup
- `category.create=epmfs`: lihat di bawah
- `minfreespace=5G`: disk dengan free space di bawah ini nggak dipilih untuk file baru

## Apply
```bash
sudo systemctl daemon-reload
sudo mount -a
df -h /mnt/pool
ls /mnt/pool
```
Remount pool saja: `sudo umount /mnt/pool && sudo mount /mnt/pool`.

## Policy epmfs (path yang sudah ada, free space terbesar)
File baru cuma ditulis ke disk yang **sudah punya folder induknya**:
- Folder ada di 1 disk: file masuk ke disk itu saja
- Folder ada di 2 disk: file masuk ke disk yang free space-nya lebih besar (mergerfs nggak menduplikasi)
- Folder nggak ada di disk mana pun: gagal (ENOENT)

Bikin folder di disk tertentu langsung di disknya, jangan lewat pool:
```bash
sudo mkdir /mnt/disk1/Film          # Film hanya di disk1
sudo mkdir /mnt/disk1/Series
sudo mkdir /mnt/disk2/Series        # Series ada di 2 disk
```
Folder baru di dalam folder yang sudah ada otomatis ikut disk induknya. Folder baru di root pool masuk ke disk dengan free space terbesar, jadi folder level atas lebih aman dibuat manual.

Policy lain:
- `epff`: disk pertama (urutan branch) yang punya path
- `eplfs`: disk yang punya path dengan free space paling sedikit (ngisi satu-satu)
- `moveonenospc=true` sengaja **tidak** dipakai, karena policy `ep*` nggak bisa pindah ke disk yang foldernya nggak ada

## Glob vs daftar eksplisit
- Eksplisit (dipakai di atas): `/mnt/disk1:/mnt/disk2`. Urutan dan isi pasti.
- Glob: `/mnt/disk*`. Disk baru otomatis ikut kalau dibuat `/mnt/disk3`, tapi folder lain yang namanya `disk...` juga ikut kegabung.

## Waspada disk gagal mount
Kalau satu HDD gagal mount (kabel lepas), mount point-nya tetap ada tapi kosong, dan file bisa nyasar ke disk OS. Cek dengan `df -h /mnt/disk1 /mnt/disk2` sebelum nulis data penting. Service yang butuh pool pakai `RequiresMountsFor=/mnt/pool` di unit systemd-nya.
