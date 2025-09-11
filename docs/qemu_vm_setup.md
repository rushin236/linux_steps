# 🖥️ QEMU/KVM Guest Setup Guide

Make your QEMU VMs work smoothly with:

- ✅ Clipboard sharing (host ↔ guest)
- ✅ Auto-resizing display
- ✅ Graceful shutdown/reboot
- ✅ Better graphics performance

---

## 🔹 Host (Linux with QEMU/KVM)

Install required packages:

```bash
# Debian/Ubuntu
sudo apt install qemu-kvm virt-manager spice-client-gtk qemu-system ovmf

# Fedora
sudo dnf install @virtualization virt-manager virt-viewer spice-gtk ovmf

# Arch
sudo pacman -S qemu virt-manager virt-viewer spice-gtk ovmf
```

When creating VM in **virt-manager**:

- Display: **SPICE**
- Video: **QXL** (X11) or **Virtio** (Wayland)
- Add: **Channel → Spice agent**
- Add: **Channel → QEMU Guest Agent**

---

## 🔹 Guest Setup

### Arch Linux

```bash
sudo pacman -S qemu-guest-agent spice-vdagent
```

### Ubuntu / Debian

```bash
sudo apt install qemu-guest-agent spice-vdagent
```

### Fedora / RHEL / CentOS

```bash
sudo dnf install qemu-guest-agent spice-vdagent
```

### openSUSE

```bash
sudo zypper install qemu-guest-agent spice-vdagent xorg-x11-driver-video
```
### Finally enable both services

```bash
sudo systemctl enable --now qemu-guest-agent
sudo systemctl enable --now spice-vdagentd
```

Now shutdown the vm and start it again, 
if it does not work just do a reboot after VM starts

---

## 🔹 What You Get

- 📋 Copy & paste works between host ↔ guest
- 🖼️ VM display auto-adjusts to window size
- 🔌 Graceful shutdown/reboot from `virsh` / virt-manager
- 📡 Guest IP reporting to host
- 📂 Optional: File sharing with **virtiofs** or **9p**
