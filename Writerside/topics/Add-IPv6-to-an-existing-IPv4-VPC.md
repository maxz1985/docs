# Add IPv6 to an existing IPv4 VPC

1.  Associate an IPv6 CIDR Block with VPC and subnets - In the VPC Console, choose `Add IPv6 CIDR`.
2.  Update Route Tables
    1.  For _public_ subnets create a route that forwards all IPv6 traffic from the subnet to the Internet Gateway
    2.  For _private_ subnets create a route that forwards all *internet-bound IPv6 traffic* to an `Egress-Only Internet Gateway`
3.  Update security group rules
4.  Change the Instance type for EC2s **if** your current instance type does not support IPv6. (all current generation instance types support IPv6)
5.  Assign IPv6 addresses to your instances

>NAT Gateway is an IPv4-only service, for IPv6 an Egress-Only Internet Gateway should be used.
> 
{style="warning"}

[Add IPv6 support to your VPC](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-migrate-ipv6.html#vpc-migrate-assign-ipv6-address)