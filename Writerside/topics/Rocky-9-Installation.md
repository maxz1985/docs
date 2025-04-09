# Rocky 9 Installation

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
sudo dnf install git vim traceroute tcpdump bind-utils mtr htop btop fzf zsh -y
```
### Packages
* git
* vim
* traceroute
* tcpdump
* bind-utils
* mtr
* btop
* htop
* fzf
* zsh
* https://s3.amazonaws.com/ec2-downloads-windows/SSMAgent/latest/linux_amd64/amazon-ssm-agent.rpm

## Install AWS SSM Agent if required
```Shell
sudo dnf install https://s3.amazonaws.com/ec2-downloads-windows/SSMAgent/latest/linux_amd64/amazon-ssm-agent.rpm -y
```

## Enable Cockpit
```Shell
sudo systemctl enable --now cockpit.socket
```

## Install and enable XRDP
```Shell
sudo dnf install xrdp
```
```Shell
sudo firewall-cmd --permanent --add-port=3389/tcp
```
```Shell
sudo firewall-cmd --reload
```
```Shell
sudo systemctl enable --now xrdp
```
Reboot the system.

## Change shell to `zsh` for current user and root
```Shell
chsh -s $(which zsh)
```
And for root
```Shell
sudo chsh -s $(which zsh)
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
## Install ohMyZsh
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

## Enable `fzf` keybindings
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
