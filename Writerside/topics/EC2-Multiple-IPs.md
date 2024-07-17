# EC2 Multiple IPs

There are two ways to assign multiple IPs – using `IPv4 prefix` when creating an `ENI`,
or using `Secondary IP` assignment when creating an instance.

## Using `IPv4 Prefix` when creating an `ENI`

During `ENI` creating expand `Advanced Settings` and under `IPv4 prefix delegation` select either `Auto-assign` or `Custom`.

The allowed IP block size is `/28` which is 15 IP addresses.

![ec2-multiple-ip-01.png](ec2-multiple-ip-01.png) {thumbnail="true"}

## Using `Secondary IP` when creating an instance

When creating an instance, click `Edit` in the `Network Settings` group.

Expand `Advanced network configuration`

![ec2-multiple-ip-02.png](ec2-multiple-ip-02.png) {thumbnail="true"}