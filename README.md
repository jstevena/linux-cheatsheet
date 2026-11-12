# Linux Cheatsheet

Catatan Linux yang berlaku umum (Arch, Debian, Fedora): paket, storage, share, SSH, dan Docker dasar. Catatan khusus lab ada di [homelab-cheatsheet](../homelab-cheatsheet/README.md).

## Sistem dan Paket
- [Package_Management](Package_Management.md) - Install, update, dan bersihin paket per distro (Arch, Debian, Fedora) plus Flatpak.
- [System_Setup](System_Setup.md) - Setup awal sistem: timezone, user, shell, display manager, update otomatis, dan konfigurasi desktop.
- [Quick_Reference](Quick_Reference.md) - Cheatsheet cepat: Vim, command Linux umum, dan path penting.
- [Bash_Aliases](Bash_Aliases.md) - Daftar alias `~/.bashrc` buat update, backup stack, dan monitoring NAS.
- [Sudoers_Visudo](Sudoers_Visudo.md) - Sudo tanpa password untuk perintah tertentu (smartctl, backup, poweroff, turbostat).
- [Login_Message_MOTD](Login_Message_MOTD.md) - Sembunyiin pesan MOTD saat login SSH.
- [Desktop_and_Apps](Desktop_and_Apps.md) - Printer Epson dan fix Brave/keyring di Arch + Hyprland.

## Storage dan Share
- [Storage_and_Disk](Storage_and_Disk.md) - Format dan cek HDD, passthrough disk ke VM Proxmox, dan mount cloud storage (rclone).
- [Mergerfs_Storage_Pool](Mergerfs_Storage_Pool.md) - Gabungin beberapa HDD jadi satu pool (mergerfs) dengan aturan tulis `epmfs`.
- [Samba_Share](Samba_Share.md) - Setup network share Samba (`Downloads` dan `Johan`) di atas pool mergerfs.
- [Permissions](Permissions.md) - Ownership, chmod, ACL, dan checklist troubleshooting permission (Samba dan container).

## Network dan Akses
- [Networking_and_Firewall](Networking_and_Firewall.md) - Rule UFW per aplikasi di PC desktop (Samba, KDE Connect, VNC, Sunshine) dan setup DNS over HTTPS.
- [SSH_and_User_Management](SSH_and_User_Management.md) - Generate dan pasang SSH key, konfigurasi SSH, dan bikin user baru.
- [Wake_on_LAN](Wake_on_LAN.md) - Aktifkan Wake-on-LAN di NIC dan bikin persisten lewat systemd.
- [Ignore_Ping](Ignore_Ping.md) - Bikin mesin Linux nggak membalas ping (ICMP echo).

## Docker dan Virtualisasi
- [Docker_Install](Docker_Install.md) - Install Docker Engine + Compose dari repo resmi di Debian.
- [Docker_Container_IP](Docker_Container_IP.md) - Cek IP dan gateway container Docker.
- [Virtualization](Virtualization.md) - QEMU/Virt-Manager di Arch dan ganti ID container LXC di Proxmox.
