# What You Can Do

Here are some options to get a better result and fix mirror-related issues:

---

## Option 1: Increase the Timeout

If you're on a slow connection or live far from Arch mirrors:

```bash
sudo reflector --latest 20 --protocol https --sort rate --timeout 10 --save /etc/pacman.d/mirrorlist
```

> Sets timeout to 10 seconds instead of 5.

---

## Option 2: Use Country-Limited Mirrors

You’ll likely get better speed and fewer timeouts by choosing mirrors close
to your location.

Example for India:

```bash
sudo reflector --country India --latest 10 --protocol https --sort rate --save /etc/pacman.d/mirrorlist
```

Or for a region (e.g. Asia):

```bash
sudo reflector --continent Asia --latest 20 --protocol https --sort rate --save /etc/pacman.d/mirrorlist
```

---

## Option 3: Skip Broken Mirrors Automatically

Reflector has logic to skip non-working mirrors automatically, but you can
further filter out broken ones with:

```bash
sudo reflector --latest 20 --protocol https --sort rate \
  --save /etc/pacman.d/mirrorlist --score 10
```

This makes it prefer mirrors that respond faster and penalizes flaky ones.

---

## If Many Mirrors Timeout

You may be facing:

- **Network issues**
- ISP restrictions
- A VPN that’s throttling traffic
- A firewall blocking access to certain mirrors

Try pinging or downloading manually to confirm.

Example:

```bash
curl -I https://archlinux.org
```
