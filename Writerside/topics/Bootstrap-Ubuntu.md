# Bootstrap Ubuntu

## Packages
```shell
sudo apt install git openssh-server fzf -y
``` 
## Start and configure `OpenSSH`
```shell
sudo systemctl enable ssh
```
From another machine, copy your public key to Ubuntu for password-less ssh access.
```shell
ssh-copy-id -i {your_ssh_public_key}.pub {ubuntu_box_username}@{ubuntu_box_ip_address}
```
## Set hostname
```shell
sudo hostnamectl set-hostname {new_hostname}
```
## Set Static IP in Netplan
```shell
cd /etc/netplan/
```
```shell
sudo vim 50-cloud-init.yaml
```
Make file look like the following:
```yaml
network:
    ethernets:
        eth0:
            dhcp4: no
            addresses:
              - {ip_address}/{netmask}
            routes:
              - to: default
                via: {default_gateway_ip}
            nameservers:
              addresses: [{dns1_ip},{dns2_ip}]
    version: 2
```
To test/verify the updated network configuration run:
```shell
sudo netplan try
```
## Copy and install a `Nerd Font`
```shell
git clone --filter=blob:none --sparse https://github.com/ryanoasis/nerd-fonts.git
```
```shell
cd ~/nerd-fonts/
```
```shell
git sparse-checkout add patched-fonts/Meslo
```
```shell
./install.sh Meslo
```
## Install and activate `Oh My Bash`
```shell
bash -c "$(wget https://raw.githubusercontent.com/ohmybash/oh-my-bash/master/tools/install.sh -O -)"
```
```shell
vim .bashrc
```
Set the following ohMyBash Theme in the file:
```shell
OSH_THEME="powerbash10k"
```
## Configure `FZF` Keybindings
```shell
vi .bashrc
```
Add the following line at the end and save
```bash
# Set up fzf key bindings and fuzzy completion
eval "$(fzf --bash)"
```

Finally, restart your terminal. Make sure bash theme loaded ok and Nerd Font Icons are rendered correctly.

Try `CTRL` + `r` to make sure it gives you a browsable list of recent commands.