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
        TBD
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
        TBD
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
        TBD
    </tab>
</tabs>

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
        Download <code>Google Cloud CLI</code> <a href="https://cloud.google.com/sdk/docs/install">for your Mac platform</a>
        <code-block lang="bash"> tar -zxvf google-cloud-cli-darwin-x86_64.tar.gz </code-block>
        <code-block lang="bash"> ./google-cloud-sdk/install.sh</code-block>
    </tab>
    <tab id="linux-install3" title="Linux">
        TBD
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

To verify your gcloud configuration run:
```Bash
gcloud auth list
```
## Re-authenticating
If you get logged off run
```Bash
gcloud auth login --no-launch-browser
```
The process of authentication will be similar to `gcloud init`