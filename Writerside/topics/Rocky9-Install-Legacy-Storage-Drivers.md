# Rocky9 Install Legacy Storage Drivers

Rocky9 has deprecated some of the old storage controllers such as `mpt sas` and `megaraid sas`.

Drivers for deprecated controllers will not be included in the distribution by default,
leading to inability to access the array.

The drivers can still be installed from a community-supported [ELRepo](https://elrepo.org/wiki/doku.php?id=start)
project that maintains the legacy drivers.

## Symptoms
After installing Rocky9 and trying to access the RAID array, you may encounter the following:

From GNOME Disk Utility

![rocky9-install-legacy-storage-drivers-01.png](rocky9-install-legacy-storage-drivers-01.png) {thumbnail="true"}

When attempting to format the volume

![rocky9-install-legacy-storage-drivers-02.png](rocky9-install-legacy-storage-drivers-02.png) {thumbnail="true"}

When attempting to use `fdisk`
```Shell
sudo fdisk /dev/sda -l
```
```Shell
fdisk: cannot open /dev/sda: Input/output error
```
## Find the controller ID
```Shell
/sbin/lspci -nn | grep -i RAID
```
```Shell
02:00.0 RAID bus controller [0104]: Broadcom / LSI MegaRAID SAS 2208 [Thunderbolt] [1000:005b] (rev 05)
```
> The part of the output to note is the **Controller ID** which in this case is `1000:005b`
> 
{style="note"}

## Install `ELRepo` repository
```Shell
sudo dnf install elrepo-release
```
## Find `ELRepo` driver for your controller

Go to [](https://elrepo.org/wiki/doku.php?id=deviceids)and search for your controller id, in this case `1000:005b`

![rocky9-install-legacy-storage-drivers-03.png](rocky9-install-legacy-storage-drivers-03.png) {thumbnail="true"}

>In this case, the driver is `kmod-megaraid_sas`
>
{style="note"}

## Install the driver

Search `ELRepo` for the *exact* package name
```Shell
sudo dnf -q list kmod-megaraid_sas
```
```Shell
Available Packages
kmod-megaraid_sas.x86_64                                          07.725.01.00-3.el9_3.elrepo
```
Install the driver package
```
sudo dnf install kmod-megaraid_sas.x86_64
```
```Shell
ast metadata expiration check: 0:01:32 ago on Fri 26 Apr 2024 10:22:03 AM EDT.
Dependencies resolved.
==============================================================================================================================================
Package                             Architecture             Version                                          Repository                Size
==============================================================================================================================================
Installing:
kmod-megaraid_sas                   x86_64                   07.725.01.00-3.el9_3.elrepo                      elrepo                   112 k

Transaction Summary
==============================================================================================================================================
Install  1 Package

Total download size: 112 k
Installed size: 440 k
Is this ok [y/N]: y
Downloading Packages:
kmod-megaraid_sas-07.725.01.00-3.el9_3.elrepo.x86_64.rpm                                                      203 kB/s | 112 kB     00:00
----------------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                                         153 kB/s | 112 kB     00:00
ELRepo.org Community Enterprise Linux Repository - el9                                                        1.6 MB/s | 1.7 kB     00:00
Importing GPG key 0xBAADAE52:
Userid     : "elrepo.org (RPM Signing Key for elrepo.org) <secure@elrepo.org>"
Fingerprint: 96C0 104F 6315 4731 1E0B B1AE 309B C305 BAAD AE52
From       : /etc/pki/rpm-gpg/RPM-GPG-KEY-elrepo.org
Is this ok [y/N]: y
Key imported successfully
Running transaction check
Transaction check succeeded.
Running transaction test
Transaction test succeeded.
Running transaction
Preparing        :                                                                                                                      1/1
Installing       : kmod-megaraid_sas-07.725.01.00-3.el9_3.elrepo.x86_64                                                                 1/1
Running scriptlet: kmod-megaraid_sas-07.725.01.00-3.el9_3.elrepo.x86_64                                                                 1/1
Verifying        : kmod-megaraid_sas-07.725.01.00-3.el9_3.elrepo.x86_64                                                                 1/1

Installed:
kmod-megaraid_sas-07.725.01.00-3.el9_3.elrepo.x86_64

Complete!
```
Reboot for the changes to take effect