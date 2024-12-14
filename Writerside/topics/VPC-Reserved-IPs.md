# VPC Reserved IPs

In a VPC, the **first three** and **last one** IP addresses of the entire range for each CIDR block are **reserved**.

## Example

| CIDR block                                  | 20.0.0.0/25                |
|---------------------------------------------|----------------------------|
| Netmask                                     | 255.255.255.128 (/25)      |
| FULL CIDR NETWORK RANGE                     | 20.0.0.0 -> 20.0.0.127     |
| **IN AWS for this CIDR:**                   |                            |
| Base Network Address (`BASE CIDR`)          | 20.0.0.0 (reserved)        |
| VPC Router (`BASE CIDR` + `1`)              | 20.0.0.1 (reserved)        |
| DNS (`BASE CIDR` + `2`)                     | 20.0.0.2 (reserved)        |
| Reserved for future use (`BASE CIDR` + `3`) | 20.0.0.3 (reserved)        |
| **Available user range**                    | **20.0.0.4 -> 20.0.0.126** |
| Broadcast Address                           | 20.0.0.127 (reserved)      |
