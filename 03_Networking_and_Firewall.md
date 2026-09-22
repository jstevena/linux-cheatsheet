# Networking & Firewall

## UFW
```bash
# Enable UFW
sudo ufw enable
sudo systemctl enable --now ufw

# View rules
ufw status numbered
```

### Ports by Service
```bash
# Samba (file sharing)
ufw allow 137/udp
ufw allow 138/udp
ufw allow 139/tcp
ufw allow 445/tcp

# KDE Connect
ufw allow 1714:1764/tcp
ufw allow 1714:1764/udp

# VNC
ufw allow 5900/tcp
ufw allow 5969/tcp

# Sunshine (game streaming)
ufw allow 47984/tcp
ufw allow 47989/tcp
ufw allow 48010/tcp
ufw allow 47998/udp
ufw allow 47999/udp
ufw allow 48000/udp
ufw allow 48002/udp
ufw allow 48010/udp
```

## DNS over HTTPS (DoH)
```bash
# Install
sudo pacman -S dns-over-https

# Check if port 53 is already in use
ss -lp 'sport = :domain'
```

Configure upstream DNS - edit `/etc/dns-over-https/doh-client.conf`:
```
[[upstream.upstream_ietf]]
    url = "https://cloudflare-dns.com/dns-query"
    weight = 50
```

Point resolv.conf to localhost - edit `/etc/resolv.conf`:
```
nameserver 127.0.0.1
```

```bash
# Enable service
sudo systemctl enable --now doh-client.service
```
