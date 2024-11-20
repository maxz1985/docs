# Install Metrics Server

## yaml manifest preparation
### Edits
```bash
wget https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml
```
Edit the `components.yaml` file to make the following adjustments.

1. Allow both Cluster Roles access to `configmaps` resources.

at `ClusterRole -> resources` add `- configmaps`

<compare>
<code-block lang="yaml">
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  labels:
    k8s-app: metrics-server
    rbac.authorization.k8s.io/aggregate-to-admin: "true"
    rbac.authorization.k8s.io/aggregate-to-edit: "true"
    rbac.authorization.k8s.io/aggregate-to-view: "true"
  name: system:aggregated-metrics-reader
rules:
- apiGroups:
  - metrics.k8s.io
  resources:
  - pods
  - nodes
  verbs:
  - get
  - list
  - watch
</code-block>
<code-block lang="yaml">
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  labels:
    k8s-app: metrics-server
    rbac.authorization.k8s.io/aggregate-to-admin: "true"
    rbac.authorization.k8s.io/aggregate-to-edit: "true"
    rbac.authorization.k8s.io/aggregate-to-view: "true"
  name: system:aggregated-metrics-reader
rules:
- apiGroups:
  - metrics.k8s.io
  resources:
  - pods
  - nodes
  - configmaps
  verbs:
  - get
  - list
  - watch
</code-block>
</compare>
<compare>
<code-block lang="yaml">
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  labels:
    k8s-app: metrics-server
  name: system:metrics-server
rules:
- apiGroups:
  - ""
  resources:
  - nodes/metrics
  verbs:
  - get
- apiGroups:
  - ""
  resources:
  - pods
  - nodes
  verbs:
  - get
  - list
  - watch
</code-block>
<code-block lang="yaml">
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  labels:
    k8s-app: metrics-server
  name: system:metrics-server
rules:
- apiGroups:
  - ""
  resources:
  - nodes/metrics
  verbs:
  - get
- apiGroups:
  - ""
  resources:
  - pods
  - nodes
  - configmaps
  verbs:
  - get
  - list
  - watch
</code-block>
</compare>

2. Metrics API service should run on the host network (not on virtual cluster network).

at `Deployment → spec → template → spec` add `hostNetwork: true`
<compare>
<code-block lang="yaml">
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    k8s-app: metrics-server
  name: metrics-server
  namespace: kube-system
spec:
  selector:
    matchLabels:
      k8s-app: metrics-server
  strategy:
    rollingUpdate:
      maxUnavailable: 0
  template:
    metadata:
      labels:
        k8s-app: metrics-server
    spec:
      containers:
</code-block>
<code-block lang="yaml">
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    k8s-app: metrics-server
  name: metrics-server
  namespace: kube-system
spec:
  selector:
    matchLabels:
      k8s-app: metrics-server
  strategy:
    rollingUpdate:
      maxUnavailable: 0
  template:
    metadata:
      labels:
        k8s-app: metrics-server
    spec:
      hostNetwork: true
      containers:
</code-block>
</compare>

3. For self-installed clusters without public CA certificate, disable SSL/TLS verification between metrics server and `kubelet`

at `Deployment → spec → template → spec → containers → args`, add `- --kubelet-insecure-tls`

<compare>
<code-block lang="yaml">
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    k8s-app: metrics-server
  name: metrics-server
  namespace: kube-system
spec:
  selector:
    matchLabels:
      k8s-app: metrics-server
  strategy:
    rollingUpdate:
      maxUnavailable: 0
  template:
    metadata:
      labels:
        k8s-app: metrics-server
    spec:
      hostNetwork: true
      containers:
      - args:
        - --cert-dir=/tmp
        - --secure-port=10250
        - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
        - --kubelet-use-node-status-port
        - --metric-resolution=15s
        image: registry.k8s.io/metrics-server/metrics-server:v0.7.2
</code-block>
<code-block lang="yaml">
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    k8s-app: metrics-server
  name: metrics-server
  namespace: kube-system
spec:
  selector:
    matchLabels:
      k8s-app: metrics-server
  strategy:
    rollingUpdate:
      maxUnavailable: 0
  template:
    metadata:
      labels:
        k8s-app: metrics-server
    spec:
      hostNetwork: true
      containers:
      - args:
        - --cert-dir=/tmp
        - --secure-port=10250
        - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
        - --kubelet-use-node-status-port
        - --metric-resolution=15s
        - --kubelet-insecure-tls
        image: registry.k8s.io/metrics-server/metrics-server:v0.7.2
</code-block>
</compare>

4. Change container port

The default manifest defines container port as `10250/tcp`. This port is already in use by the `kubelet`
```Bash
sudo ss -atlnp | grep 10250
```
```Bash
LISTEN 0      4096               *:10250            *:*    users:(("kubelet",pid=947,fd=11))
```
Find an unused port, for example, `4443` and switch the deployment to use that.
```Bash
sudo ss -atlnp | grep 4443
```
at `Deployment → spec → template → spec → containers → args` change to `--secure-port=4443` and `- containerPort: 4443`

<compare>
<code-block lang="yaml">
spec:
      hostNetwork: true
      containers:
      - args:
        - --cert-dir=/tmp
        - --secure-port=10250
        - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
        - --kubelet-use-node-status-port
        - --metric-resolution=15s
        - --kubelet-insecure-tls
        image: registry.k8s.io/metrics-server/metrics-server:v0.7.2
        imagePullPolicy: IfNotPresent
        livenessProbe:
          failureThreshold: 3
          httpGet:
            path: /livez
            port: https
            scheme: HTTPS
          periodSeconds: 10
        name: metrics-server
        ports:
        - containerPort: 10250
          name: https
          protocol: TCP
</code-block>
<code-block lang="yaml">
spec:
      hostNetwork: true
      containers:
      - args:
        - --cert-dir=/tmp
        - --secure-port=4443
        - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
        - --kubelet-use-node-status-port
        - --metric-resolution=15s
        - --kubelet-insecure-tls
        image: registry.k8s.io/metrics-server/metrics-server:v0.7.2
        imagePullPolicy: IfNotPresent
        livenessProbe:
          failureThreshold: 3
          httpGet:
            path: /livez
            port: https
            scheme: HTTPS
          periodSeconds: 10
        name: metrics-server
        ports:
        - containerPort: 4443
          name: https
          protocol: TCP
</code-block>
</compare>

### Final Diff

![install-metrics-server-01.png](install-metrics-server-01.png) {thumbnail="true"}

### Final file
```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  labels:
    k8s-app: metrics-server
  name: metrics-server
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  labels:
    k8s-app: metrics-server
    rbac.authorization.k8s.io/aggregate-to-admin: "true"
    rbac.authorization.k8s.io/aggregate-to-edit: "true"
    rbac.authorization.k8s.io/aggregate-to-view: "true"
  name: system:aggregated-metrics-reader
rules:
- apiGroups:
  - metrics.k8s.io
  resources:
  - pods
  - nodes
  - configmaps
  verbs:
  - get
  - list
  - watch
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  labels:
    k8s-app: metrics-server
  name: system:metrics-server
rules:
- apiGroups:
  - ""
  resources:
  - nodes/metrics
  verbs:
  - get
- apiGroups:
  - ""
  resources:
  - pods
  - nodes
  - configmaps
  verbs:
  - get
  - list
  - watch
---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  labels:
    k8s-app: metrics-server
  name: metrics-server-auth-reader
  namespace: kube-system
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: extension-apiserver-authentication-reader
subjects:
- kind: ServiceAccount
  name: metrics-server
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  labels:
    k8s-app: metrics-server
  name: metrics-server:system:auth-delegator
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:auth-delegator
subjects:
- kind: ServiceAccount
  name: metrics-server
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  labels:
    k8s-app: metrics-server
  name: system:metrics-server
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:metrics-server
subjects:
- kind: ServiceAccount
  name: metrics-server
  namespace: kube-system
---
apiVersion: v1
kind: Service
metadata:
  labels:
    k8s-app: metrics-server
  name: metrics-server
  namespace: kube-system
spec:
  ports:
  - name: https
    port: 443
    protocol: TCP
    targetPort: https
  selector:
    k8s-app: metrics-server
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    k8s-app: metrics-server
  name: metrics-server
  namespace: kube-system
spec:
  selector:
    matchLabels:
      k8s-app: metrics-server
  strategy:
    rollingUpdate:
      maxUnavailable: 0
  template:
    metadata:
      labels:
        k8s-app: metrics-server
    spec:
      hostNetwork: true
      containers:
      - args:
        - --cert-dir=/tmp
        - --secure-port=4443
        - --kubelet-preferred-address-types=InternalIP,ExternalIP,Hostname
        - --kubelet-use-node-status-port
        - --metric-resolution=15s
        - --kubelet-insecure-tls
        image: registry.k8s.io/metrics-server/metrics-server:v0.7.2
        imagePullPolicy: IfNotPresent
        livenessProbe:
          failureThreshold: 3
          httpGet:
            path: /livez
            port: https
            scheme: HTTPS
          periodSeconds: 10
        name: metrics-server
        ports:
          - containerPort: 4443
            name: https
            protocol: TCP
        readinessProbe:
          failureThreshold: 3
          httpGet:
            path: /readyz
            port: https
            scheme: HTTPS
          initialDelaySeconds: 20
          periodSeconds: 10
        resources:
          requests:
            cpu: 100m
            memory: 200Mi
        securityContext:
          allowPrivilegeEscalation: false
          capabilities:
            drop:
              - ALL
          readOnlyRootFilesystem: true
          runAsNonRoot: true
          runAsUser: 1000
          seccompProfile:
            type: RuntimeDefault
        volumeMounts:
          - mountPath: /tmp
            name: tmp-dir
      nodeSelector:
        kubernetes.io/os: linux
      priorityClassName: system-cluster-critical
      serviceAccountName: metrics-server
      volumes:
        - emptyDir: {}
          name: tmp-dir
---
apiVersion: apiregistration.k8s.io/v1
kind: APIService
metadata:
  labels:
    k8s-app: metrics-server
  name: v1beta1.metrics.k8s.io
spec:
  group: metrics.k8s.io
  groupPriorityMinimum: 100
  insecureSkipTLSVerify: true
  service:
    name: metrics-server
    namespace: kube-system
  version: v1beta1
  versionPriority: 100
```

## Apply manifest

```bash
kubectl apply -f components.yaml
```

## Verify installation
```Bash
kubectl get deployment metrics-server -n kube-system
```
```Bash
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
metrics-server   1/1     1            1           6h28m
```
```Bash
kubectl get pods m* -n kube-system | grep metrics
```
## Verify Metrics Server Installation
```Bash
kubectl get deployment metrics-server -n kube-system
```
```Bash
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
metrics-server   1/1     1            1           2d21h
```
```Bash
 kubectl get pods -n kube-system | grep metrics
```
```Bash
metrics-server-66966f44c6-6mp2l     1/1     Running   0               2d21h
```
```Bash
kubectl get apiservices | grep metrics
```
```Bash
v1beta1.metrics.k8s.io                 kube-system/metrics-server    True        2d21h
```
```Bash
kubectl top pods --all-namespaces
```
```Bash
NAMESPACE              NAME                                                   CPU(cores)   MEMORY(bytes)
calico-apiserver       calico-apiserver-7f495874df-7z7th                      4m           58Mi
calico-apiserver       calico-apiserver-7f495874df-qlrpw                      4m           71Mi
calico-system          calico-kube-controllers-7fb658ff7f-jdj24               3m           61Mi
calico-system          calico-node-cdgl2                                      37m          146Mi
calico-system          calico-node-ns9mb                                      29m          151Mi
calico-system          calico-node-vk7nj                                      23m          226Mi
calico-system          calico-typha-6bff78b46-gzt52                           2m           57Mi
calico-system          calico-typha-6bff78b46-mkk66                           5m           20Mi
calico-system          csi-node-driver-6q55j                                  0m           6Mi
calico-system          csi-node-driver-lr75v                                  1m           29Mi
calico-system          csi-node-driver-wln5q                                  0m           6Mi
kube-system            coredns-7c65d6cfc9-mkwhj                               2m           33Mi
kube-system            coredns-7c65d6cfc9-rt7xl                               2m           40Mi
kube-system            etcd-k8smaster                                         43m          100Mi
kube-system            kube-apiserver-k8smaster                               82m          413Mi
kube-system            kube-controller-manager-k8smaster                      26m          125Mi
kube-system            kube-proxy-h5ks9                                       2m           71Mi
kube-system            kube-proxy-l22wm                                       4m           12Mi
kube-system            kube-proxy-q44l5                                       1m           12Mi
kube-system            kube-scheduler-k8smaster                               5m           71Mi
kube-system            metrics-server-66966f44c6-6mp2l                        7m           20Mi
kubernetes-dashboard   kubernetes-dashboard-api-7746d7db6f-6sjrj              1m           17Mi
kubernetes-dashboard   kubernetes-dashboard-auth-6cdff87986-kgr7n             1m           8Mi
kubernetes-dashboard   kubernetes-dashboard-kong-57d45c4f69-vzdqz             3m           127Mi
kubernetes-dashboard   kubernetes-dashboard-metrics-scraper-df869c886-hmstr   1m           13Mi
kubernetes-dashboard   kubernetes-dashboard-web-6ccf8d967-4kj5m               1m           8Mi
tigera-operator        tigera-operator-f8bc97d4c-g8tg2                        4m           79Mi
```
```Bash
kubectl top nodes
```
```Bash
NAME          CPU(cores)   CPU%   MEMORY(bytes)   MEMORY%
k8smaster     233m         11%    3069Mi          39%
k8sworker01   106m         5%     1374Mi          35%
k8sworker02   78m          3%     1362Mi          17%
```
Check that the metrics appear in K8s Dashboard

![install-metrics-server-02.png](install-metrics-server-02.png) {thumbnail="true"}