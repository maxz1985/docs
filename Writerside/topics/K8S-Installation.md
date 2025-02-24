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
### Load required Kernel modules
`overlay` module provides support for `OverlayFS`, which is a filesystem used by container runtimes to layer the
container’s root filesystem over the host filesystem.

`br_netfilter` module provides support for packet filtering in Linux bridge networks based on various criteria,
such as source and destination IP address, port numbers, and protocol type.
```Shell
echo 'overlay
br_netfilter' | sudo tee /etc/modules-load.d/kubernetes.conf
```
```Shell
sudo modprobe overlay
```
```Shell
sudo modprobe br_netfilter
```
Check
```Shell
sudo lsmod | grep -E "overlay|br_netfilter"
```
```Shell
br_netfilter           32768  0
bridge                421888  1 br_netfilter
overlay               212992  0
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
## Install container runtime (`containerd` (Docker))
### Remove default docker packages
```shell
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```
### Install Docker's apt repository
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
> Since `Kubernetes` `v1.24` `Docker` support is fully removed in favor of `containerd`.
> 
> Some documentation online claims that currently installing _ALL_ Docker packages is _not required_.
> 
> The only package needed is `containerd.io`, the line below should be 
> `sudo apt-get install containerd.io -y`, and the rest of docker-related instructions could be skipped.
> 
> I have not tested this, so until I do, this guide will recommend installing full docker as stated in the current K8s documentation.
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
vi /etc/containerd/config.toml
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
Install tigera operator
```Shell
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.29.0/manifests/tigera-operator.yaml
```
Download custom resources manifest
```Shell
wget https://raw.githubusercontent.com/projectcalico/calico/v3.29.0/manifests/custom-resources.yaml
```
Rename `custom-resources.yaml` to `calico-custom-resources.yaml`

Change `cidr: 192.168.0.0/16` to match the CIDR you used in `kubeadm init`
```Shell
vi calico-custom-resources.yaml
```
```Shell
cidr: 10.76.0.0/16
```
Install `Calico` by applying custom resources manifest 
```Shell
kubectl create -f calico-custom-resources.yaml
```
Check that all containers are in the `Running` status. Especially all calico and coredns
```Shell
kubectl get pods --all-namespaces
```
```Shell
NAMESPACE          NAME                                       READY   STATUS    RESTARTS   AGE
calico-apiserver   calico-apiserver-7f495874df-7z7th          1/1     Running   0          76s
calico-apiserver   calico-apiserver-7f495874df-qlrpw          1/1     Running   0          76s
calico-system      calico-kube-controllers-7fb658ff7f-jdj24   1/1     Running   0          74s
calico-system      calico-node-vk7nj                          1/1     Running   0          75s
calico-system      calico-typha-6bff78b46-gzt52               1/1     Running   0          75s
calico-system      csi-node-driver-lr75v                      2/2     Running   0          75s
kube-system        coredns-7c65d6cfc9-mkwhj                   1/1     Running   0          21m
kube-system        coredns-7c65d6cfc9-rt7xl                   1/1     Running   0          21m
kube-system        etcd-k8smaster                             1/1     Running   0          21m
kube-system        kube-apiserver-k8smaster                   1/1     Running   0          21m
kube-system        kube-controller-manager-k8smaster          1/1     Running   0          21m
kube-system        kube-proxy-h5ks9                           1/1     Running   0          21m
kube-system        kube-scheduler-k8smaster                   1/1     Running   0          21m
tigera-operator    tigera-operator-f8bc97d4c-g8tg2            1/1     Running   0          12m
```
## Check that your node is now ready
```Shell
kubectl get nodes -o wide
```
```Shell
NAME        STATUS   ROLES           AGE   VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION     CONTAINER-RUNTIME
k8smaster   Ready    control-plane   28m   v1.31.2   10.74.8.12    <none>        Ubuntu 24.04.1 LTS   6.8.0-48-generic   containerd://1.7.23
```
## Install `calicoctl`
```Shell
cd /usr/local/bin
```
```Shell
sudo curl -L https://github.com/projectcalico/calico/releases/download/v3.29.0/calicoctl-linux-amd64 -o calicoctl
```
```Shell
sudo chmod +x ./calicoctl
```
## Node install and add
### Prepare the node by performing the following steps on each node
1. [](#node-preparation)
2. [](#install-container-runtime-containerd-docker)
3. [](#enable-cri-for-containerd)
4. [](#install-kubeadm)
### Join worker to the cluster
**ON MASTER**, generate the join command
```Shell
kubeadm token create --print-join-command
```
Copy the join command **onto the node** and run it

(do not use the example command below, it is specific to each cluster)
```Shell
sudo kubeadm join 1.1.1.1:6443 --token cc8t23.4f8qxn4xao21lx66 --discovery-token-ca-cert-hash sha256:d4175f1e927b33e03e0cc3812deaa96f171ed652da2249d6985818a69b81dddd
```
After completion list nodes **on master** - the new node should be ready
```Shell
kubectl get nodes
```
```Shell
NAME          STATUS   ROLES           AGE   VERSION
k8smaster     Ready    control-plane   83m   v1.31.2
k8sworker01   Ready    <none>          36s   v1.31.
```