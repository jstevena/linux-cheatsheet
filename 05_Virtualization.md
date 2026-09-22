# Virtualization

## QEMU + Virt-Manager (Arch)
```bash
# 1. Install packages
sudo pacman -S qemu-full virt-manager virt-viewer dnsmasq vde2 bridge-utils \
  iptables-nft libvirt edk2-ovmf

# 2. Enable libvirt service
sudo systemctl enable --now libvirtd.service
sudo systemctl status libvirtd.service

# 3. Add user to libvirt group (re-login after this)
sudo usermod -aG libvirt $USER

# 4. Check and enable the default virtual network
virsh net-list --all
virsh net-start default
virsh net-autostart default
```

### If the "default" network doesn't exist
```bash
# Generate a new UUID
uuidgen
```

Create `/usr/share/libvirt/networks/default.xml`:
```xml
<network>
  <name>default</name>
  <uuid>REPLACE-WITH-A-REAL-UUID</uuid>
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
# Register and start
sudo virsh --connect qemu:///system net-define /usr/share/libvirt/networks/default.xml
sudo virsh --connect qemu:///system net-autostart default
sudo virsh --connect qemu:///system net-start default
```

### VM Disk Permissions (when stored in home directory)
```bash
# Option 1: chown + chmod
sudo chown libvirt-qemu:kvm /home/johan/VMs/Windows-LTSC.qcow2
sudo chmod 755 /home/johan
sudo chmod 755 /home/johan/VMs

# Option 2: ACL (safer, doesn't change folder permissions)
sudo setfacl -m u:libvirt-qemu:x /home/johan
sudo setfacl -m u:libvirt-qemu:rx /home/johan/VMs
sudo setfacl -m u:libvirt-qemu:rw /home/johan/VMs/Windows-LTSC.qcow2
```

## Proxmox - LXC

**Rename LXC container (example: ID 102 → 153)**
```bash
# 1. Stop the container
pct stop 102

# 2. Move config file
mv /etc/pve/lxc/102.conf /etc/pve/lxc/153.conf

# 3. Move container directory
mv /var/lib/lxc/102 /var/lib/lxc/153

# 4. Check volume name in use
pct config 153

# 5. Rename LVM volume
lvrename pve vm-102-disk-0 vm-153-disk-0

# 6. Update config to point to the new volume
nano /etc/pve/lxc/153.conf
  -> Change: rootfs: local-lvm:vm-153-disk-0
```
