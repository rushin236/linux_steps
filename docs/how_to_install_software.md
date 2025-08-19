# ✅ How to Install Software on Arch Linux

This guide covers the most common ways to install software on an
Arch Linux system — using both official repositories and the AUR (Arch User Repository).
Whether you're setting up from scratch or adding missing utilities,
you’ll find step-by-step instructions here.

---

## 🏬 1. Using Pacman (Official Repositories)

Arch Linux ships with `pacman` as the default package manager.

### 🔍 1.1 Search for a Package

```bash
pacman -Ss <package_name>
```

Example:

```bash
pacman -Ss firefox
```

### 📥 1.2 Install a Package

```bash
sudo pacman -S <package_name>
```

Example:

```bash
sudo pacman -S vlc
```

### ❌ 1.3 Remove a Package

```bash
sudo pacman -R <package_name>
```

Remove a package with its dependencies not used by others:

```bash
sudo pacman -Rns <package_name>
```

### 🔄 1.4 Update All Packages

```bash
sudo pacman -Syu
```

This synchronizes and updates all system packages.

### 🧹 1.5 Clean Unused Packages (orphans)

```bash
sudo pacman -Rns $(pacman -Qtdq)
```

This removes orphaned packages you no longer need.

### 🧹 1.6 Clean Cached Packages

```bash
sudo pacman -Scc
```

This removes cached packages you no longer need.

---

## 🧰 Using AUR (Arch User Repository) with yay

- ✅ [AUR setup guide](./yay_setup.md)

---

## 💡 Tips & Best Practices

- ✅ Prefer official repo packages first.
- 🔍 Use `pacman -Qi <package>` to check installed package details.
- 📦 Use `pacman -Qdt` to list orphan packages.
- 🕵️ Use `pacman -Ql <package>` to list installed files of a package.
- 📁 Use `pacman -Qo <file>` to find which package owns a file.
- ⚠️ Be cautious with AUR: read PKGBUILD before installing unknown packages.

---

## 📌 Handy Shortcuts

| Task                           | Command                                |
| ------------------------------ | -------------------------------------- |
| Update all system packages     | `sudo pacman -Syu`                     |
| Search for a package           | `pacman -Ss`                           |
| Remove a package (with config) | `sudo pacman -Rns <pkg>`               |
| Clean pacman cache             | `sudo pacman -Sc` / `sudo pacman -Scc` |
| Remove orphan packages         | `sudo pacman -Rns $(pacman -Qtdq)`     |

---

## ❓ FAQ

### 1. What's the difference between `-R`, `-Rs`, and `-Rns`?

- `-R` → Just removes the package.
- `-Rs` → Removes the package + dependencies if unneeded.
- `-Rns` → Also removes config files & leftovers (full cleanup).

### 2. What if I can't find a package?

- Use:

```bash
 pacman -Ss <keyword>
```

- Or visit [https://aur.archlinux.org](https://aur.archlinux.org)

---

## 🧠 Learn More

- 📘 [Arch Wiki - Pacman](https://wiki.archlinux.org/index.php/pacman)
- 📘 [Arch Wiki - AUR](https://wiki.archlinux.org/title/Arch_User_Repository)

---
