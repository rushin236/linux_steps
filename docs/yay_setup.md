# 🧰 1. Using AUR (Arch User Repository) with yay

AUR contains community-maintained packages not found in official repos.
To install from it, you'll need an AUR helper like `yay`.

---

## ⚙️ 1.1 Install `yay` (if not installed)

### Step 1: Install prerequisites

```bash
sudo pacman -S --needed base-devel git
```

### Step 2: Clone yay Git repo

```bash
cd ~
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```

Once done, you can use `yay` like `pacman`.

---

### 🔎 1.2 Search for a Package in AUR

```bash
yay -Ss <package_name>
```

Example:

```bash
yay -Ss google-chrome
```

### 📦 1.3 Install a Package from AUR

```bash
yay -S <package_name>
```

Example:

```bash
yay -S google-chrome
```

### 🔁 1.4 Update All Packages (Including AUR)

```bash
yay -Syu
```

### 🧼 1.5 Remove a Package Installed via yay

```bash
yay -Rns <package_name>
```

### 🧹 1.6 Clean Cached Packages

```bash
yay -Scc
```

---

## 💡 Tips & Best Practices

- ✅ Prefer official repo packages first.
- 🔍 Use `yay -Qi <package>` to check installed package details.
- 📦 Use `yay -Qdt` to list orphan packages.
- 🕵️ Use `yay -Ql <package>` to list installed files of a package.
- 📁 Use `yay -Qo <file>` to find which package owns a file.
- ⚠️ Be cautious with AUR: read PKGBUILD before installing unknown packages.

---

## 📌 Handy Shortcuts

| Task                           | Command                 |
| ------------------------------ | ----------------------- |
| Update all system packages     | `yay -Syu`              |
| Search for a package           | `yay -Ss`               |
| Remove a package (with config) | `yay -Rns <pkg>`        |
| Clean yay cache                | `yay -Sc` / `yay -Scc`  |
| Remove orphan packages         | `yay -Rns $(yay -Qtdq)` |

---

## ❓ FAQ

### 1. What's the difference between `-R`, `-Rs`, and `-Rns`?

- `-R` → Just removes the package.
- `-Rs` → Removes the package + dependencies if unneeded.
- `-Rns` → Also removes config files & leftovers (full cleanup).

### 2. What if I can't find a package?

- Use:

```bash
 yay -Ss <keyword>
```

- Or visit [https://aur.archlinux.org](https://aur.archlinux.org)

---

## 🧠 Learn More

- 📘 [yay GitHub Repo](https://github.com/Jguer/yay)
- 📘 [Arch Wiki - AUR](https://wiki.archlinux.org/title/Arch_User_Repository)
