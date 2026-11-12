# Sudoers (NOPASSWD)

> **Untuk:** Sudo tanpa password untuk perintah tertentu (smartctl, backup, poweroff, turbostat).

Selalu edit pakai `visudo -f` supaya sintaks dicek sebelum disimpan. Kalau salah tulis, sudo bisa rusak.

## smartctl
```bash
sudo visudo -f /etc/sudoers.d/smartctl
```
```
johan ALL=(ALL) NOPASSWD: /usr/sbin/smartctl
```

## Backup app stack
```bash
sudo visudo -f /etc/sudoers.d/backup-appstack
```
```
johan ALL=(ALL) NOPASSWD: /home/johan/scripts/backup-appstack.sh
```

## Poweroff / reboot
```bash
sudo visudo -f /etc/sudoers.d/poweroff-nopasswd
```
```
johan ALL=(ALL) NOPASSWD: /usr/sbin/poweroff, /usr/sbin/shutdown, /usr/sbin/reboot
```

## turbostat
```bash
sudo visudo -f /etc/sudoers.d/turbostat
```
```
johan ALL=(root) NOPASSWD: /usr/sbin/turbostat
```

## Catatan
- **Path harus persis sama** dengan yang dipanggil. Alias `backup-*` di [Bash_Aliases](Bash_Aliases.md) memanggil `/home/johan/.scripts/backup-appstack.sh` (pakai titik), sedangkan entri di atas `/home/johan/scripts/backup-appstack.sh` (tanpa titik). Kalau beda, sudo tetap minta password.
- Script yang ada di home dan bisa diedit user `johan` sendiri, kalau di-NOPASSWD-kan, artinya `johan` efektif punya akses root tanpa password lewat script itu. Untuk lebih aman, pindahkan ke `/usr/local/sbin/` dengan owner `root:root` dan permission `755`.
- Cek hasil: `sudo -l`.
