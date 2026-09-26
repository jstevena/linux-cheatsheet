# Permissions

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

## 3. Troubleshooting Checklist

Urutan debug, dari yang paling sering jadi penyebab:

1. **Cek user Samba terdaftar & punya password:**
   ```bash
   sudo pdbedit -L
   sudo smbpasswd -a <username>
   ```

2. **Cek permission folder tujuan:**
   ```bash
   ls -ld /path/to/folder
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
   - Kalau gagal di sini → masalah di permission Linux, belum sampai ke Samba.
   - Kalau berhasil di sini tapi tetap gagal dari Windows → masalah di Samba auth (kemungkinan besar user belum `smbpasswd`).

7. **Cek mount options mergerfs kalau menyangkut xattr:**
   ```bash
   mount | grep mergerfs
   ```

## Contoh Terapan: Permission untuk Folder Homelab yang Diakses Container (misal FileBrowser/Metube)

Kalau folder ditulis dari dalam container yang jalan sebagai UID/GID tertentu (misal UID 1000 = johan), paling simple: samain ownership folder ke UID/GID itu, dan jalanin container dengan `user: "UID:GID"` yang sama — tanpa perlu ACL.

```bash
# Set kepemilikan dasar folder ke user johan (1000)
sudo chown -R johan:johan /mnt/pool1/Downloads/YouTube
sudo chmod -R 775 /mnt/pool1/Downloads/YouTube
```

Di compose, pastikan container jalan sebagai UID yang sama:
```yaml
    user: "1000:1000"
```
