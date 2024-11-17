# Install Kubectl
## Install
Check the version of the latest K8s release at [](https://kubernetes.io/releases/download/#binaries)

<tabs>
    <tab title="macOS">
        <code-block lang="bash"> curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/darwin/amd64/kubectl"</code-block>
        <code-block lang="Bash"> chmod +x ./kubectl</code-block>
        <code-block lang="Bash"> sudo mv ./kubectl /usr/local/bin/kubectl </code-block>
        <code-block lang="Bash"> sudo chown root: /usr/local/bin/kubectl</code-block>
        <p>Install shell completion. Add the following to your <code>.zshrc</code> file:</p>
        <code-block lang="bash"> source &lt;(kubectl completion zsh)</code-block>
        <warning><emphasis>RESTART YOUR TERMINAL.</emphasis> There is a unique behavior on <code>macOS</code>: unless the terminal application is restarted after installing and configuring <code>kubectl</code>, you will get <code>"no route to host" when connecting to the cluster</code></warning>
    </tab>
    <tab title="Ubuntu">
        <code-block lang="Bash">curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"</code-block>
        <code-block lang="Bash">sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl</code-block>
    </tab>
    <tab title="Windows">
        <p>Start <i>Administrative</i> PowerShell</p>
        <p>Substitute version in URL with the latest version in the examples below.</p>
        <code-block lang="PowerShell">curl.exe -LO "https://dl.k8s.io/release/v1.31.2/bin/windows/amd64/kubectl.exe"</code-block>
        <code-block lang="PowerShell">mv .\kubectl.exe C:\Windows\System32\</code-block>
        <p>Exit administrative PowerShell and open regular PowerShell window</p>
        <code-block lang="PowerShell">kubectl version</code-block>
        <code-block lang="PowerShell">
            Client Version: v1.31.2
            Kustomize Version: v5.4.2
            Unable to connect to the server: dial tcp [::1]:8080: connectex: No connection could be made because the target machine actively refused it.
        </code-block>
        <p>Install <code>PowerShell</code> command completion for <code>kubectl</code></p>
        <p>Start <i>Administrative</i> PowerShell</p>
        <code-block lang="PowerShell"> vi $PROFILE.AllUsersAllHosts</code-block>
        <p>Add line</p>
        <code-block lang="PowerShell">kubectl completion powershell | Out-String | Invoke-Expression</code-block>
    </tab>
</tabs>


## Configure to connect to cluster

On all systems, `kubectl` uses a `~/.kube/config` file to reach the cluster.

Copy the `config` file from a `K8s` master.

Verify:
```Bash
kubectl cluster-info 
```
```Bash
Kubernetes control plane is running at https://10.74.8.12:6443
CoreDNS is running at https://10.74.8.12:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```
