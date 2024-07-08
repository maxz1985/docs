# AWS Wavelength

## Accessing the internet from a Wavelength zone

![aws-wavelength-01.png](aws-wavelength-01.png) {thumbnail="true"}

* Use `Carrier Gateway` to access the internet from a Wavelength zone
* A `Carrier Gateway` serves two purposes. 
  * It allows inbound traffic from a carrier network at a specific location
  * it allows outbound traffic to the carrier network and traffic **to** the **internet** via AWS network
  * There is no inbound connection configuration from the internet to a Wavelength Zone through the carrier gateway.
* `Carrier Gateway` ONLY supports `IPv4`

## Communication between EC2 instances deployed in 2 different Wavelength zones

> EC2 Instances launched into **different** Wavelength Zones can't communicate with each other **if they're 
> associated with the same VPC**
> 
{style="warning"}

![aws-wavelength-02.png](aws-wavelength-02.png) {thumbnail="true"}

To enable communication, each Wavelength Zone has to be associated with its **own VPC** and a **`Transit Gateway` should
be used to enable VPC-to-VPC communication**.

![aws-wavelength-03.png](aws-wavelength-03.png) {thumbnail="true"}

![aws-wavelength-04.png](aws-wavelength-04.png) {thumbnail="true"}

REF: [Subnets in AWS Wavelength](https://docs.aws.amazon.com/vpc/latest/userguide/subnet-wavelength.html)

PPT: <resource src="Communication between instances in different Wavelength Zones.pptx"/>