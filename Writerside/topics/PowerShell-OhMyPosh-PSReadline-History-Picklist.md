# PowerShell OhMyPosh PSReadline History Picklist

> The actions below are performed from **Administrative** Terminal session
> 
{style="note"}

## Install OhMyPosh
```shell
winget install JanDeDobbeleer.OhMyPosh -s winget
```
## Install NerdFont (ex Meslo)
```shell
oh-my-posh font install meslo
```
## Set `MesloLGS Nerd Font` in Terminal
<ui-path>Terminal Settings -> Profiles -> Default -> Appearance -> Font Face</ui-path>

## History Picklist
Install the latest `PSReadline` PowerShell module
```shell
Install-Module PSReadLine -AllowPrerelease -Force
```
## Activate OhMyPosh and PSReadline with history picklist
Edit global PowerShell Profile.
```shell
 code $PROFILE.AllUsersAllHosts
```
Place the following content into `profile.ps1` and save
```shell
oh-my-posh init pwsh | Invoke-Expression
if ($host.Name -eq 'ConsoleHost')
{
    Import-Module PSReadLine
}
Set-PSReadLineOption -PredictionViewStyle ListView
```

Restart Terminal for changes to take effect

## Additional info
### Fonts
The Best fonts to use are either `Meslo Nerd Font` or `CaskaydiaCove Nerd Font Mono`:

[Nerd Font Downloads](https://www.nerdfonts.com/font-downloads)

Get installed font names to be used in `VSCode` settings on Windows:
```shell
[System.Reflection.Assembly]::LoadWithPartialName("System.Drawing")
```
```shell
(New-Object System.Drawing.Text.InstalledFontCollection).Families
```
Setting VSCode built-in terminal font:

![powershell-ohmyposh-psreadline-history-picklist-01.png](powershell-ohmyposh-psreadline-history-picklist-01.png)

in `Settings` search for `Terminal > Integrated:Font Family`
### Other notes
File type Icons in directory lists in Terminal: (makes PowerShell profile load slower)
```shell
Install-Module terminal-icons -Scope AllUsers -Force
```
Posh-Git:
```shell
Install-Module Posh-Git -Scope AllUsers -Force
```
Go to PowerShell Global Profile directory
```shell
cd ($PROFILE.AllUsersAllHosts | Split-Path)
```