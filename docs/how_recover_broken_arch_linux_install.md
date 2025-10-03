# Arch Linux System Recovery Guide

This guide assumes you're using an Arch Linux live USB (e.g., archiso).

## 1. Boot into Arch Linux Live Environment

1. Insert the Arch Linux live USB
2. Reboot your system
3. Enter BIOS/UEFI and set USB as the first boot device
4. Select "Boot Arch Linux" from the boot menu

## 2. Identify Partitions

```bash
# List all disks and partitions
lsblk -f
```

Identify:

- Root partition (e.g., `/dev/sda3`)
- Boot partition (e.g., `/dev/sda1`)
- LVM/LUKS volumes (if used)

## 3. Mount Partitions

### For standard setups

```bash
# Create mount points
sudo mkdir -p /mnt /mnt/boot

# Mount partitions
sudo mount /dev/sda3 /mnt       # Root partition
sudo mount /dev/sda1 /mnt/boot  # Boot partition
```

### For LVM setups

```bash
sudo vgchange -ay <VG_name>     # Activate volume group
sudo mount /dev/<VG_name>/root /mnt
```

### For LUKS setups

```bash
sudo cryptsetup luksOpen /dev/sda3 cryptroot
sudo mount /dev/mapper/cryptroot /mnt
```

## 4. Mount Essential Filesystems

```bash
mount -o bind /dev /mnt/dev
mount -o bind /proc /mnt/proc
mount -o bind /sys /mnt/sys
mount -o bind /run /mnt/run
```

## 5. Chroot into the System

```bash
arch-chroot /mnt
```

## 6. Perform Recovery Operations

### A. Update System (if package sync issue)

```bash
pacman -Syu --noconfirm
```

### B. Regenerate initramfs

```bash
mkinitcpio -P
```

### C. Reinstall Bootloader (if needed)

#### For GRUB

```bash
grub-install /dev/sda
grub-mkconfig -o /boot/grub/grub.cfg
```

#### For systemd-boot

```bash
bootctl install
```

### D. Check fstab (backup first)

```bash
cp /etc/fstab /etc/fstab.bak
nano /etc/fstab
```

Verify partition UUIDs with `blkid`.

### E. Check for failed units

```bash
systemctl --failed
journalctl -xb
```

### F. Fix corrupted packages

```bash
pacman -Syy
pacman -Qkq | pacman -Rns --noconfirm -
pacman -S --needed --noconfirm $(pacman -Qkq)
```

## 7. Exit Chroot and Unmount

```bash
exit  # Exit chroot
```

Unmount partitions:

```bash
umount -R /mnt
```

For LUKS setups:

```bash
cryptsetup luksClose cryptroot
```

## 8. Reboot

```bash
reboot
```

## 9. Post-Recovery Checks

1. Boot into your Arch system
2. Check system status:

    ```bash
    systemctl --failed
    journalctl -xb

    ```

3. Verify all services are running:

    ```bash
    systemctl status
    ```

## Troubleshooting Tips

1. If you get "mount: /dev not found" - use `modprobe` for required filesystem modules
2. For LVM issues: `vgscan` and `vgchange -ay <VG_name>`
3. For LUKS issues: Verify password and keyslot with `cryptsetup luksDump`
4. If chroot fails: Ensure all required partitions are mounted
