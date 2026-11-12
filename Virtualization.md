# Virtualisasi

> **Untuk:** QEMU/Virt-Manager di Arch dan ganti ID container LXC di Proxmox.

## QEMU + Virt-Manager (Arch)
```bash
# 1. Install paket
sudo pacman -S qemu-full virt-manager virt-viewer dnsmasq vde2 bridge-utils \
  iptables-nft libvirt edk2-ovmf

# 2. Aktifkan service libvirt
sudo systemctl enable --now libvirtd.service
sudo systemctl status libvirtd.service

# 3. Tambah user ke grup libvirt (login ulang setelah ini)
sudo usermod -aG libvirt $USER

# 4. Cek dan aktifkan virtual network default
virsh net-list --all
virsh net-start default
virsh net-autostart default
```

### Kalau network "default" belum ada
```bash
# Generate UUID baru
uuidgen
```

Buat `/usr/share/libvirt/networks/default.xml`:
```xml
<network>
  <name>default</name>
  <uuid>GANTI-DENGAN-UUID-ASLI</uuid>
  <forward mode='nat'/>
  <bridge name='virbr0' stp='on' delay='0'/>
  <ip address='192.168.122.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='192.168.122.2' end='192.168.122.254'/>
    </dhcp>
  </ip>
</network>
```

```bash
# Daftarkan dan jalankan
sudo virsh --connect qemu:///system net-define /usr/share/libvirt/networks/default.xml
sudo virsh --connect qemu:///system net-autostart default
sudo virsh --connect qemu:///system net-start default
```

### Permission Disk VM (kalau disimpan di home directory)
```bash
# Opsi 1: chown + chmod
sudo chown libvirt-qemu:kvm /home/johan/VMs/Windows-LTSC.qcow2
sudo chmod 755 /home/johan
sudo chmod 755 /home/johan/VMs

# Opsi 2: ACL (lebih aman, tidak mengubah permission folder)
sudo setfacl -m u:libvirt-qemu:x /home/johan
sudo setfacl -m u:libvirt-qemu:rx /home/johan/VMs
sudo setfacl -m u:libvirt-qemu:rw /home/johan/VMs/Windows-LTSC.qcow2
```

## Proxmox - LXC

**Ganti ID container LXC (contoh: ID 102 → 153)**
```bash
# 1. Stop container
pct stop 102

# 2. Pindahkan file config
mv /etc/pve/lxc/102.conf /etc/pve/lxc/153.conf

# 3. Pindahkan direktori container
mv /var/lib/lxc/102 /var/lib/lxc/153

# 4. Cek nama volume yang dipakai
pct config 153

# 5. Ganti nama volume LVM
lvrename pve vm-102-disk-0 vm-153-disk-0

# 6. Update config supaya menunjuk ke volume baru
nano /etc/pve/lxc/153.conf
  -> Ubah: rootfs: local-lvm:vm-153-disk-0
```
