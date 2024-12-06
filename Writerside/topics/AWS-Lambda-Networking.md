# Lambda Networking

## Customer VPC connected Lambda

>Every Lambda function runs inside a VPC that is owned and managed by the Lambda service. 
>These VPCs are maintained automatically by Lambda and aren't visible to customers

>By default, Lambda functions have access to the public internet.
>When you attach your function to a VPC, it can only access resources existing within that VPC.
> 
{style="note"}

The internet access should be provided through the VPC Lambda is connected to.
> **Consider Lambda Concurrency**
> 
> When `Lambda` is connected to a `VPC`, the `VPC ENI` **will acquire a separate IP address** from the subnet **for each invocation**.
> 
> This means that your subnet should have **enough free IP space** to support _high concurrency__ invocation scenarios.
> 
> If IP space is exhausted, you may get `EC2ThrottledException`
{style="warning"}

![aws-lambda-networking-02.png](aws-lambda-networking-02.png){thumbnail="true"}

<procedure title="Configure Lambda to use VPC" id="procedure-id">
   <step>Attach the AWS managed policy <code>AWSLambdaVPCAccessExecutionRole</code> to your function's execution role.</step>
   <step>Edit Lambda configuration and select VPC to attach to</step>
   <step>Select a private subnet and a security group for Lambda to use</step>
</procedure>

NOTES:

{type="medium"}
`Lmabda Service VPC`
: AWS-managed internal VPC for hosting Lambda functions. It is not visible or accessible to customer.

`VPC-to-VPC NAT`
: AWS-managed internal NAT Gateway connecting `Lambda Service VPC` to `ENI` in `Customer VPC`

`ENI`
: Elastic Network Interface **created in the `Customer VPC` by Lambda service** using `AWSLambdaVPCAccessExecutionRole` managed permission policy.
  <br/>`ENI` is assigned a **private IP** address from the range of `Private Subnet`.
  <br/> **It is not possible to assign a _public_ IP to a Lambda ENI**.

`Security Group`
: Security group attached to the `ENI`. Defines Lambda network access. This is the security group selected in step 3

`Private Subnet`
: Private subnet attached to the `ENI`. Defines ENI private IP range. This is the subnet selected in step 3

`Route Table`
: Route table pointing to NAT Gateway in the public subnet

`NAT Gateway`
: NAT gateway in the public subnet providing internet access through iGW

## Default Lambda Networking

Internet access provided by the iGW in Lambda Service VPC.

Customer VPC is not accessible

![aws-lambda-networking-03.png](aws-lambda-networking-03.png) {thumbnail="true"}

Reference: [Configuring Lambda With VPC Resources Access](https://medium.com/@arunasilva86/how-to-use-vpc-networking-effectively-for-lambda-445abefdf8cf)