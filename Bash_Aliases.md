# Bash Aliases

> **Untuk:** Daftar alias `~/.bashrc` buat update, backup stack, dan monitoring NAS.

Taruh di `~/.bashrc`, lalu reload:
```bash
nano ~/.bashrc
source ~/.bashrc
```

## Pemeliharaan dan Monitoring
```bash
alias cleanup='sudo apt clean && sudo apt autoremove --purge -y && sudo journalctl --vacuum-time=2weeks'
alias cpu-watt='sudo turbostat --Summary --quiet --show PkgWatt --interval 1'
alias ct-status='docker ps --format "table {{.Names}}\t{{.Status}}"'

alias hdsentinel='sudo /home/johan/hdsentinel/HDSentinel'
alias disk-usage='iostat -dx sdc sdb 1'
alias disk-temp='for d in /dev/sda /dev/sdb /dev/sdc; do echo "== $d =="; sudo smartctl -A "$d" | grep -i temp; done'

alias qbit-ip='docker exec qbittorrent curl -s ifconfig.me'
```
- `cpu-watt` dan `disk-temp` butuh sudo tanpa password, lihat [Sudoers_Visudo](Sudoers_Visudo.md).
- `qbit-ip` buat cek IP yang keluar dari qbittorrent. Harusnya IP exit node Tailscale, bukan IP rumah.

## Update Stack
Pull image baru, recreate, hapus image lama, lalu tampilkan status:
```bash
alias update-storage='cd /home/johan/storage-stack && docker compose pull && docker compose up -d && docker image prune -f && sleep 3 && docker compose ps'
alias update-immich='cd /home/johan/immich-stack && docker compose pull && docker compose up -d && docker image prune -f && sleep 3 && docker compose ps'
alias update-system='cd /home/johan/system-stack && docker compose pull && docker compose up -d && docker image prune -f && sleep 3 && docker compose ps'
alias update-arr='cd /home/johan/arr-stack && docker compose pull && docker compose up -d && docker image prune -f && sleep 3 && docker compose ps'
alias update-music='cd /home/johan/music-stack && docker compose pull && docker compose up -d && docker image prune -f && sleep 3 && docker compose ps'
alias update-tools='cd /home/johan/tools-stack && docker compose pull && docker compose up -d && docker image prune -f && sleep 3 && docker compose ps'
alias update-all='update-system && update-arr && update-tools && update-storage && update-immich'
```

## Backup Stack
`docker compose down` dulu (stack mati selama backup), jalankan script backup, lalu `up -d` lagi:
```bash
alias backup-storage='cd /home/johan/storage-stack && docker compose down && sudo /home/johan/.scripts/backup-appstack.sh storage-stack && docker compose up -d && sleep 3 && docker compose ps'
alias backup-immich='cd /home/johan/immich-stack && docker compose down && sudo /home/johan/.scripts/backup-appstack.sh immich-stack && docker compose up -d && sleep 3 && docker compose ps'
alias backup-system='cd /home/johan/system-stack && docker compose down && sudo /home/johan/.scripts/backup-appstack.sh system-stack && docker compose up -d && sleep 3 && docker compose ps'
alias backup-arr='cd /home/johan/arr-stack && docker compose down && sudo /home/johan/.scripts/backup-appstack.sh arr-stack && docker compose up -d && sleep 3 && docker compose ps'
alias backup-music='cd /home/johan/music-stack && docker compose down && sudo /home/johan/.scripts/backup-appstack.sh music-stack && docker compose up -d && sleep 3 && docker compose ps'
alias backup-tools='cd /home/johan/tools-stack && docker compose down && sudo /home/johan/.scripts/backup-appstack.sh tools-stack && docker compose up -d && sleep 3 && docker compose ps'
alias backup-all='backup-system && backup-arr && backup-tools && backup-storage && backup-immich'
```

## Catatan
- `update-all` dan `backup-all` **tidak** include `music-stack`, jadi jalankan `update-music` atau `backup-music` manual.
- Path script di alias: `/home/johan/.scripts/backup-appstack.sh` (pakai titik). Entri sudoers harus path yang sama persis, lihat [Sudoers_Visudo](Sudoers_Visudo.md).
- Hasil backup ada di `/mnt/pool/Backup-AppStacks/nas/<tanggal>/`. Cara restore: [Docker_Restore_Stacks](../homelab-cheatsheet/Docker_Restore_Stacks.md).

## Saat Terminal Dibuka
`fastfetch` di baris terakhir `.bashrc` supaya info sistem muncul tiap buka terminal.
