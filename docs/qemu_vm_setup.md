# QEMU/KVM Guest Setup Guide

Make your QEMU VMs work smoothly with:

- Clipboard sharing (host ↔ guest)
- Auto-resizing display
- Graceful shutdown/reboot
- Better graphics performance

---

## Host (Linux with QEMU/KVM)

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

## Guest Setup

### Arch Linux

```bash
sudo pacman -S qemu-guest-agent spice-vdagent xf86-video-qxl
sudo systemctl enable --now qemu-guest-agent
```

### Ubuntu / Debian

```bash
sudo apt install qemu-guest-agent spice-vdagent xserver-xorg-video-qxl
sudo systemctl enable --now qemu-guest-agent
```

### Fedora / RHEL / CentOS

```bash
sudo dnf install qemu-guest-agent spice-vdagent xorg-x11-drv-qxl
sudo systemctl enable --now qemu-guest-agent
```

### openSUSE

```bash
sudo zypper install qemu-guest-agent spice-vdagent xorg-x11-driver-video
sudo systemctl enable --now qemu-guest-agent
```

---

## What You Get

- 📋 Copy & paste works between host ↔ guest
- 🖼️ VM display auto-adjusts to window size
- 🔌 Graceful shutdown/reboot from `virsh` / virt-manager
- 📡 Guest IP reporting to host
- 📂 Optional: File sharing with **virtiofs** or **9p**
