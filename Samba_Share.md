# Samba Share

> **Untuk:** Setup network share Samba (`Downloads` dan `Johan`) di atas pool mergerfs.

Dua share: `Downloads` (user `share`) dan `Johan` (user `johan`), dua-duanya di atas pool mergerfs.

## Install
```bash
sudo apt update
sudo apt install samba
```

## User
`smbpasswd -a` cuma nambahin user Linux yang **sudah ada**. Password Samba terpisah dari password login.
```bash
sudo smbpasswd -a johan

sudo useradd -M -s /usr/sbin/nologin share
sudo smbpasswd -a share
```

## /etc/samba/smb.conf
Tambahkan di paling bawah (bagian `[global]` bawaan biarkan):
```ini
[Downloads]
   path = /mnt/pool/Downloads
   browseable = yes
   read only = no
   guest ok = no
   valid users = share
   create mask = 0775
   directory mask = 0775
   force user = johan

[Johan]
   path = /mnt/pool
   veto files = /lost+found/
   browseable = yes
   read only = no
   guest ok = no
   valid users = johan
   create mask = 0775
   directory mask = 0775
   force user = johan
```
- `veto files` pakai slash di kiri-kanan. Lebih dari satu: `/lost+found/.Trash-1000/`.
- `force user = johan`: semua file dibuat sebagai `johan`, jadi isi pool harus owner `johan`.
- Dengan policy `epmfs`, folder `/mnt/pool/Downloads` harus sudah ada di minimal satu disk:
  ```bash
  sudo mkdir -p /mnt/disk1/Downloads
  sudo chown johan:johan /mnt/disk1/Downloads
  ```

## Cek dan restart
```bash
testparm
sudo systemctl restart smbd
sudo systemctl enable smbd
```

## Firewall (ufw)
```bash
sudo ufw allow samba
```

## Akses dari klien
- Windows: `\\IP-SERVER\Johan` atau `\\IP-SERVER\Downloads`
- Linux: `smb://IP-SERVER/Johan`
- Windows nggak bisa pakai dua user berbeda ke server yang sama. Putuskan koneksi lama dulu: `net use * /delete`

## Pastikan pool sudah ter-mount sebelum smbd start
```bash
sudo systemctl edit smbd
```
```ini
[Unit]
RequiresMountsFor=/mnt/pool
```
