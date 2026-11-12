# Install Docker (Debian)

> **Untuk:** Install Docker Engine + Compose dari repo resmi di Debian.

```bash
sudo apt update
sudo apt install ca-certificates curl

sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/debian/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/debian
Suites: $(. /etc/os-release && echo "$VERSION_CODENAME")
Components: stable
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo usermod -aG docker johan
```

## Catatan
- File `.sources` **harus dibuat lewat `tee <<EOF`**. Kalau ditulis manual pakai nano, `$(...)` nggak dieksekusi dan apt error. Alternatif manual: tulis langsung `Suites: trixie`.
- Grup `docker` baru aktif setelah logout/login (atau `newgrp docker`).
- User di grup `docker` efektif punya akses root, jangan masukin user yang nggak dipercaya.

## Cek
```bash
sudo systemctl status docker
docker run --rm hello-world
docker compose version
```
