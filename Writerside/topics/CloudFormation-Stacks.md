# CloudFormation Stacks

##  Question
A large enterprise wants to adopt CloudFormation to automate administrative tasks and implement the security
principles of the least privilege and separation of duties.
They've identified the following roles with the corresponding tasks in the company.
* Network administrators: create, modify, and delete VPCs, and delete EC2 instances, subnets, NACLs, routing tables,
and security groups.
* Application operators: deploy complete application stacks (ELB, Auto-Scaling groups, RDS, EC2 instances),
whereas all resources must be deployed in the VPCs managed by the network administrators.

Both groups must maintain their own CloudFormation templates and should be able to create, update,
and delete only their own Cloud Formation stacks.

The company has followed your advice to create two IAM groups, one for applications and one for networks.
Both IAM groups are attached to IAM policies that grant rights to perform the necessary task of each group
and the creation, update, and deletion of CloudFormation stacks.

Which statements represent valid design considerations based on the given setup and requirements? (Select TWO)

## Suggested Answers

|   |                                                                                                                                                                 |
|---|-----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| A | Network stack updates will fail upon attempts to delete a subnet with EC2 instances                                                                             |
| B | Restricting the launch of EC2 instances into VPCs requires resource level permissions in the IAM policy of the application group                                |
| C | Nesting network stacks within application stacks simplifies management and debugging, but requires resource-level permissions in the network group's IAM policy |
| D | The application stack cannot be deleted before all network stacks are deleted                                                                                   |

## Working through the problem
`A` is **valid**. CloudFormation can't delete a subnet that has dependencies. Existing EC2 instances depend on a
subnet. **To delete a subnet, the EC2 instances created in that subnet have to be terminated first**.

`B` is **valid**. To make sure that application operators can only launch EC2 instances into VPCs managed by network
administrators, those VPCs have to be mentioned in the **IAM policy attached to the application group** by **ARN** like so:
```json
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Action": "ec2:RunInstances",
    "Resource": "arn:aws:ec2:{$region:account-id}:subnet/*",
    "Condition": {
      "ArnEquals": {
        "ec2:Vpc": "arn:aws:ec2:{$region:account-id}:vpc/{$vpc-id}"
      }
    }
  }
}
```
And when `Resource` is used in a policy with `arn:`, it is a **resource level permission**

See [](AWS-IAM-Account-level-permissions-vs-Resource-level-Permissions.topic)

`C` is incorrect.

First, when nesting stacks, the dependent stack (application) is nested inside the base stack (network), not the other
way around.

Second, this doesn't necessarily require resource level permissions in the network group's IAM policy.


Even if you used the wrong nesting (network stack inside application stack), the combined stack would be deployed using
application group permissions. So actually, in that case, the application group would require account-level permissions of the network group.

`D` is incorrect. 

Application stacks are children of the network stacks. Network stacks are foundational stacks.

Therefore, application stacks **CAN BE DELETED** before network stacks.