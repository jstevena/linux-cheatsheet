# Wake-on-LAN

> **Untuk:** Aktifkan Wake-on-LAN di NIC dan bikin persisten lewat systemd.

Aktifkan Wake-on-LAN (magic packet) di NIC supaya mesin bisa dinyalakan dari jaringan, dan tetap aktif setelah reboot. Contoh interface: `enp3s0`.

## 1. Install ethtool
```bash
sudo pacman -S ethtool        # Arch
sudo apt install ethtool      # Debian
```

## 2. Cek nama interface dan status WoL
```bash
ip link
sudo ethtool enp3s0 | grep Wake-on
```
- `Supports Wake-on: pumbg` berarti NIC support. Huruf `g` = magic packet.
- `Wake-on: d` berarti WoL **mati**. Harus jadi `g`.

## 3. Aktifkan sementara (hilang saat reboot)
```bash
sudo ethtool -s enp3s0 wol g
sudo ethtool enp3s0 | grep "Wake-on"      # konfirmasi: Wake-on: g
```

## 4. Persisten lewat systemd
`/etc/systemd/system/wol.service`:
```ini
[Unit]
Description=Enable Wake-on-LAN
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/sbin/ethtool -s enp3s0 wol g

[Install]
WantedBy=multi-user.target
```
Path binary beda per distro: Debian `/usr/sbin/ethtool`, Arch `/usr/bin/ethtool`. Cek dengan `which ethtool`.
```bash
sudo systemctl daemon-reload
sudo systemctl enable --now wol.service
sudo ethtool enp3s0 | grep Wake-on        # harus: Wake-on: g
```

## Troubleshooting
- `status=203/EXEC`: path binary di `ExecStart` salah (lihat `which ethtool`).
- `Speed: 100Mb/s` padahal NIC support 1000baseT: lihat `Link partner advertised link modes`. Kalau cuma sampai 100baseT, masalahnya di kabel (kurang pair / bukan Cat5e+), konektor, atau port switch/router.
- Kirim magic packet dari router OpenWrt: paket `etherwake`, lihat [OpenWrt_Packages](../homelab-cheatsheet/OpenWrt_Packages.md).
