# Rocky 9 Installation

## Install OpenSSH
Install OpenSSH
```Bash
sudo dnf install openssh-server -y
```
Enable password authentication
```Bash
 sudo vi /etc/ssh/sshd_config
```
Change `PasswordAuthentication` to `yes`
```Bash
PasswordAuthentication yes
```
Start OpenSSH service
```Bash
sudo systemctl start sshd
```
Enable OpenSSH service and get status
```Bash
sudo systemctl enable sshd
```
```Bash
sudo systemctl status sshd
```
### Set SSH sessions to never expire
```bash
sudo vi /etc/ssh/sshd_config
```
Ensure these lines exist and not commented out
```Bash
TCPKeepAlive yes
ClientAliveInterval 0
ClientAliveCountMax 0
```
Reload `sshd`
```Bash
sudo systemctl reload sshd
```

## Set Static IP address
List existing connections:
```Bash
nmcli connection show
```
```Bash
enp0s25  bbb4a89f-b419-31d0-852d-3a1efdcb7f1c  ethernet  enp0s25
lo       7853dbec-320c-41f6-b54d-91e7a03af08e  loopback  lo
```
Note the connection name  - `enp0s25`

List the connection's IP address
```Bash
ip addr show enp0s25
```
```Bash
2: enp0s25: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether xx:xx:xx:xx:xx:xx brd xx:xx:xx:xx:xx:xx
    inet 192.168.1.36/24 brd 192.168.1.255 scope global dynamic noprefixroute enp0s25
       valid_lft 85794sec preferred_lft 85794sec
    inet6 xxxx::xxxx:xxxx:xxxx:xxxx/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
```
Set the desired static IP
```Bash
sudo nmcli connection modify "enp0s25" \
  ipv4.method manual \
  ipv4.addresses 10.70.1.22/24 \
  ipv4.gateway 10.70.1.1 \
  ipv4.dns "1.1.1.1 8.8.8.8"
```
If you're connected via `ssh`
```
sudo reboot
```
If you're on a console, instead of reboot you can do
```Bash
nmcli connection down "enp0s25"
nmcli connection up "enp0s25"
```

## Copy SSH key to Rocky9
From a machine with an SSH key,
```Bash
ssh-copy-id -i ~/.ssh/{your_key_name}.pub youruser@rockyIP
```

## Passwordless sudo (for DEV or LAB workstations)
Become root
```bash
sudo -i 
```
Create `sudoers` file for your user
```Bash
visudo -f /etc/sudoers.d/{youruser}
```
Add the following line
```Bash
youruser ALL=(ALL) NOPASSWD:ALL
```
Set correct permissions
```Bash
chmod 440 /etc/sudoers.d/{youruser}
```
Exit root and test sudo from your user
```Bash
exit
```
```Bash
sudo whoami
```
It should respond with `root`.

## Package List

### Commands
Activate `epel` extras repository
```Shell
sudo dnf install epel-release -y
```
Activate CRB powertools repository
```Shell
sudo /usr/bin/crb enable
```
Refresh repository content
```Shell
sudo dnf upgrade --refresh -y
```
Install additional packages
```Shell
sudo dnf install bind-utils btop dnf-automatic fzf git htop mtr openssh-server tcpdump traceroute unzip vim -y
```

### Packages
* bind-utils
* btop
* dnf-automatic
* fzf
* git
* htop
* mtr
* openssh-server
* tcpdump
* traceroute
* unzip
* vim
* https://s3.amazonaws.com/ec2-downloads-windows/SSMAgent/latest/linux_amd64/amazon-ssm-agent.rpm

## Install AWS SSM Agent if required
```Shell
sudo dnf install https://s3.amazonaws.com/ec2-downloads-windows/SSMAgent/latest/linux_amd64/amazon-ssm-agent.rpm -y
```
## Enable Automatic Updates
```bash
sudo vi /etc/dnf/automatic.conf
```
*Update* the following settings to match
```ini
[commands]
apply_updates = yes
random_sleep = 360

[emitters]
emit_via = motd
```
Enable service
```bash
sudo systemctl enable --now dnf-automatic.timer
```
Check status
```Bash
systemctl list-timers | grep dnf
```
Verify logs
```Bash
journalctl -u dnf-automatic
```

## Install nerd fonts
Clone the repo
```Shell
 git clone --filter=blob:none --sparse https://github.com/ryanoasis/nerd-fonts.git
```
Checkout the font of your choice
```Shell
cd ~/nerd-fonts/
```
```Shell
git sparse-checkout add patched-fonts/Meslo
```
Ref: font list here [](https://github.com/ryanoasis/nerd-fonts/tree/master/patched-fonts)

Install font
```Shell
./install.sh Meslo
```

## Install and configure `oh-my-posh`
Install `oh-my-posh`
```Bash
curl -s https://ohmyposh.dev/install.sh | bash -s
```
Clone `dotfiles` repo. From your `$HOME` directory:
```Bash
git clone https://github.com/maxz1985/dotfiles.git
```
Source the `dotfiles` `bashrc`
```Bash
vi .bashrc
```
Add the following at the end of `.bashrc`
```Bash
# --- User dotfiles (cross-platform) ---
if [ -f "$HOME/dotfiles/linux/bash/.bashrc" ]; then
    # shellcheck disable=SC1091
    source "$HOME/dotfiles/linux/bash/.bashrc"
fi
```

## Enable Remote Access XRDP and VNC
[](Remote-Desktop-Rocky-Linux-9.md)

## (Optional) Enable Cockpit
```Bash
sudo dnf install -y cockpit
```
```Shell
sudo systemctl enable --now cockpit.socket
```
```Bash
systemctl status cockpit.socket
```
```Bash
sudo firewall-cmd --add-service=cockpit --permanent
```
```Bash
sudo firewall-cmd --reload
```
Cockpit listens on TCP `9090`
```Bash
https://<rocky-ip>:9090
```
Install useful modules
```Bash
sudo dnf install -y \
  cockpit-machines \
  cockpit-podman \
  cockpit-storaged \
  cockpit-packagekit \
  cockpit-networkmanager
```

## (Optional) Change shell to `zsh` for current user and root
```Shell
chsh -s $(which zsh)
```
And for root
```Shell
sudo chsh -s $(which zsh)
```

## (Legacy) Install ohMyZsh
```Shell
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```
Install PowerLevel10K Theme
```Shell
git clone https://github.com/romkatv/powerlevel10k.git $ZSH_CUSTOM/themes/powerlevel10k
```
Set the theme in `.zshrc`
```Shell
ZSH_THEME="powerlevel10k/powerlevel10k"`
```
### Make the following changes to `.p10k.zsh`
```Shell
vi .p10k.zsh
```
Comment out `user@hostname`
```Shell
# context                 # user@hostname
```
Change OS Icon to Rocky
```Shell
typeset -g POWERLEVEL9K_OS_ICON_CONTENT_EXPANSION=$'\uF32B'
```
Ref: look for icons here [](https://www.nerdfonts.com/cheat-sheet)

Set icon colors
```Shell
typeset -g POWERLEVEL9K_OS_ICON_FOREGROUND=49
typeset -g POWERLEVEL9K_OS_ICON_BACKGROUND=8
```

## (Legacy, part of `dotfiles` now) Enable `fzf` keybindings
```Shell
vi .zshrc
```
Add the following line
<tabs>
    <tab title="zsh">
        <code-block lang="bash">
            # Set up fzf key bindings and fuzzy completion
            source &lt; (fzf --zsh)
        </code-block>
    </tab>
    <tab title="bash">
        <code-block lang="bash">
            # Set up fzf key bindings and fuzzy completion
            eval "$(fzf --bash)"
        </code-block>
    </tab>  
</tabs>

```Bash
sudo ethtool -K enp0s25 tso off gso off gro off tx off rx off
```
## Updating OhMyPosh
### Windows
```Bash
winget upgrade JanDeDobbeleer.OhMyPosh
```
### macOS
```Bash
brew upgrade oh-my-posh
```
### Linux
```Bash
curl -s https://ohmyposh.dev/install.sh | bash -s -- -d ~/.local/bin
```