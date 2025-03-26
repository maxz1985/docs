# PowerShell OhMyPosh PSReadline History Picklist

> The actions below are performed from **Administrative** Terminal session
> 
{style="note"}
## Suggested `profile.ps1` file
```PowerShell
# Alias vi to vim
Set-Alias -Name vi -Value vim
# Fix blue directories after PowerShell v7.3
$PSStyle.FileInfo.Directory = "`e[38;2;255;255;255m"
# Activate Terminal-Icons
Import-Module -Name Terminal-Icons
# Activate Posh-Git
Import-Module posh-git
# Activate oh-my-posh
oh-my-posh init pwsh | Invoke-Expression
# Activate PSReadLine and history picklist
if ($host.Name -eq 'ConsoleHost') 
{
    Import-Module PSReadLine
}
Set-PSReadLineOption -PredictionViewStyle ListView
```
## Install OhMyPosh
```shell
winget install JanDeDobbeleer.OhMyPosh -s winget
```
## Install NerdFont (ex Meslo)
```shell
oh-my-posh font install meslo
```
> When installing fonts from a **non-administrative** `Terminal` session, there is an additional step.
> 
> From a non-administrative `Terminal` session, the fonts will be installed into:
> ```shell
> $USERPROFILE$\AppData\Local\Microsoft\Windows\Fonts
> ```
> This will result in `Unable to locate specified font` error on `Terminal` startup.
> 
> Navigate to the above directory on your drive, right-click font files and choose `Show more options`,
> then select `Install for all users`
>
{style="warning"}
## Set `MesloLGS Nerd Font` in Terminal
<ui-path>Terminal Settings -> Profiles -> Default -> Appearance -> Font Face</ui-path>

## History Picklist
Install the latest `PSReadline` PowerShell module
```PowerShell
Install-Module PSReadLine -AllowPrerelease -Force
```
## Activate OhMyPosh and PSReadline with history picklist
Edit global PowerShell Profile.
```shell
 code $PROFILE.AllUsersAllHosts
```
Alternatively, if you're using a local profile:
```shell
code $PROFILE.CurrentUserAllHosts
```
Place the following content into `profile.ps1` and save
```PowerShell
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
```PowerShell
[System.Reflection.Assembly]::LoadWithPartialName("System.Drawing")
```
```PowerShell
(New-Object System.Drawing.Text.InstalledFontCollection).Families
```
Setting VSCode built-in terminal font:

![powershell-ohmyposh-psreadline-history-picklist-01.png](powershell-ohmyposh-psreadline-history-picklist-01.png)

in `Settings` search for `Terminal > Integrated:Font Family`
### Other notes
#### Terminal-Icons
Add file type icons to directory lists in Terminal: (makes PowerShell profile load slower)
```PowerShell
Install-Module terminal-icons -Scope AllUsers -Force
```
Activate in `profile.ps1`by adding:
```PowerShell
Import-Module -Name Terminal-Icons
```
#### Posh-Git
```PowerShell
Install-Module Posh-Git -Scope AllUsers -Force
```
Activate in `profile.ps1`by adding:
```PowerShell
Import-Module posh-git
```
#### Navigating to PowerShell Global Profile directory
```PowerShell
cd ($PROFILE.AllUsersAllHosts | Split-Path)
```
#### After `PowerShell v7.3` directory items are `blue` and hard to read.
Add the following to `profile.ps`:
```PowerShell
$PSStyle.FileInfo.Directory = "`e[38;2;255;255;255m"
```
