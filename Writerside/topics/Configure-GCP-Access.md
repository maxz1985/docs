# Configure GCP Access

## Install `terraform`
<tabs>
    <tab id="windows-install" title="Windows">
        <code-block lang="powershell"> winget install Hashicorp.Terraform </code-block>
    </tab>
    <tab id="macos-install" title="macOS">
        <code-block lang="bash"> brew install terraform </code-block>
    </tab>
    <tab id="linux-install" title="Linux">
        <code-block lang="bash">sudo apt-get update &&  \
sudo apt-get install -y gnupg software-properties-common</code-block>
        <code-block lang="bash">wget -O- https://apt.releases.hashicorp.com/gpg | \
gpg --dearmor | \
sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null</code-block>
        <code-block lang="bash">gpg --no-default-keyring \
--keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg \
--fingerprint</code-block>
        <code-block lang="bash">echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list</code-block>
        <code-block lang="bash">sudo apt-get update && \
sudo apt-get install terraform -y</code-block>   
    </tab>
</tabs>

## Install Python 3.12
`gcloud` CLI depends on Python 3.12.

If Python 3.12 is not installed, you would have to set n `CLOUDSDK_PYTHON` environment variable pointing to the Python interpreter of your choice; 
It is easier to just have Python 3.12 installed.

First, check if you already have Python 3.12 installed:
<tabs>
    <tab id="windows-install2" title="Windows">
        <code-block lang="PowerShell"> py -0 </code-block>
        <code-block lang="PowerShell">  
            -V:3.13 *        Python 3.13 (64-bit)
            -V:3.12          Python 3.12 (64-bit)
            -V:3.11          Python 3.11 (64-bit)
        </code-block>
    </tab>
    <tab id="macos-install2" title="macOS">
        <code-block lang="bash"> brew list | grep ^python</code-block>
        <code-block lang="bash">
            python@3.11
            python@3.12
            python@3.13
        </code-block>
    </tab>
    <tab id="linux-install2" title="Linux">
        <code-block lang="bash">ls -ls /usr/bin/python*</code-block>
        <code-block lang="bash">0 lrwxrwxrwx 1 root root 10 Aug  7  2024 /usr/bin/python3 -> python3.12
7832 -rwxr-xr-x 1 root root 8019136 Feb  4 09:48 /usr/bin/python3.12
6340 -rwxr-xr-x 1 root root 6490504 Apr  9 04:55 /usr/bin/python3.13</code-block>
    </tab>
</tabs>

<tabs>
    <tab id="windows-install1" title="Windows">
        For <b>this specific case</b>, use <code>Microsoft Store</code> to find and install Python 3.12
    </tab>
    <tab id="macos-install1" title="macOS">
        <code-block lang="bash"> brew install Python@3.12</code-block>
    </tab>
    <tab id="linux-install1" title="Linux">
        <code-block lang="bash">sudo add-apt-repository ppa:deadsnakes/ppa</code-block>
        <code-block lang="bash">sudo apt-get update</code-block>
        <code-block lang="bash">sudo apt-get install python3.12 -y</code-block>   
    </tab>
</tabs>

## Install `Docker` in `WSL` (NOT via host's `Docker Desktop`)
> The normal procedure for enabling `docker` in `WSL` involves installing `Docker Desktop` on the host
> and using that engine in `WSL`. That is described in
>[Get started with Docker remote containers on WSL 2](https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-containers)
> or [Docker Desktop WSL 2 backend on Windows](https://docs.docker.com/desktop/features/wsl/)
>
> THIS document describes how to install `docker` directly inside `WSL` avoiding `Docker Desktop` usage.
> 
{style="note"}

Install required packages
```bash
sudo apt-get install -y apt-transport-https ca-certificates curl \
gnupg lsb-release
```
Install docker `gpg` key
```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
```
Add docker's stable repository
```bash
echo "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
Update the package cache and install required packages
```bash
sudo apt-get update
```
```bash
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```
### Test `docker` in WSL

Make sure the service is running
```bash
sudo systemctl status docker
```
```bash
● docker.service - Docker Application Container Engine
     Loaded: loaded (/usr/lib/systemd/system/docker.service; enabled; preset: enabled)
     Active: active (running) since Wed 2025-04-16 16:14:14 EDT; 9s ago
TriggeredBy: ● docker.socket
       Docs: https://docs.docker.com
   Main PID: 279 (dockerd)
      Tasks: 14
     Memory: 102.0M ()
     CGroup: /system.slice/docker.service
             └─279 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
```
Check `docker` version
```bash
docker version
```
```bash
Docker version 28.0.4, build b8034c0
```
Run a test container
```bash
sudo docker run hello-world
```
```bash
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
e6590344b1a5: Pull complete
Digest: sha256:424f1f86cdf501deb591ace8d14d2f40272617b51b374915a87a2886b2025ece
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.
```
## Install Google Cloud SDK

<tabs>
    <tab id="windows-install3" title="Windows">
        <code-block lang="PowerShell">
        (New-Object Net.WebClient).DownloadFile("https://dl.google.com/dl/cloudsdk/channels/rapid/GoogleCloudSDKInstaller.exe", "$env:Temp\GoogleCloudSDKInstaller.exe")
        & $env:Temp\GoogleCloudSDKInstaller.exe
        </code-block>
        <b>Uncheck</b> "Install bundled python" component during the installation.
    </tab>
    <tab id="macos-install3" title="macOS">
        <p>Download <code>Google Cloud CLI</code> <a href="https://cloud.google.com/sdk/docs/install">for your Mac platform</a>.</p>
        <p>Place the file into your <code>$HOME</code> directory.</p> 
        <code-block lang="bash"> tar -zxvf google-cloud-cli-darwin-x86_64.tar.gz </code-block>
        <code-block lang="bash"> ./google-cloud-sdk/install.sh</code-block>
    </tab>
    <tab id="linux-install3" title="Linux">
        <code-block lang="bash">curl -k https://packages.cloud.google.com/apt/doc/apt-key.gpg | \
sudo gpg --dearmor -o /usr/share/keyrings/cloud.google.gpg</code-block>
    </tab>
</tabs>



## Configure `gcloud` CLI

> In some environments, you need to use a **specific** browser where you work with GCP to authorize `gcloud` for your account.
> 
> Therefore, it is best to use the `--console-only` option and perform the browser part of the authorization manually.
> 
{style="note"}

```Bash
gcloud init --console-only
```
After the `You must sign in to continue. Would you like to sign in (Y/n)?` prompt, you will see a URL:
```Bash
 https://accounts.google.com/o/oauth2/auth?response_type=code&client_id=...
```
Copy and paste the URL into the **specific browser where you work with GCP**, it is best if you are already logged into the GCP console there.

GCP will generate a verification code response in the browser. Copy and paste this code into your terminal.

Pick a default project and `us-east4-a` as your default zone.

To verify your gcloud configuration, run:
```Bash
gcloud auth list
```
## Re-authenticating for `gcloud` commands
If you get logged off, run
```Bash
gcloud auth login --no-launch-browser
```
The process of authentication will be similar to `gcloud init`

## Authenticating for `terraform`
`Terraform` requires a special type of authentication called ADC (Application Default Credentials)
To run `terraform` commands authenticate as follows:
```Bash
gcloud auth application-default login --no-launch-browser
```