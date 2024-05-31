# Create an instance store-backed Linux AMI

>Only the following instance types support an instance store volume as the root device: C1, C3, D2, I2, M1, M2, M3, R3, and X1.
> 
{style="note"}

> AMI creation process for **instance store-backed** AMIs is *different* from AMI creation for EBS-backed AMIs

* Launch an **instance store-backed** instance from AWS AMI catalog.
* Customize the instance for your needs.
* Create an S3 bucket **with ACLS enabled** (if using AMI tools is 1.5.18 or later, you can also use the `ec2-upload-bundle` command to create the S3 bucket)
* Note your AWS account ID
* Create a signing X509 certificate and upload to AWS.[Manage Signing Certificates](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/set-up-ami-tools.html#ami-tools-managing-certs)
* In your instance:
  * install AMI tools 
  * install AWS CLI
  * configure AWS CLI with credentials
  * install AMI tools dependencies [To prepare to use the AMI tools (HVM instances only)](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/create-instance-store-ami.html#amazon_linux_instructions)
  * copy your X509 cert and private key to `/tmp/cert` on the instance (this is so only you and AWS can access the AMI)
  * use `ec2-bundle-vol` to create your instance's volume bundle 
  * (optional) edit `image.manifest.xml` if you need to add more instance store-backed volumes to the AMI
  * use `ec2-upload-bundle` to upload the bundle to S3
  * (optional) remove the bundle after the upload is successful
  * register the new AMI using `aws ec2 register-image`