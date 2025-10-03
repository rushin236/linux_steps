# Set Up WiFi from Terminal in Arch Linux

This guide explains how to connect to WiFi in Arch Linux **entirely from the terminal**,
especially useful on fresh installations, headless setups, or minimal environments.

---

## Tools You Can Use

| Tool        | Availability        | Recommended For                |
| ----------- | ------------------- | ------------------------------ |
| `iwctl`     | Default on Arch ISO | Simple connections (iwd users) |
| `nmcli`     | NetworkManager      | Full-feature WiFi & config     |
| `wifi-menu` | netctl (deprecated) | Legacy, less preferred         |

---

## 1. Using `iwctl` (iwd - Internet Wireless Daemon)

This is the recommended method during Arch installation or for minimal
setups without NetworkManager.

### Step-by-step

1. Run `iwctl`:

    ```bash
    iwctl
    ```

2. Inside the shell, find your wireless device:

    ```bash
    device list
    ```

3. Start a scan:

    ```bash
    station wlan0 scan
    ```

4. Then list networks:

    ```bash
    station wlan0 get-networks
    ```

    (Replace `wlan0` with your actual wireless interface)

5. Connect to WiFi:

    ```bash
    station wlan0 connect YourNetworkName
    ```

    You'll be prompted for the WiFi password.

6. Exit:

    ```bash
    exit
    ```

### Tip

Test with:

```bash
ping archlinux.org
```

---

## 2. Using `nmcli` (NetworkManager CLI)

Recommended **post-installation** if you’re using a desktop environment or
want persistent WiFi setup.

### First, install and enable NetworkManager

```bash
sudo pacman -S networkmanager
sudo systemctl enable NetworkManager
sudo systemctl start NetworkManager
```

### Scan & Connect

1. See your available interfaces:

    ```bash
    nmcli device
    ```

2. Scan for networks:

    ```bash
    nmcli device wifi rescan
    nmcli device wifi list
    ```

3. Connect:

    ```bash
    nmcli device wifi connect "<SSID>" password "<password>"
    ```

    You’re connected! 🎉

### Auto Connect Next Boot

Once connected, it will auto-connect every reboot unless disabled.

---

## 3. (Optional) Using `wifi-menu` (Legacy - netctl)

You **can** use `wifi-menu` if you're using **`netctl`** but it's deprecated.
Use only if nothing else is available.

### Setup Steps

1. Install `dialog`:

    ```bash
    sudo pacman -S dialog
    ```

2. Use `wifi-menu`:

    ```bash
    sudo wifi-menu
    ```

3. Select your network and enter the password.

4. Enable auto-connect (Optional):

    ```bash
    sudo netctl enable <profile_name>
    ```

> This method is outdated and not recommended for long-term usage.

---

## Verify Network Connectivity

Try pinging a website:

```bash
ping archlinux.org
```

Press `Ctrl + C` to stop the ping.

---

## Common Troubleshooting

### NetworkManager not detecting WiFi?

- Ensure services are running:

```bash
sudo systemctl start NetworkManager
sudo systemctl enable NetworkManager
```

- Reboot if needed.

---

### Wireless Interface not detected?

Check your interface list:

```bash
ip link
```

No `wlan0` or similar?

- Run:

```bash
lspci | grep -i network
```

- Or for USB adapter:

```bash
lsusb
```

Make sure the correct drivers are installed.

---

## References

- [Arch Wiki – Wireless](https://wiki.archlinux.org/title/Wireless_network_configuration)
- [Arch Wiki – iwd](https://wiki.archlinux.org/title/Iwd)
- [Arch Wiki – NetworkManager](https://wiki.archlinux.org/title/NetworkManager)

---

## TL;DR Quick Connect using `iwctl`

```bash
iwctl
device list
station wlan0 scan
station wlan0 get-networks
station wlan0 connect YourSSID
```

---
