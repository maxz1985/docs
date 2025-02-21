# K8s Upgrade Minor Version

K8s is following semantic versioning in the form of `<major>.<minor>.<patch>`

This guide describes updating K8s from one minor version to another.

When upgrading to another patch version, skip updating repositories etcd `etcd` upgrade.

In this guide, we will upgrade from `v1.31.2` to `v1.32.1`

### Upgrade control plane nodes

#### Update `apt` repositories

```Bash
sudo vi /etc/apt/sources.list.d/kubernetes.list
```
```Bash
deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /
```
Update the minor version to match what you are upgrading to - `v1.31` to `v1.32`
```Bash
deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.32/deb/ /
```
#### Update `apt` cache and get the K8s version to upgrade to
```Bash
sudo apt update
```
```Bash
sudo apt-cache madison kubeadm
```
```Bash
kubeadm | 1.32.1-1.1 | https://pkgs.k8s.io/core:/stable:/v1.32/deb  Packages
kubeadm | 1.32.0-1.1 | https://pkgs.k8s.io/core:/stable:/v1.32/deb  Packages
```
> Note the version string we are upgrading to - `1.32.1-1.1`
> 
> We will use that string when upgrading `kubeadm`
#### Upgrade `kubeadm`
```Bash
sudo apt-mark unhold kubeadm
```
```Bash
sudo apt-get update && sudo apt-get install -y kubeadm='1.32.1-1.1'
```
```Bash
sudo apt-mark hold kubeadm
```
```Bash
sudo kubeadm version
```
```Bash
kubeadm version: &version.Info{Major:"1", Minor:"32", GitVersion:"v1.32.1", GitCommit:"e9c9be4007d1664e68796af02b8978640d2c1b26", GitTreeState:"clean", BuildDate:"2025-01-15T14:39:14Z", GoVersion:"go1.23.4", Compiler:"gc", Platform:"linux/amd64"}
```
#### Call `kubeadm upgrade`
```Bash
sudo kubeadm upgrade plan
```
We are interested in the section that describes `v1.32.1` upgrade.
```Bash
_____________________________________________________________________

Components that must be upgraded manually after you have upgraded the control plane with 'kubeadm upgrade apply':
COMPONENT   NODE          CURRENT   TARGET
kubelet     k8smaster     v1.31.2   v1.32.1
kubelet     k8sworker01   v1.31.2   v1.32.1
kubelet     k8sworker02   v1.31.2   v1.32.1

Upgrade to the latest stable version:

COMPONENT                 NODE        CURRENT    TARGET
kube-apiserver            k8smaster   v1.31.2    v1.32.1
kube-controller-manager   k8smaster   v1.31.2    v1.32.1
kube-scheduler            k8smaster   v1.31.2    v1.32.1
kube-proxy                            1.31.2     v1.32.1
CoreDNS                               v1.11.3    v1.11.3
etcd                      k8smaster   3.5.15-0   3.5.16-0

You can now apply the upgrade by executing the following command:

        kubeadm upgrade apply v1.32.1

_____________________________________________________________________
```
Execute a kubeadm upgrade command
```Bash
sudo kubeadm upgrade apply v1.32.1
```
```Bash
[upgrade] SUCCESS! A control plane node of your cluster was upgraded to "v1.32.1".

[upgrade] Now please proceed with upgrading the rest of the nodes by following the right order.
```
Upgrade your CNI plug in **IF REQUIRED**.

#### Upgrade `kubelet` and `kubectl`
Drain the node
> The following command is **ALWAYS** executed on the **CONTROL PLANE node**.
>
{style="warning"}
```Bash
kubectl drain {node_name} --ignore-daemonsets
```
Upgrade the `kubelet` and `kubectl`.
```Bash
sudo apt-mark unhold kubelet kubectl
```
```Bash
sudo apt-get update && sudo apt-get install -y kubelet='1.32.1-1.1' kubectl='1.32.1-1.1'
```
```Bash
sudo apt-mark hold kubelet kubectl
```
Restart services
```Bash
sudo systemctl daemon-reload
```
```Bash
sudo systemctl restart kubelet
```
Un-cordon the node
> The above command is **ALWAYS** executed on the **CONTROL PLANE node**.
>
{style="warning"}

```Bash
kubectl uncordon {node_name}
```

### Upgrade worker nodes
1.  [](#update-apt-repositories)
2. [](#update-apt-cache-and-get-the-k8s-version-to-upgrade-to)
3. [](#upgrade-kubeadm)

#### Call `upgrade node`
```Bash
sudo kubeadm upgrade node
```
6. [](#upgrade-kubelet-and-kubectl)
> You may get the following error when running `kubectl drain` on a worker node running `kubernetes-dashbord`:
> ```Bash
> cannot delete Pods with local storage (use --delete-emptydir-data to override): kube-system/metrics-server-66966f44c6-tgq9s, kubernetes-dashboard/kubernetes-dashboard-api-7746d7db6f-lkrbt, kubernetes-dashboard/kubernetes-dashboard-auth-6cdff87986-sxt52, kubernetes-dashboard/kubernetes-dashboard-kong-57d45c4f69-4qm9s, kubernetes-dashboard/kubernetes-dashboard-metrics-scraper-df869c886-m2s48, kubernetes-dashboard/kubernetes-dashboard-web-6ccf8d967-hn7zf
> ```
> In that case, use 
> ```Bash
>kubectl drain k8sworker01 --ignore-daemonsets --delete-emptydir-data
>```
> 
{style="note"}