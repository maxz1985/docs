# Remote Desktop Rocky Linux 9
## XRDP: RDP-Style Login Access (macOS / General Use)
### Installation
```Bash
sudo dnf groupinstall -y "Server with GUI"
```
```Bash
sudo reboot
```
```Bash
sudo vi /etc/gdm/custom.conf
```
Uncomment or add
```Bash
[daemon]
WaylandEnable=false
DefaultSession=gnome-xorg.desktop
```
Save, then reboot.
```bash
sudo dnf install -y xrdp xorgxrdp
```
```Bash
sudo systemctl enable --now xrdp
```
### Firewall
```bash
sudo firewall-cmd --permanent --add-port=3389/tcp
```
```Bash
sudo firewall-cmd --reload
```
### Tell xrdp which desktop session to start
```Bash
vi ~/.xsession
```
Put the following
```Bash
exec gnome-session
```
```Bash
chmod +x ~/.xsession
```
```Bash
sudo systemctl restart xrdp
```
Connect via RDP client

## TigerVNC: HiDPI-Correct Desktop for Windows
## TigerVNC Installation
```bash
sudo dnf install -y tigervnc-server xfce4-session
```
## VNC User Configuration
### Password
```bash
vncpasswd
```
### `~/.vnc/config`
```bash
mkdir ~/.vnc
```
````Bash
vi ~/.vnc/config
````
Add
```ini
session=xfce
geometry=2560x1440
localhost=no
```
## Xfce Requires a DBus Session

Without this, systemd-launched VNC sessions **start and immediately exit**.

### `~/.vnc/xstartup`
```Bash
vi ~/.vnc/xstartup
```
Add
```sh
#!/bin/sh
unset SESSION_MANAGER
unset DBUS_SESSION_BUS_ADDRESS

exec dbus-launch --exit-with-session startxfce4 &
```
```bash
chmod +x ~/.vnc/xstartup
```
## Systemd-Managed VNC
### User → Display Mapping
```bash
sudo mkdir -p /etc/tigervnc
```
```Bash
sudo vi /etc/tigervnc/vncserver.users
```
Add
```ini
:1={your_linux_user}
```
### Enable Service
```bash
sudo systemctl enable vncserver@:1
```
```Bash
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
## Firewall Configuration
```bash
sudo firewall-cmd --permanent --add-port=5901/tcp
````
```Bash
sudo firewall-cmd --reload
```
Verify:
```bash
sudo firewall-cmd --list-ports --permanent
```
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
> 
>It is using numbered ports according to display number.
> 
> i.e. Display 1 = 5901, Display 2 = 5902 and so on.
## Appendix: Verification Commands
### XRDP
```bash
systemctl status xrdp
```
```Bash
ss -ltnp | grep 3389
```
### VNC
```Bash
systemctl status vncserver@:1
```
```Bash
ss -ltnp | grep 5901
```
```Bash
vncserver -list
```
### Session type
```Bash
echo $XDG_SESSION_TYPE
```