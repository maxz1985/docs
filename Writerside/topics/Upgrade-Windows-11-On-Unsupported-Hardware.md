# Upgrade Windows 11 On Unsupported Hardware

<tldr><code>setupprep.exe /product server</code></tldr>

## Brief
Starting with Windows 11 version 21H2, Microsoft introduced a "supported hardware" check before proceeding with installation.

There is still a way to install or upgrade a Windows 11 installation to a next version on unsupported hardware.

## Upgrade
1. Navigate to the [Official Windows Download Site](https://www.microsoft.com/en-us/software-download/windows11) and download the `Media Creation Tool`
2. Get a USB Drive at least `32GB` in size
3. Use the `Media Creation Tool` to burn the latest Windows 11 image onto the USB drive.
4. Open the USB drive in Windows Explorer
5. `Shift-Right-click` on the `Sources` folder and select `Open PowerShell window here`
6. Type `cmd` at PowerShell prompt then type:
```shell
setupprep.exe /product server
```
7. Follow the on-screen instructions.

> Note that although the setup screen shows "Windows Server," you're still installing a regular edition that matches your current SKU.
> 
{style="note"}

REF: [](https://www.neowin.net/guides/how-to-install-windows-11-24h2-on-unsupported-hardware/) 