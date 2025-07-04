# NordVPN IPsec

How to create a native NordVPN IPSec connection in Windows 11.

Connect without using NordVPN App.

[Connect to NordVPN (IKEv2/IPSec) on Windows](https://support.nordvpn.com/hc/en-us/articles/19916965616017-Connect-to-NordVPN-IKEv2-IPSec-on-Windows)
## Import NordVPN Certificate
Download certificate 

[NordVPN Root Certificate](https://downloads.nordcdn.com/certificates/root.der)

## Fix `Policy Match Error`
![fix-policy-match-error-01.png](fix-policy-match-error-01.png) {thumbnail="true"}

* Launch *Administrative PowerShell*
* Run the following command below replacing `"Your VPN Name"` with the connection name you created
```powershell
Set-VpnConnectionIPsecConfiguration -ConnectionName "Your VPN Name" -AuthenticationTransformConstants SHA256128 -CipherTransformConstants AES128 -DHGroup Group14 -EncryptionMethod AES128 -IntegrityCheckMethod SHA256 -PFSgroup PFS2048 -Force
```