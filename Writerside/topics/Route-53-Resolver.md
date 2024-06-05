# Route 53 Resolver

* An AWS-Managed DNS resolution service, **one instance of service per each AZ**
* available to all VPC at `VPC + 2` IP address

## Automatically Resolves
* Local VPC domain names for EC2 instances (for example, `ec2-192-0-2-44.compute-1.amazonaws.com`).
* Records in **private hosted zones** (for example, `acme.example.com`).
* recursive lookups against public name servers on the internet for public domain names.

## Using in a hybrid setup with on-prem DNS
* Create a secure connection b/w cloud and on-prem (VPN or DirectConnect)
* Create an **inbound** Resolver endpoint in each AZ to answer on-prem queries
* Create an **outbound** Resolver endpoint to forward the queries to on-prem DNS infrastructure
* A set of Forwarding Rules, one per domain name, indicating where to forward the queries to.

![route-53-resolver-01.png](route-53-resolver-01.png) {thumbnail="true"}