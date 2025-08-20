# Enabling Secure Boot on Arch Linux using sbctl

## Prerequisites

Before you begin, ensure you have the following:

- A working Arch Linux installation.
- Root or sudo privileges.
- Basic knowledge of Arch Linux and UEFI.

## Step 1: Enable Setup Mode in BIOS

1. Reboot your system and enter the UEFI firmware settings
   (usually by pressing a key like `F2`, `F10`, `Del`, or `Esc`
   during boot).

2. Enable Setup Mode in the UEFI firmware settings. This mode
   allows you to enroll new keys and is necessary for the initial
   setup.

3. Save and exit the UEFI firmware settings.

## Step 2: Install sbctl

1. Update your system:

   ```sh
   sudo pacman -Syu
   ```

2. Install `sbctl`:

   ```sh
   sudo pacman -S sbctl
   ```

## Step 3: Generate and Enroll Keys

1. Generate a new key pair:

   ```sh
   sudo sbctl create-keys
   ```

2. Enroll the keys in the UEFI firmware:

   ```sh
   sudo sbctl enroll-keys
   ```

   Or:

   ```sh
   sudo sbctl enroll-keys -m # For enrolling the micro soft vendor keys
   ```

   Follow the on-screen instructions to enroll the keys. This
   usually involves rebooting into the UEFI firmware settings and
   enrolling the keys manually.

## Step 4: Sign the Bootloader

1. Sign the bootloader (e.g., GRUB):

   Use the following command to see which all .efi files you need to sign

   ```sh
   sudo sbctl --verify
   ```

   Then sign all the keys as shown in following example

   ```sh
   sudo sbctl sign -s /boot/efi/EFI/arch/grubx64.efi
   ```

   Replace `/boot/efi/EFI/arch/grubx64.efi` with the path to your
   bootloader's EFI binary if it's different.

2. Sign the kernel:

   ```sh
   sudo sbctl sign -s /boot/vmlinuz-linux
   ```

   Replace `/boot/vmlinuz-linux` with the path to your kernel if it's
   different.

3. Sign the initramfs:

   ```sh
   sudo sbctl sign -s /boot/initramfs-linux.img
   ```

   Replace `/boot/initramfs-linux.img` with the path to your
   initramfs if it's different.

## Step 5: Enable Secure Boot

1. Reboot your system:

   ```sh
   sudo reboot
   ```

2. Enter the UEFI firmware settings (usually by pressing a key like
   `F2`, `F10`, `Del`, or `Esc` during boot).

3. Enable Secure Boot in the UEFI firmware settings.

4. Save and exit the UEFI firmware settings.

## Step 6: Verify Secure Boot

1. Reboot your system and log in.

2. Verify that Secure Boot is enabled:

   ```sh
   sudo mokutil --sb-state
   ```

   The output should indicate that Secure Boot is enabled.

3. Verify the signatures of your signed binaries:

   ```sh
   sudo sbctl verify /boot/efi/EFI/arch/grubx64.efi
   sudo sbctl verify /boot/vmlinuz-linux
   sudo sbctl verify /boot/initramfs-linux.img
   ```

   Replace the paths with the actual paths to your bootloader,
   kernel, and initramfs if they are different.

## Troubleshooting

- If you encounter issues, check the `sbctl` documentation or the
  Arch Wiki for more detailed information.
- Ensure that all signed binaries are correctly placed and that
  their paths are accurate.

## Additional Resources

- [Arch Wiki on Secure Boot](https://wiki.archlinux.org/title/Secure_Boot)
- [sbctl GitHub Repository](https://github.com/Foxboron/sbctl)

---

This guide should help you enable Secure Boot on Arch Linux using
sbctl. If you encounter any issues, refer to the additional
resources or seek help from the Arch Linux community.
