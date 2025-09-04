# LightDM Display Config Script

This is a guide to setup the lightdm display-setup-script option
using custom script that uses xrandr or autorandr. This is for
people who have multi monitor setup.

---

## Method 1. With xrandr (most easy to use and setup)

### Install requirements

```bash
sudo pacman -Syu
sudo pacman -S xorg-xrandr
```

### 🖥️ 1. Generate .xrandr.conf File Automatically

This script analyzes your current monitor layout using xrandr and creates
a .xrandr.conf file in your home directory. The file will contain an xrandr
command that can be used to restore your display setup automatically.

You can run this anytime you change your monitor layout to update the config.

```bash
#!/bin/bash

OUT="$HOME/.xrandr.conf"
>"$OUT"

XRANDR_CMD="xrandr"

while IFS= read -r line; do
  mon=$(echo "$line" | grep " connected" | awk '{print $1}')
  if [[ -n $mon ]]; then

    # Check if primary
    is_primary=false
    if [[ "$line" == *primary* ]]; then
      is_primary=true
    fi

    # Get resolution and position
    res_pos=$(echo "$line" | grep -o '[0-9]\+x[0-9]\++[0-9]\++[0-9]\+')

    if [[ -n $res_pos ]]; then
      # Read the next line (mode list)
      IFS= read -r next_line
      res=$(echo "$next_line" | awk '{print $1}')

      # Determine refresh rate
      if [[ "$next_line" == *" + "* ]]; then
        rate=$(echo "$next_line" | cut -d'+' -f2 | awk '{print $1}')
      else
        rate=$(echo "$next_line" | awk -v r="$res" '$1 == r {print $2}')
      fi
      rate=${rate//\*/}

      # Extract X and Y
      x_pos=$(echo "$res_pos" | cut -d'+' -f2)
      y_pos=$(echo "$res_pos" | cut -d'+' -f3)

      # Append monitor to xrandr command
      XRANDR_CMD+=" --output $mon --mode $res --rate $rate --pos ${x_pos}x${y_pos}"
      $is_primary && XRANDR_CMD+=" --primary"
    else
      # If disconnected or ignored, turn off
      XRANDR_CMD+=" --output $mon --off"
    fi
  fi
done < <(xrandr -q)

# Save to file
echo "$XRANDR_CMD" >"$OUT"

echo "Saved xrandr command to $OUT"
```

### 2. Add following command in your i3wm config or startup scripts

```bash
exec_always --no-startup-id "~/.config/i3/scripts/xrandr_command_generator.sh"
```

You can run the .xrandr.conf file automatically when your window manager (WM) starts.
This file is created by the script above and contains xrandr commands to set up your
display.

To make sure it runs every time you log in, add the following line to your WM's
startup file.

For example, if you're using i3wm, add this line to your ~/.config/i3/config:

```bash
exec --no-startup-id bash "$HOME/.xrandr.conf"
```

This will run the display setup commands at startup.

<!-- ### 🛠️ 3. Configure LightDM -->

<!-- markdownlint-disable MD033 -->
<h3 id="configure-lightdm">🛠️ 3. Configure LightDM</h3>
<!-- markdownlint-enable MD033 -->

To run your display setup script automatically when LightDM starts, follow
these steps:

#### 🔍 Step 1: Find the LightDM config file

Use this command to locate `lightdm.conf`:

```bash
sudo ls -al /etc/lightdm | grep 'lightdm.conf'
```

#### 📝 Step 2: Edit the config file

Open the file in a text editor like `nano`:

```bash
sudo nano /etc/lightdm/lightdm.conf
```

Find the section that looks like this:

```
[Seat:*]
```

In this section, look for the line that starts with `display-setup-script=`,
and replace or add it as follows (remember to replace `<your user name>`
with your actual username):

```bash
display-setup-script=/bin/bash -c '[ -f "/home/<your user name>/.xrandr.conf" ] && bash "/home/<your user name>/.xrandr.conf" || true'
```

✅ This checks if the `.xrandr.conf` file exists in your home directory and runs it.
If the file doesn't exist or there's an error, it still returns `true` so LightDM
won't break.

---

### ⚠️ Having issues?

If the display script isn't working, try adjusting this line in the `[LightDM]`
section of the same file:

```bash
logind-check-graphical=true
```

Try commenting it out (add `#` at the start of the line) or uncommenting it,
depending on its current state. This can help in some setups.

---

### 🧰 Troubleshooting

If LightDM isn't working as expected, check the logs for errors:

#### 🔎 View LightDM logs

```bash
sudo cat /var/log/lightdm/lightdm.log
```

Or check the previous log:

```bash
sudo cat /var/log/lightdm/lightdm.log.old
```

#### 🔎 Check X session logs

```bash
sudo cat /var/log/lightdm/x-0.log
```

Or:

```bash
sudo cat /var/log/lightdm/x-0.log.old
```

---

## 🔧 Method 2: Using `autorandr` (Easy but needs extra setup)

### ✅ Step 1: Install `autorandr`

Run the following commands to install `autorandr`:

```bash
sudo pacman -Syu
sudo pacman -S autorandr
```

---

### ✅ Step 2: Enable and start the `autorandr` service

To use `autorandr`, enable and start it as your regular user:

```bash
systemctl --user enable autorandr
systemctl --user start autorandr
```

**Or**, if needed, enable it as root user (not common):

```bash
sudo systemctl enable autorandr
sudo systemctl start autorandr
```

---

### 🖥️ Step 3: Create an `autorandr` profile

You can save your current monitor layout into a profile like this:

```bash
autorandr -s <profile-name> --force
```

Replace `<profile-name>` with something like `home`, `office`, etc.

This saves the display setup to:  
`~/.config/autorandr/<profile-name>/config`

---

### ⚙️ Step 4: Create a systemd service to sync the profile

This will auto-copy the saved profile to system space for LightDM to use.

Create a new systemd service file:

```bash
sudo nano /etc/systemd/system/autorandrlastUsed@.service
```

Paste this into the file:

```ini
[Unit]
Description=Copy autorandr profile from %i user to LightDM dir
DefaultDependencies=no
Before=shutdown.target reboot.target halt.target
ConditionPathExists=/home/%i/.config/autorandr/<profile-name>

[Service]
Type=oneshot
RemainAfterExit=yes
ExecStart=/bin/true
ExecStop=/usr/local/bin/copy-lastused.sh %i
TimeoutStopSec=10

[Install]
WantedBy=multi-user.target
```

🔁 Replace `<profile-name>` with your saved profile name.

---

### 📝 Step 5: Create the `copy-lastused.sh` script

This script copies your saved profile to `/etc/xdg/autorandr/`.

Create the script:

```bash
sudo nano /usr/local/bin/copy-lastused.sh
```

Paste this into the file:

```bash
#!/bin/bash

USER="$1"
USER_HOME="/home/$USER"
LASTUSED="$USER_HOME/.config/autorandr/<profile-name>"
DEST_DIR="/etc/xdg/autorandr/"
LOG_FILE="/var/log/custom/monitors-copy.log"
: >"$LOG_FILE"

log() {
  echo "[$(date '+%Y-%m-%d %H:%M:%S')] $1" >>"$LOG_FILE"
  # logger -t copy-lastused "$1"  # Optional system log
}

log "Running copy for: $USER"
log "Checking at: $LASTUSED"

if [[ -d "$LASTUSED" ]]; then
  mkdir -p "$DEST_DIR"
  if cp -r "$LASTUSED" "$DEST_DIR"; then
    log "Copied: $LASTUSED -> $DEST_DIR"
  else
    log "ERROR: Failed to copy"
  fi
else
  log "WARNING: Profile not found at: $LASTUSED"
fi
```

> 📂 Make sure the folder `/etc/xdg/autorandr/` exists, or make one:
>
> ```bash
> sudo mkdir -r /etc/xdg/autorandr/
> ```

#### Also

> 📂 Make sure the folder `/var/log/custom/` exists, or change this line:
>
> ```bash
> LOG_FILE="/var/log/custom/monitors-copy.log"
> ```
>
> To:
>
> ```bash
> LOG_FILE="/var/log/monitors-copy.log"
> ```

Make the script executable:

```bash
chmod +x /usr/local/bin/copy-lastused.sh
```

---

### ⚙️ Step 6: Add autorandr to LightDM

In your LightDM config file at `/etc/lightdm/lightdm.conf`,  
find the line under `[Seat:*]` that starts with `display-setup-script`.

Replace or add this line:

```bash
display-setup-script=/bin/bash -c 'autorandr --load <profile-name> || true'
```

🔁 Replace `<profile-name>` with the name you saved earlier.

---

### 📝 Optional: Link to LightDM setup

See the setup steps for LightDM:
[Go to LightDM config section️](#configure-lightdm)

---
