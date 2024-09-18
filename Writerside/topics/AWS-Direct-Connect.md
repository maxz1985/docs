# AWS Direct Connect

## LAG (LACP)
LAG (Link Aggregation Group) can be used with Direct Connect if you ordered more than one connection to an AWS Direct Connect Endpoint.

![aws-direct-connect-01.png](aws-direct-connect-01.png){thumbnail="true"}

* Create a LAG group in AWS Direct Connect Console
* The LAG connection is seen as a single interface by your router

## Direct Connect Gateway to connect VPCs in different regions (`Private Virtual Interface`)

![aws-direct-connect-02.png](aws-direct-connect-02.png) {thumbnail="true"}

* Use AWS Direct Connect Gateway for Inter-Region VPC access
* Use **private virtual interface** between `Direct Connect Endpoint` and `Direct Connect Gateway`
* Use **private virtual gateway** in each VPC to connect to `Direct Connect Gateway`

## Direct Connect to Publicly Routable AWS Services (`Public Virtual Interface`)

![aws-direct-connect-03.png](aws-direct-connect-03.png) {thumbnail="true"}