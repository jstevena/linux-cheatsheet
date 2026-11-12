# Sembunyiin Pesan Login (MOTD)

> **Untuk:** Sembunyiin pesan MOTD saat login SSH.

Pesan "Linux johan-nas ... The programs included with the Debian GNU/Linux system..." muncul tiap login SSH.

## Per user (paling gampang)
```bash
touch ~/.hushlogin
```
Logout lalu login lagi. Sekaligus menyembunyikan baris "Last login". Balikin lagi dengan `rm ~/.hushlogin`.

## Semua user
```bash
sudo truncate -s 0 /etc/motd
```
Ini mengosongkan teks "The programs included...". Baris `Linux johan-nas 6.12...` di paling atas dibuat dari sumber lain, jadi kalau masih muncul, pakai `.hushlogin`.
