# K8S Installation
This document was created on _Ubuntu_

## Node preparation
>Make sure your machine (or VM) has at least **2 vCPU**
> 
{style="note"}
### Make sure `product_uuid` is unique for every node (especially important for cloned VMs)
```shell
 sudo cat /sys/class/dmi/id/product_uuid
```
```shell
034c8473-3064-4ab3-9f84-1a4ddf32bf4f
```
### Disable swap
```Shell
sudo vi /etc/fstab
```
Comment the swap line
```Shell
# /swap.img     none    swap    sw      0       0
```
```Shell
sudo swapoff -a
```
```shell
sudo rm -rf /swap.img
```
### Networking
Enable IPv4 packet forwarding and allow `iptables` to see bridged traffic
```shell
echo "net.ipv4.ip_forward=1" | sudo tee -a  /etc/sysctl.conf
```
```shell
sudo tee -a /etc/sysctl.conf << 'EOL'
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
EOL
```
```shell
sudo sysctl -p
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
```shell
sudo su
```
```Shell
rm -rf /etc/containerd/config.toml
```
```shell
containerd config default > /etc/containerd/config.toml
```
```shell
vi /etc/contaierd/config.toml
```
Change line `SystemdCgroup = false` to
```shell
SystemdCgroup = true
```
Reboot
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
## Initialize cluster on master node
> If you don't specify `--pod-network-cidr`, a random CIDR will be chosen by `kubeadm init`
> 
> To change the CIDR _after_ `kubeadm init`, you will have to _reinstall the cluster_.
> 
{style="warning"}
```Shell
sudo kubeadm init --pod-network-cidr=10.76.0.0/16
```
> Take note of the `join` command from the `init` output – you will need that exact command to join other nodes to this cluster.
> 
> `kubeadm join {control_palne_io}:6443 --token {token} --discovery-token-ca-cert-hash sha256:{ca_cert_hash}`
> 
> You can regenerate the join command at any time but running:
> ```shell
>kubeadm token create --print-join-command
>```
{style="note"}
> _K8s tokens expire after 24 hours by default_.
> 
> To check if you have active token run on master `kubeadm token list`.
> 
> If no tokens are present, regenerate the join command.
> 
{style="warning"}
### After init
Make `kubectl` work for your non-root user:
```Shell
mkdir -p $HOME/.kube
```
```Shell
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
```
```Shell
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```
## Install Calico network plug in
```Shell
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.29.0/manifests/tigera-operator.yaml
```