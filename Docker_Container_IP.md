# Docker: Cek IP Container

> **Untuk:** Cek IP dan gateway container Docker.

## Semua container
```bash
docker inspect -f '{{.Name}} - {{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' $(docker ps -aq)
```

## Gateway container
```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.Gateway}}{{end}}' NAMA_CONTAINER_ATAU_ID
```

## IP satu container
```bash
docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' NAMA_CONTAINER
```
Flag-nya `-f` atau `--format` (dua strip). `-format` satu strip bakal error.

## Catatan
- Container yang pakai `network_mode: service:...` (misal `qbittorrent` numpang `tailscale-arr`) nggak punya IP sendiri, hasilnya kosong. Cek IP container yang jadi host network-nya.
- Container yang mati juga tampil di perintah pertama (`-aq`), IP-nya kosong.
