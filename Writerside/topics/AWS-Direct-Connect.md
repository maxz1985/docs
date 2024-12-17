# Direct Connect
Direct Connect uses a concept of `Virtual Interface` or `VIF` to facilitate connection to AWS Resources.

There are three types of `VIF`:
* `Private VIF` used to connect to `VPC`s
* `Public VIF` used to connect to Publicly Routable AWS Resources.
* `Transit VIF` used specifically to connect to a `Direct Connect Gateway` which is linked to `Transit Gateway`

`VIF` creation is initiated via `AWS Console` for a particular `AWS Direct Connect Endpoint`,
which then causes AWS to remotely configure a `VIF object` in the `AWS Direct Connect ROUTER` at a location.

## Single Region `Private VIF` connections
### Single Direct Connect connection to a Multi-AZ VPC

![aws-direct-connect-04.png](aws-direct-connect-04.png) {thumbnail="true"}

> `Virtual Private Gateway` or `VGW` is an endpoint created in the VPC to terminate connections from `Site-to-Site VPN`,
> `Private VIF`of a Direct Connect or a connection from a `Direct Connect Gateway`.
> 
> There is a **single** `VGW` per `VPC`.
> 
> `VGW` supports handling a mix of multiple connections simultaneously.
> A single `VGW` can serve a mix of multiple
> `Site-to-Site`, `Private VIF` and `Direct Connect Gateway` connections.
> 
{style="note"}

### Redundant Direct Connect connection to a Multi-AZ VPC

![aws-direct-connect-05.png](aws-direct-connect-05.png) {thumbnail="true"}

> Note in this case each Direct Connect Location has its own `VIF`
> 
> It is possible to have multiple `VIF`s per Direct Connect Location, but each `VIF` has to terminate on its own different `VGW`
> 
{style="note"}
## Multi-Region `Private VIF` connections {id="multi-region"}
### Redundant Direct Connect connection to VPCs in **different Regions** using **Direct Connect Gateway (DXGW)**

![aws-direct-connect-06.png](aws-direct-connect-06.png) {thumbnail="true"}

* Use **AWS Direct Connect Gateway for Inter-Region VPC access**
* Use **private virtual interface** between `Direct Connect Endpoint` and `Direct Connect Gateway`
* Use **private virtual gateway** in each VPC to connect to `Direct Connect Gateway`

### Using `Transit Gateway` with `Direct Connect`
* scalable when attaching to **numerous** `VPC`s
* saves on `VIF`s and `VGW`s  – only **one** `Transit VIF` required, and one `Transit Gateway` per `Region`

![aws-direct-connect-07.png](aws-direct-connect-07.png){thumbnail="true"}

## Direct Connect to Publicly Routable AWS Services (`Public Virtual Interface`)

![aws-direct-connect-03.png](aws-direct-connect-03.png) {thumbnail="true"}

## LAG (LACP)
LAG (Link Aggregation Group)
can be used with Direct Connect if you ordered more than one connection to an AWS Direct Connect Endpoint.

![aws-direct-connect-01.png](aws-direct-connect-01.png){thumbnail="true"}

* Create a LAG group in AWS Direct Connect Console
* The LAG connection is seen as a single interface by your router