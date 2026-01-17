# Remote Desktop Rocky Linux 9

## An approach using XRDP, TigerVNC, and GNOME Wayland RDP
## Summary

This document describes a **working, reboot-safe remote desktop architecture for Rocky Linux 9** that balances:

* **Login-manager–based access** (VNC and XRDP)
* **High-DPI usability from Windows** (VNC)
* **Auto-resize from Windows** (VNC)
* **Cross-platform client compatibility** 
* **Future-facing experimentation with Wayland RDP**

The final design intentionally uses **multiple remote desktop technologies**, each aligned to what it does best:

| Client OS             | Protocol | Server            | Purpose                      |
|-----------------------|----------|-------------------|------------------------------|
| macOS                 | RDP      | `xrdp`            | Login-manager RDP experience |
| Windows (HiDPI)       | VNC      | TigerVNC + Xfce   | Correct scaling + resize     |
| Experimental / Future | RDP      | GNOME Wayland RDP | Native RDP, console-attached |

This is **not redundancy** — it is **layered capability**.

## Goals and Constraints

### Goals

* Connect to a **login screen**, not a pre-existing desktop
* Support **headless access**
* Maintain **reboot persistence**
* Provide **usable HiDPI experience** on Windows
* Avoid fragile or experimental components for primary access

### Constraints

* Rocky Linux 9 (RHEL 9 family)
* Physical host
* Multiple client operating systems
* Wayland is desirable, but not yet a full RDP login replacement

## Architectural Overview

### Why Not One Tool?

Linux desktop remoting is currently split across **three architectural models**:

| Model               | Example           | Characteristics                        |
| ------------------- | ----------------- | -------------------------------------- |
| Session-based login | `xrdp`            | Headless, login manager, multi-session |
| Virtual display     | TigerVNC          | Stable, scalable, HiDPI-friendly       |
| Console attachment  | GNOME Wayland RDP | Native RDP, no login manager           |

No single tool satisfies all requirements today.

## XRDP: RDP-Style Login Access (macOS / General Use)

### Purpose

* True **RDP-like experience**
* Connect → login screen → desktop
* Independent of physical console

### Characteristics

* Uses Xorg (`xorgxrdp`)
* Creates independent user sessions
* Stable and mature
* Windows RDP Client does not support HiDPI and dynamic resize

### Installation

```bash
sudo dnf install -y xrdp xorgxrdp
sudo systemctl enable --now xrdp
```

### Firewall

```bash
sudo firewall-cmd --permanent --add-port=3389/tcp
sudo firewall-cmd --reload
```

---

## TigerVNC: HiDPI-Correct Desktop for Windows

### Why VNC?

Windows RDP clients **do not handle Linux HiDPI or dynamic resize correctly** with XRDP.
TigerVNC does.

### Desktop Choice: Xfce

* Lightweight
* Predictable under VNC
* No Wayland/Xorg ambiguity

---

## TigerVNC Installation

```bash
sudo dnf install -y tigervnc-server xfce4-session
```

---

## VNC User Configuration

### Password

```bash
vncpasswd
```

### `~/.vnc/config`

```ini
session=xfce
geometry=2560x1440
localhost=no
```

---

## **Critical Fix**: Xfce Requires a DBus Session

Without this, systemd-launched VNC sessions **start and immediately exit**.

### `~/.vnc/xstartup`

```sh
#!/bin/sh
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS

exec dbus-launch --exit-with-session startxfce4 &
```

```bash
chmod +x ~/.vnc/xstartup
```

This line is **required** for Rocky/RHEL 9.

---

## Systemd-Managed VNC (Reboot-Safe)

### User → Display Mapping

```bash
sudo mkdir -p /etc/tigervnc
sudo nano /etc/tigervnc/vncserver.users
```

```ini
:1= {your_linux_user}/.
```

### Enable Service

```bash
sudo systemctl enable vncserver@:1
sudo systemctl start vncserver@:1
```

### Important Note (Expected Behavior)

After startup:

```bash
systemctl status vncserver@:1
```

Will show:

```
inactive (dead)
```

This is **normal**.
The service **launches the session and exits**; the actual process is `Xvnc`.

Verify with:

```bash
ss -ltnp | grep 5901
```

---

## Firewall Configuration (Persistent)

```bash
sudo firewall-cmd --permanent --add-port=5901/tcp
sudo firewall-cmd --reload
```

Verify:

```bash
sudo firewall-cmd --list-ports --permanent
```

---

## Client Usage

### Windows (TigerVNC Viewer)

Connect using **display syntax**:

```
hostname:1
```

or

```
hostname:5901
```

> VNC does **not** have a single fixed port like RDP.

### macOS

Use standard RDP client → `xrdp`

---

## GNOME Wayland RDP (Experimental / Future)

### What It Is

* Native RDP implemented **inside the Wayland compositor**
* No xrdp
* No VNC
* Correct HiDPI and resize

### What It Is Not

* Not a login manager
* Not headless
* Not multi-user

### Requirements

* GNOME
* Wayland session
* User already logged into console

Verify:

```bash
echo $XDG_SESSION_TYPE
```

Expected:

```
wayland
```

### Enable

```
Settings → Sharing → Remote Desktop → RDP
```

### **Port Conflict Warning**

GNOME RDP and XRDP both use **TCP 3389**.

**They must not run simultaneously.**

Recommended workflow:

```bash
sudo systemctl stop xrdp
# test Wayland RDP
sudo systemctl start xrdp
```

---

## Final Architecture (Recommended)

| Scenario                  | Solution          |
| ------------------------- | ----------------- |
| Headless remote login     | XRDP              |
| Windows HiDPI desktop     | TigerVNC + Xfce   |
| Native RDP future testing | GNOME Wayland RDP |

This provides:

* Stability
* Recoverability
* Correct scaling
* Forward compatibility

---

## Key Takeaways

* **Login-manager support is the defining feature of RDP-style access**
* Wayland RDP is architecturally correct, but **not a replacement yet**
* XRDP + VNC remains the most reliable Linux desktop strategy today
* Rocky/RHEL 9 requires **explicit DBus handling** for systemd-launched VNC sessions

---

## Appendix: Verification Commands

```bash
# XRDP
systemctl status xrdp
ss -ltnp | grep 3389

# VNC
systemctl status vncserver@:1
ss -ltnp | grep 5901
vncserver -list

# Session type
echo $XDG_SESSION_TYPE
```

