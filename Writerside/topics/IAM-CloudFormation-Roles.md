# IAM CloudFormation Roles

## Scenario
Restrict `Engineers` to create **ONLY approved resources** using `CloudFormation`.

## Role Setup
* `IAM Role` assigned to `Engineers`
* `Permission Policy` attached to the `Engineers IAM Role` allowing only `CloudFormation` actions
* `Service-Linked Role` to be assumed by `CloudFormation` when creating a stack
* Trust Policy attached to CloudFormation Service-Linked Role allowing CloudFormation to assume the Service-Linked Role
* Permission Policy attached to CloudFormation Service-Linked Role allowing creation of ONLY approved resources.