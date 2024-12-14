# Private Hosted Zone Sharing

## Default VPC DNS

The **default VPC DNS** hosted at `Base CIDR + 2` resolves:
* Internal resource names within the VPC (for example, `ip-10-0-1-123.ec2.internal`).
* Public domain names via AWS public DNS resolvers (for example, `www.jetbrains.com).

Default VPC DNS **can't resolve names for resources in another VPC**, even if the VPCs are connected (for example, via peering).

Enter `Private Hosted Zones` (`PHZ`).

## Private Hosted Zones

With `PHZ` you can:
* Define Custom DNS Names, example `db.internal.example.com` to the private IP of a database
* Share DNS Names Across `VPC`s, example Associate the `PHZ` with multiple `VPC`s, **including across AWS Accounts**
* Centralize private DNS management, manage DNS records in one place

> `Private Hosted Zone` defined in one `AWS Account` can be authorized to provide resolution for a VPC in **another** A`WS Account`
> 
{style="note"}

## Associate a `PHZ` with `VPC` in Another AWS Account
Let say we have a `PHZ` in `Account A` and we would like to share that `PHZ` with a VPC in `Account B`
* In `Account A`, **authorize** the `Account B VPC` to be associated with the `PHZ`
```bash
aws route53 create-vpc-association-authorization \
--hosted-zone-id Z123456ABCDEFG \
--vpc VPCRegion=us-east-1,VPCId=vpc-xxxxxxxxxxxxxxxx
```
* In `Account B`, **associate** `VPC` with `PHZ`
```Bash
aws route53 associate-vpc-with-hosted-zone \
  --hosted-zone-id Z123456ABCDEFG \
  --vpc VPCRegion=us-east-1,VPCId=vpc-xxxxxxxxxxxxxxxx

```
* In `Account A`, **remove** the authorization **after** VPC has been associated to prevent any further unauthorized associations
```Bash
aws route53 delete-vpc-association-authorization \
  --hosted-zone-id Z123456ABCDEFG \
  --vpc VPCRegion=us-east-1,VPCId=vpc-xxxxxxxxxxxxxxxx
```
## DNS Resolution after PHZ association
* `BASE CIDR +2` remains the DNS resolver for **VPC**
*  **`PHZ` records take priority over internal records**
