# Terraformer (Reverse Terraform)

CLI to generate `.tf` files from existing infrastructure.

[GoogleCloudPlatform | terraformer](https://github.com/GoogleCloudPlatform/terraformer)

## Prerequisites
* AWS CLI installed and configured with authentication
* `terraform` installed
* A working directory created where the `terraformer` output will be saved

## Installation
First, install binary
```shell
brew install terraformer
```
Navigate to the directory where you want `terraformer` to output `.tf` files

We're going to be using AWS provider (other providers available)

Create a `version.tf` file in the directory with the following content.
```shell
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.55"
    }
  }

  required_version = ">= 1.8.5"
}
```
Run `init` in the directory.
```shell
terraform init
```

## Operation
Query which AWS services are available to scan (import)
```shell
 terraformer import aws list
```
Create terraform files for the current AWS services configuration (examples)

The output files will be located under `{your directory}/generated/aws/{service name}`
```shell
terraformer import aws --resources="api_gateway"
```
```shell
terraformer import aws --resources="lambda"
```
Example output for API Gateway import:
```shell
api_gateway_integration.tf          api_gateway_rest_api.tf
api_gateway_integration_response.tf api_gateway_stage.tf
api_gateway_method.tf               outputs.tf
api_gateway_method_response.tf      provider.tf
api_gateway_model.tf                terraform.tfstate
api_gateway_resource.tf
```
> IF you're planning on _checking in_ the output, make sure to ignore the `.terraform` directory in your `.gitignore`
> 
{style="warning"}

List of AWS services that `terraformer` supports:
```shell
accessanalyzer
acm
alb
api_gateway
appsync
auto_scaling
batch
budgets
cloud9
cloudformation
cloudfront
cloudhsm
cloudtrail
cloudwatch
codebuild
codecommit
codedeploy
codepipeline
cognito
config
customer_gateway
datapipeline
devicefarm
docdb
dynamodb
ebs
ec2_instance
ecr
ecrpublic
ecs
efs
eip
eks
elastic_beanstalk
elasticache
elb
emr
eni
es
firehose
glue
iam
identitystore
igw
iot
kinesis
kms
lambda
logs
media_package
media_store
medialive
msk
nacl
nat
opsworks
organization
qldb
rds
redshift
resourcegroups
route53
route_table
s3
secretsmanager
securityhub
servicecatalog
ses
sfn
sg
sns
sqs
ssm
subnet
swf
transit_gateway
vpc
vpc_peering
vpn_connection
vpn_gateway
waf
waf_regional
wafv2_cloudfront
wafv2_regional
workspaces
xray
```
