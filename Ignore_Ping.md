# Abaikan Ping (ICMP Echo)

> **Untuk:** Bikin mesin Linux nggak membalas ping (ICMP echo).

```bash
sudo nano /etc/sysctl.conf
```
```
net.ipv4.icmp_echo_ignore_all = 1
net.ipv6.icmp.echo_ignore_all = 1
```

Terapkan tanpa reboot:
```bash
sudo sysctl -p
```

## Cek
```bash
sysctl net.ipv4.icmp_echo_ignore_all
```
Nilai `1` berarti mesin ini nggak membalas ping.

## Catatan
- Berlaku untuk **semua** ping ke mesin itu, termasuk dari monitoring (Uptime Kuma, ping check, dll.). Kalau monitoring pakai ICMP, hasilnya selalu down.
- Balikin: ubah jadi `0`, lalu `sudo sysctl -p`.
