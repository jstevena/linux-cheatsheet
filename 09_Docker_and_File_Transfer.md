# Docker & File Transfer

## Install Docker (Debian)
```bash
sudo apt update
sudo apt install ca-certificates curl

sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
```

Create `/etc/apt/sources.list.d/docker.sources`:
```ini
Types: deb
URIs: https://download.docker.com/linux/debian
Suites: $(. /etc/os-release && echo "$VERSION_CODENAME")
Components: stable
Signed-By: /etc/apt/keyrings/docker.asc
```

```bash
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-compose-plugin

sudo usermod -aG docker johan
```

## Restore Docker Stack from Backup
```bash
sudo tar -xzvf /mnt/pool/Backup-AppStacks/arr-stack-backup-20260724.tar.gz -C /home/johan/arr-stack
```

## Copy .tar.gz to Windows
```powershell
cd ~
tar -czvf vps-stack.tar.gz vps-stack/
scp -P 6969 johan@178.83.181.232:/home/johan/vps-stack.tar.gz C:\Users\johan\Desktop\
```

## Copy .tar.gz from Windows (Restore to Server)
```bash
# 1. Copy file from Windows to server
scp -P 6969 "C:\Users\johan\Desktop\Memek.tar.gz" johan@172.16.40.2:/home/johan/

# 2. Buat folder core-stack dan ekstrak file backup ke dalamnya
mkdir -p core-stack && tar -zxvf Memek.tar.gz -C core-stack

# 3. Pindahkan isi folder yang bersarang ke dalam direktori core-stack utama
cd core-stack
mv core-stack/* .
rmdir core-stack

# 4. Verifikasi isi file (pastikan ada appdata dan docker-compose.yml)
ls -la

cd /home/johan/core-stack
docker compose up -d
```
