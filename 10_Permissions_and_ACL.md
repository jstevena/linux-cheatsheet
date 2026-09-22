# Permissions & ACL

## 1. Ownership Dasar

Ubah owner folder jadi johan (recursive):
```bash
sudo chown -R johan:johan /path/to/folder
```

Kalau grup mau tetap grup lama (tanpa ganti grup):
```bash
sudo chown -R johan /path/to/folder
```

## 2. Perbedaan Chmod Permission

Format: owner-group-other, tiap digit = read(4) + write(2) + execute(1)

| Mode | Symbolic | Keterangan |
|---|---|---|
| 700 | `rwx------` | cuma owner, group & other no access sama sekali |
| 750 | `rwxr-x---` | owner full, group read+execute, other no access |
| 770 | `rwxrwx---` | owner & group full akses, other no access |
| 775 | `rwxr-xr-x` | owner & group full, other cuma read+execute |
| 777 | `rwxrwxrwx` | semua orang full akses (read/write/execute) |

**Rekomendasi struktur:**
```
/mnt                 -> 755 (default, biasanya sudah begini)
/mnt/pool            -> 755 (cukup buat traverse)
/mnt/pool/Downloads  -> 777 (folder kerja aktual, full akses semua)
```

## 3. ACL

```bash
# Install paket ACL
sudo apt install acl -y

# Tambah ACL rule ke user tertentu
sudo setfacl -R -m u:share:rwx /path/to/folder

# Set default ACL (biar file/folder baru otomatis inherit permission)
sudo setfacl -R -d -m u:share:rwx /path/to/folder

# Cek ACL
getfacl /path/to/folder

# Fix mask ACL kalau permission ke-cut (mask membatasi efektif permission)
sudo setfacl -R -m m::rwx /path/to/folder

# Hapus ACL tertentu
sudo setfacl -x u:share /path/to/folder

# Hapus SEMUA ACL (kembali ke permission Unix biasa)
sudo setfacl -R -b /path/to/folder
```

## 4. Langkah Rollback dari ACL ke Chmod 777
```bash
# 1. Hapus semua ACL di folder Downloads (recursive)
sudo setfacl -R -b /mnt/pool/Downloads

# Verifikasi bersih
getfacl /mnt/pool/Downloads

# 2. Set permission 777 di folder Downloads
sudo chmod -R 777 /mnt/pool/Downloads

# 3. Hapus sisa ACL di /mnt/pool (kalau ada entry tambahan)
sudo setfacl -b /mnt/pool

# 4. Set /mnt dan /mnt/pool ke 755 (folder induk/traverse)
sudo chmod 755 /mnt
sudo chmod 755 /mnt/pool
```

**5. Update smb.conf** - hapus baris terkait ACL (`vfs objects`, `map acl inherit`) karena sudah tidak dipakai:
```ini
[Downloads]
   path = /mnt/pool/Downloads
   browseable = yes
   read only = no
   guest ok = no
   valid users = share
   # (baris vfs objects = acl_xattr dan map acl inherit = yes DIHAPUS)
```

```bash
# 6. Restart Samba
sudo systemctl restart smbd nmbd

# 7. Test akses
sudo -u share ls -la /mnt/pool/Downloads
```

## 5. Troubleshooting Checklist

Urutan debug, dari yang paling sering jadi penyebab:

1. **Cek user Samba terdaftar & punya password:**
   ```bash
   sudo pdbedit -L
   sudo smbpasswd -a <username>
   ```

2. **Cek permission folder tujuan:**
   ```bash
   ls -ld /path/to/folder
   getfacl /path/to/folder    # kalau masih pakai ACL
   ```

3. **Cek permission folder INDUK (parent)** - user butuh execute (x) di SETIAP level folder di atasnya untuk bisa traverse masuk:
   ```bash
   ls -ld /mnt
   ls -ld /mnt/pool
   ```

4. **Cek valid users di smb.conf sudah sesuai (case-sensitive):**
   ```bash
   grep -A5 "\[NamaShare\]" /etc/samba/smb.conf
   ```

5. **Restart service Samba setelah perubahan apapun:**
   ```bash
   sudo systemctl restart smbd nmbd
   ```

6. **Test akses langsung dari Linux** (isolasi masalah network vs permission):
   ```bash
   sudo -u <username> ls -la /path/to/folder
   sudo -u <username> touch /path/to/folder/testfile.txt
   ```
   - Kalau gagal di sini → masalah di permission/ACL Linux, belum sampai ke Samba.
   - Kalau berhasil di sini tapi tetap gagal dari Windows → masalah di Samba auth (kemungkinan besar user belum `smbpasswd`).

7. **Cek mount options mergerfs kalau menyangkut ACL/xattr:**
   ```bash
   mount | grep mergerfs
   ```

## Contoh Terapan: ACL untuk Folder Homelab (misal Metube)
```bash
# Pastikan paket acl terinstal
apt update && apt install -y acl

# Set kepemilikan dasar folder ke user johan (1000)
chown -R 1000:1000 /mnt/pool1/Downloads/YouTube
chmod -R 775 /mnt/pool1/Downloads/YouTube

# Berikan izin akses penuh untuk root (0) dan johan (1000)
setfacl -m u:0:rwx,u:1000:rwx /mnt/pool1/

# Berikan Default ACL agar otomatis turun ke file/folder baru buatan Metube
setfacl -m d:u:0:rwx,d:u:1000:rwx /mnt/pool1/

# Terapkan secara rekursif ke seluruh isi folder saat ini
setfacl -R -m u:0:rwx,u:1000:rwx /mnt/pool1/
setfacl -R -m d:u:0:rwx,d:u:1000:rwx /mnt/pool1/
```
