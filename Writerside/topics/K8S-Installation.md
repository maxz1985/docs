# K8S Installation

This document was created on _Ubuntu_
>Make sure your machine (or VM) has at least **2 vCPU**
> 
{style="note"}
## Make sure `product_uuid` is unique for every node (especially important for cloned VMs)
```shell
 sudo cat /sys/class/dmi/id/product_uuid
```
```shell
034c8473-3064-4ab3-9f84-1a4ddf32bf4f
```
## Disable swap
```Shell
sudo vi /etc/fstab
```
Comment out the swap line
```Shell
# /swap.img     none    swap    sw      0       0
```
```Shell
sudo swapoff -a
```
Reboot
## Install container runtime (`containerd` (Docker))
### Remove default docker packages
```shell
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```
### Install Docker using apt repository
Add official Docker GPG Key
```Shell
sudo apt-get install ca-certificates curl -y
```
```Shell
sudo install -m 0755 -d /etc/apt/keyrings
```
```Shell
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
```
```Shell
sudo chmod a+r /etc/apt/keyrings/docker.asc
```
Add repository to Apt sources
```Shell
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
```Shell
sudo apt-get update
```
### Install Docker Packages
```Shell
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
```
### Verify Docker installation
````Shell
sudo docker run hello-world
````
### Post-install
Run `docker` command without sudo
```Shell
sudo usermod -aG docker $USER
```
> log OUT and log back in for the change to take effect
> 
{style="note"}

Configure Docker to start on boot with systemd
```Shell
sudo systemctl enable docker.service
```
```Shell
sudo systemctl enable containerd.service
```
Enable container log rotation
```Shell
sudo vi /etc/docker/daemon.json
```
```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
```
On `Linux` Docker log files are located in
```shell
/var/lib/docker/containers/{container_id}/{container_id}-json.log
```
## Enable `CRI` for `containerd`
```Shell
sudo vim /etc/containerd/config.toml
```
Change line `disabled_plugins = ["cri"]` to
```Shell
disabled_plugins = []
```
Restart `containerd`
```Shell
sudo systemctl restart containerd
```
## Install `Kubeadm`
Install required packages
```Shell
sudo apt-get install -y apt-transport-https ca-certificates curl gpg -y
```
Download the public signing key for the Kubernetes package repositories.
```Shell
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
```
Add the appropriate Kubernetes apt repository.
```Shell
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
```
Install Kubeadm packages
```Shell
sudo apt-get update
```
```Shell
sudo apt-get install -y kubelet kubeadm kubectl -y
```
Hold `Kubeadm` package versions during regular apt updates (to prevent automatic upgrades)
```Shell
sudo apt-mark hold kubelet kubeadm kubectl
```
Enable `kubelet` before running `kubeadm`
```Shell
sudo systemctl enable --now kubelet
```