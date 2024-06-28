# AWS Wavelength

![aws-wavelength-01.png](aws-wavelength-01.png) {thumbnail="true"}

* Use `Carrier Gateway` to access the internet from a Wavelength zone
* A `Carrier Gateway` serves two purposes. 
  * It allows inbound traffic from a carrier network at a specific location
  * it allows outbound traffic to the carrier network and traffic **to** the **internet** via AWS network
  * There is no inbound connection configuration from the internet to a Wavelength Zone through the carrier gateway.
* `Carrier Gateway` ONLY supports `IPv4`