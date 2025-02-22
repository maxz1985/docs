# MetalLB Load Balancer

> Cloud-managed K8s services like EKS, AKS and GKE offer the`LoadBalancer` resource you can create.
>
> However, **bare-metal K8s Clusters don't offer `LoadBalancer` by default.**
> 
> Use [MetalLB](https://metallb.io/installation/) on self-managed clusters.
> 
{style="warning"}

When creating a `LoadBalancer` resource on a self-manager K8s Cluster using something like
`kubectl expose rc myapp --type=LoadBalancer --name myapp-http`, the resource is created 
but will remain in pending state indefinitely.

Install `MetalLB` to provide `LoadBalancer` functionality for a bare-metal K8s.
> This is on a cluster using `Calico CNI`

## Create resources
```bash
kubectl apply -f https://raw.githubusercontent.com/metallb/metallb/v0.14.9/config/manifests/metallb-native.yaml
```
## Configure MetalLB with an IP range
Create a manifest file `metallb-config.yaml`:
```yaml
# MetalLB configuration for a Kubernetes cluster
# This configuration sets up MetalLB in Layer 2 mode with a specific IP address pool.
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: my-ip-pool
  namespace: metallb-system
spec:
  addresses:
    - 192.168.0.10- 192.168.0.20  # Adjust based on your network

---
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: my-advertisement
  namespace: metallb-system
```
Apply the manifest to your cluster:
```Bash
kubectl apply -f metallb-config.yaml
```
## Create `LoadBalancer` and verify
Create a load balancer (example):
```Bash
kubectl expose rc myapp --type=LoadBalancer --name myapp-http
```
Verify IP assignment
```Bash
kubectl get services
```
```bash
NAME         TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)          AGE
kubernetes   ClusterIP      10.96.0.1      <none>          443/TCP          99d
myapp-http   LoadBalancer   10.103.64.36   192.168.0.10    8080:31769/TCP   18h
```
```Bash
curl 192.168.0.10:8080
```
```Bash
You've hit myapp-k8k7q
```
