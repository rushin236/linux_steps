# How to connect to Raspberry Pi via SSH

1. **Using arp-scan**:

    First install Tiger VPN

    ```bash
    sudo pacman -S tightvnc
    ```

    Then install the arp-scan tool

    ```bash
    sudo pacman -S arp-scan   # install if needed
    sudo arp-scan --local
    ```

    This will show all devices with their MAC addresses. Raspberry Pi MAC
    addresses typically start with:
    - `B8:27:EB` (older models)
    - `DC:A6:32` or `E4:5F:01` (newer models)
    - Now in the list you will see Raspberry Pi's ip address.
    - Open tiger VPN and enter the ip address, then enter your user and password.

- You are good to go.
