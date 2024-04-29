# Rocky9 Install Legacy Storage Drivers

```Shell
sudo dnf install elrepo-release
```
```Shell
/sbin/lspci -nn | grep -i RAID
```

```Shell
02:00.0 RAID bus controller [0104]: Broadcom / LSI MegaRAID SAS 2208 [Thunderbolt] [1000:005b] (rev 05)
```
goto [](https://elrepo.org/wiki/doku.php?id=deviceids) and find `1000:005b`, it is 



## preconditions

sudo fdisk /dev/sda -l
fdisk: cannot open /dev/sda: Input/output error

sudo fdisk -l


Disk /dev/sdb: 237.97 GiB, 255516999680 bytes, 499056640 sectors
Disk model: MR9271-8i
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 2E8048D3-0222-4796-A633-70336F22596E

Device       Start       End   Sectors   Size Type
/dev/sdb1     2048   1230847   1228800   600M EFI System
/dev/sdb2  1230848   3327999   2097152     1G Linux filesystem
/dev/sdb3  3328000 499054591 495726592 236.4G Linux LVM

sudo dnf -q list kmod-megaraid_sas
Available Packages
kmod-megaraid_sas.x86_64                                          07.725.01.00-3.el9_3.elrepo

sudo dnf install kmod-megaraid_sas.x86_64
Last metadata expiration check: 0:01:32 ago on Fri 26 Apr 2024 10:22:03 AM EDT.
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

sudo reboot

find . -type d -name "mega*"
./kernel/drivers/scsi/megaraid
./weak-updates/megaraid_sas