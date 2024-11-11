# K8S Installation

This document was created on _Ubuntu_

## (optional for cloned VMs) Make sure `product_uuid` is unique for every node
```shell
 sudo cat /sys/class/dmi/id/product_uuid
```
```shell
034c8473-3064-4ab3-9f84-1a4ddf32bf4f
```
To create a new random `product_uuid`:
* find out which file system is used on your boot volume
```shell
lsblk -f
```
```shell
test
```
* Update `product_uuid` using that file system's tool. ex for `ext4`
```shell
sudo tune2fs -c 1 /dev/sda2
sudo tune2fs /dev/sda2 -U random
```

