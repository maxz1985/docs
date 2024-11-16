# Install K9s

Check the latest K9s release on GitHub [](https://github.com/derailed/k9s)

>`K9s` relies on `kubectl` installed and configured to manage a cluster
> 
{style="note"}
<tabs>
    <tab title="Ubuntu">
        <p>Note the latest release version, for example <code>v0.32.6</code></p>
        <p>Run the installation command substituting <code>{version}</code> with the version you intend to install.</p>
        <code-block lang="bash">wget https://github.com/derailed/k9s/releases/download/{version}/k9s_linux_amd64.deb && sudo apt install ./k9s_linux_amd64.deb && sudo rm k9s_linux_amd64.deb</code-block>
        <p>example:</p>
        <code-block lang="Bash">wget https://github.com/derailed/k9s/releases/download/v0.32.6/k9s_linux_amd64.deb && sudo apt install ./k9s_linux_amd64.deb && sudo rm k9s_linux_amd64.deb</code-block>
    </tab>
    <tab title="Windows">
    <code-block lang="powershell">winget install k9s</code-block>
    </tab>
</tabs>

`K9s` uses the same `K8s` configuration as `kubectl`. So if you have `kubectl` configured on your machine, simply run:
```Shell
k9s
```