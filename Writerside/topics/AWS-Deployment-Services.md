# AWS Deployment Services

## Deployment Models

| Method             | Description                                                                                                                                                                                                                                                       |
|--------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| In-Place Upgrade   | involves performing application updates on live Amazon EC2 instances.                                                                                                                                                                                             |
| Disposable Upgrade | involves rolling out a new set of EC2 instances by terminating older instances.                                                                                                                                                                                   |
| Blue-Green         | you have two identical stacks of your application running in their own environments.                                                                                                                                                                              |
| Hybrid             | using different services to deploy different parts of application.<br/>For example, you can combine the simplicity of managing AWS infrastructure provided by Elastic Beanstalk and the automation of custom network segmentation provided by AWS CloudFormation. |

## Deployment Services

* AWS Elastic Beanstalk
* AWS CloudFormation
* AWS OpsWorks (EOL May 26, 2024 – migrate to Systems Manager + CloudFormation)
* AWS CodeDeploy
* AWS CodeCommit
* AWS CodePipeline
* Amazon EC2 Container Service

| Deployment Feature            | Category                                            | Description                                              | AWS Elastic Beanstalk | AWS CloudFormation | AWS OpsWorks | AWS CodeDeploy |
|-------------------------------|-----------------------------------------------------|----------------------------------------------------------|-----------------------|--------------------|--------------|----------------|
| Provision                     | Provision infrastructure                            | EC2 instances, EBS volumes, VPC, etc.                    | ✓                     | ✓                  | ✓            |                |
| Deploy                        | Deploy applications                                 | Deploy applications from chosen repository               | ✓                     | ✓                  | ✓            | ✓              |
| Configure                     | Configuration management                            | Install software, configure software and AWS resources   | ✓                     | ✓                  | ✓            |                |
| Scale                         | Monitoring                                          | Automatically scale to handle the load                   | ✓                     | ✓                  | ✓            |                |
| Monitoring                    | Monitoring                                          | Monitor events, resources, applications health           | ✓                     | ✓                  | ✓            | ✓              |
| Logging                       | Troubleshooting, security                           | System, application logs                                 | ✓                     | ✓                  | ✓            | ✓              |
| Instance profiles             | Security                                            | Securely access AWS services such as Amazon S3, DynamoDB | ✓                     | ✓                  | ✓            | ✓              |
| Custom variables              | Configuration management                            | Pass variables to application environments               | ✓                     | ✓                  | ✓            |                |
| Other AWS service integration | Service integration                                 | Integration with other AWS services                      | ✓                     | ✓                  | ✓            | ✓              |
| Tags                          | Security, troubleshooting, configuration management | Automate configuring tags on EC2, Amazon RDS             | ✓                     | ✓                  | ✓            |                |


