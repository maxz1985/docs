# Configure CBL-Mariner post install

* install is done from full ISO

* during the installation minimal config was chosen

* base is PhotonOS, package manager is `yum`

## Enable paswordless sudo (console)

`sudo visudo`

* uncomment line `%wheel ALL=(ALL) NOPASSWD: ALL`

* comment (`#`) line `%wheel ALL=(ALL) ALL`

* save/exit

## Install Sshd (console)

* allow password login to sshd
  * `sudo vi /etc/ssh/ssh_config`
  * uncomment `PasswordAuthentication yes`
  * save/exit
* install sshd `sudo yum install openssh-server`
* start sshd `sudo systemctl start sshd`
* enable sshd on startup `sudo systemctl enable sshd`

## Log in via ssh

* get IP from `ifconfig`
* ssh dholland@currentIP with password

## Insert public key into authorized_keys (ssh)

* copy public key into `authorized_keys` in `~/.ssh` directory on mariner (`sudo vi ~/.ssh/authorized_keys`)
* log off ssh session and log in again, it should NOT ask for password

## Set static IP address

CBL-mariner is based on PhotonOS+redHat, setting static IP procedure is the same as in PhotonOS. Network subsystem is controlled by `systemd-networkd`

The interface config files are in `/etc/systemd/network`

On a fresh mariner install you would see a file `99-dhcp-en.network` file there. This file configures all `e*` interfaces for DHCP by default.

Create a file `sudo vi 10-static-eth0.network` with the following content:

```properties
[Match]
Name=eth0

[Network]
Address=192.168.1.2/24
Gateway=192.168.1.1
DNS=192.168.1.1
```

!**NB** - before proceeding make sure you change ownership of `10-static-eth0.network` file to `systemd-network`

```properties
sudo chown systemd-network:systemd-network 10-static-eth0.network
```

Restart network `sudo systemctl restart systemd-networkd`

You will lose ssh connection, reconnect using newly set static IP

## Install updates and reboot

* `sudo yum update -y`
* `sudo reboot`

## install git

`sudo yum install git`

## install and configure ohmyZsh

`sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"`

## Follow prompts in "Bootstrapping Ubuntu" document to finish ZSH configuration
