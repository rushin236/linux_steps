# Switching from GRUB to systemd-boot on Arch Linux

This guide outlines steps to remove **GRUB** and install **systemd-boot** as
your system bootloader on a UEFI system running **Arch Linux**.

> Watch out:
>
> - This is intended **ONLY for UEFI systems**.
> - Make sure `/boot` is mounted to the EFI System Partition (ESP).
> - **Backup important data** on your system before doing anything.

---

## Prerequisites

- Running **Arch Linux** in **UEFI mode**
- EFI System Partition (ESP) mounted to `/boot`
- Root privileges
- `bootctl` available (`systemd` package includes this)

---

## Step-by-step Instructions

### 1. Confirm UEFI and ESP Mount

Make sure you're running in UEFI mode:

```bash
ls /sys/firmware/efi/efivars
```

Check if your ESP (`EFI System Partition`) is mounted at `/boot`:

```bash
findmnt /boot
```

If it's not, you'll need to mount it there before proceeding.

---

### 2. Install `systemd-boot`

Install systemd-boot to the EFI system partition:

```bash
bootctl install
```

This installs `systemd-boot` to your ESP under `/boot/EFI/systemd`.

---

### 3. (Optional) Remove GRUB

If you're sure everything is working and want to clean up:

```bash
sudo pacman -Rns grub
```

You might also want to remove any existing GRUB entries from the firmware:

```bash
efibootmgr
```

Then remove the GRUB entry, replacing `xxxx` with the boot number:

```bash
efibootmgr -b xxxx -B
```

---

### 4 Create Loader and Entries

#### 4.1 Create `/boot/loader/loader.conf`

```bash
sudo tee /boot/loader/loader.conf > /dev/null <<EOF
default arch
timeout 5
console-mode max
editor no
EOF
```

#### 4.2 Create a Boot Entry: `/boot/loader/entries/arch.conf`

```bash
sudo tee /boot/loader/entries/arch.conf > /dev/null <<EOF
title   Arch Linux
linux   /vmlinuz-linux
initrd  /initramfs-linux.img
options root=UUID=$(blkid -s UUID -o value /dev/sdXn) rw
EOF
```

> 🔁 Replace `/dev/sdXn` with the root partition (e.g., `/dev/nvme0n1p3`)

You can find it with:

```bash
lsblk
```

Or use:

```bash
findmnt /
```

Then get the UUID:

```bash
blkid /dev/sdXn
```

---

### 5. If using mkinitcpio/mkinitrd for kernel

Ensure your kernel and initramfs are located under `/boot` (not `/boot/efi` or
elsewhere), or adjust paths accordingly.

---

### 6. Reboot and Test

Reboot your system:

```bash
sudo reboot
```

Enter the firmware (UEFI setup) if needed, and choose `systemd-boot`
(or whatever entry bootctl installed).

---

## Updating systemd-boot (Optional)

To update systemd-boot in the future:

```bash
bootctl update
```

---

## Managing Boot Entries with `efibootmgr`

While `systemd-boot` manages its entries inside `/boot/loader/entries/`,
you can also directly interact with UEFI firmware entries using **`efibootmgr`**.

### Install efibootmgr

```bash
sudo pacman -S efibootmgr
```

### List current UEFI entries

```bash
sudo efibootmgr
```

This shows all boot entries and the current boot order. Example output:

```txt
BootCurrent: 0002
Timeout: 2 seconds
BootOrder: 0002,0001,0000
Boot0000* Windows Boot Manager
Boot0001* UEFI: USB Device
Boot0002* Linux Boot Manager
```

### Change Boot Order

Set Linux (`0002`) to boot first:

```bash
sudo efibootmgr -o 0002,0000,0001
```

### Add a New Entry (Example)

Add a custom entry pointing to `systemd-boot`:

```bash
sudo efibootmgr --create --disk /dev/nvme0n1 --part 1 \
  --label "systemd-boot" --loader '\EFI\systemd\systemd-bootx64.efi'
```

> Replace `/dev/nvme0n1` and `--part 1` with your EFI disk and partition.

### ❌ Delete an Entry

Remove entry `0000`:

```bash
sudo efibootmgr -b 0000 -B
```

### Change BootNext (One-time Boot)

Boot into Windows (entry `0000`) on next reboot only:

```bash
sudo efibootmgr -n 0000
```

---

## Notes

- `systemd-boot` entries live in `/boot/loader/entries/` → you manage them by
  editing files.

- `efibootmgr` works at the firmware level → useful for:
    - fixing boot order
    - removing old GRUB entries
    - setting one-time boot
        >
- If things break, use an Arch ISO (live USB) to recover –
  mount your system and re-run `bootctl install`.
