# Install K8s Dashboard

## Install Helm
Get the latest `Helm` binary for your system from [](https://github.com/helm/helm/releases)
```Shell
wget https://get.helm.sh/helm-v3.16.3-linux-amd64.tar.gz
```
Unpack
```Shell
tar -zxvf helm-v3.16.3-linux-amd64.tar.gz
```
Move executable  to `/usr/local/bin`
```Shell
sudo mv linux-amd64/helm /usr/local/bin/helm
```
### Install Dashboard
See the latest dashboard version here [](https://github.com/kubernetes/dashboard/releases)

Add dashboard Helm repo
```Shell
helm repo add kubernetes-dashboard https://kubernetes.github.io/dashboard/
```
Install dashboard with Helm
```Shell
helm upgrade --install kubernetes-dashboard kubernetes-dashboard/kubernetes-dashboard --create-namespace --namespace kubernetes-dashboard
```
### Expose the dashboard on the master's IP address
```Shell
kubectl edit service kubernetes-dashboard-kong-proxy -n kubernetes-dashboard
```
In the `spec: -> ports:` section add a line:
```Shell
nodePort: 30001
```
Change the line `spec: -> type:` from `ClusterIP` to:
```Shell
type: NodePort
```
Example part of the modified configuration:
```Shell
 ports:
  - name: kong-proxy-tls
    port: 443
    protocol: TCP
    targetPort: 8443
    nodePort: 30001
  selector:
    app.kubernetes.io/component: app
    app.kubernetes.io/instance: kubernetes-dashboard
    app.kubernetes.io/name: kong
  sessionAffinity: None
  type: NodePort
status:
  loadBalancer: {}
```
Change applied upon save. Verify
```Shell
kubectl get services -n kubernetes-dashboard
```
```Shell
NAME                                   TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)         AGE
kubernetes-dashboard-api               ClusterIP   10.99.137.29     <none>        8000/TCP        82m
kubernetes-dashboard-auth              ClusterIP   10.97.143.46     <none>        8000/TCP        82m
kubernetes-dashboard-kong-proxy        NodePort    10.107.219.126   <none>        443:30001/TCP   82m
kubernetes-dashboard-metrics-scraper   ClusterIP   10.96.8.203      <none>        8000/TCP        82m
kubernetes-dashboard-web               ClusterIP   10.100.50.108    <none>        8000/TCP        82m
```

### Create an admin user for dashboard
```Shell
vim kubernetes-dashboard-admin-user.yml
```
Content
```Shell
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```
Apply the document
```Shell
kubectl apply -f kubernetes-dashboard-admin-user.yml
```
Check that user is created successfully
```Shell
kubectl get serviceaccounts -n kubernetes-dashboard
```
```Shell
NAME                                   SECRETS   AGE
admin-user                             0         53s
default                                0         121m
kubernetes-dashboard-api               0         121m
kubernetes-dashboard-kong              0         121m
kubernetes-dashboard-metrics-scraper   0         121m
kubernetes-dashboard-web               0         121m
```
```Shell
kubectl create token admin-user -n kubernetes-dashboard
```

You can now access dashboard at the link below and use the token to login
````Shell
https://{master_ip}:30001
````