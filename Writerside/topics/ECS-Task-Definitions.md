# ECS Task Definitions

ECS Task Definitions consist of the following parts

> It is possible to define **multiple** `Containers` in a task.
> 
> The practical usage would be like _web server_ + _logging container_ + _message queue processor_
> 
> Containers in the same task can communicate with each other using `localhost` and access the same `volumes`
> 
{style="note"}

| Part                       | Mandatory?   | Possible Values                    | Description                                                                                                                                            |
|----------------------------|--------------|------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------|
| Task Family                | **Required** | Any string (family name)           | The name of the task definition family that groups multiple versions of the task definition. Used to organize and manage task definitions efficiently. |
| IAM Task Role              | (Optional)   | ARN of an IAM role                 | The IAM role that grants permissions to the containers in the task to access AWS services securely without embedding credentials in your code.         |
| Network Mode               | (Optional)   | `none`, `bridge`, `host`, `awsvpc` | The Docker networking mode to use for the containers in the task. Determines how networking is configured for your containers.                         |
| Container Definitions      | **Required** | List of container definitions      | Specifies the containers to run in the task, including settings like image, CPU, memory, port mappings, and environment variables.                     |
| Volumes                    | (Optional)   | List of volume definitions         | Defines data volumes that can be used by containers in the task for persistent storage or sharing data between containers.                             |
| Task Placement Constraints | (Optional)   | `distinctInstance`, `memberOf`     | Rules that govern how tasks are placed on instances in your cluster, such as ensuring tasks run on separate instances (`distinctInstance`).            |
| Launch Types               | (Optional)   | `EC2`, `FARGATE`                   | The type of infrastructure on which to run your tasks. `EC2` uses your own Amazon EC2 instances; `FARGATE` is serverless compute managed by AWS.       |

## Task Network Modes

| Network Mode | Description                                                                                                                                                                                                                                                                                                                               |
|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `bridge`     | The default Docker network mode.                                                                                                                                                                                                                                                                                                          |
| `awsvpc`     | Each task gets its own elastic network interface (ENI) and a primary private IP address.<br/>This mode enables attaching the task to VPC subnets, security groups, and other network settings.<br/>The attachment is specified in [ECS Service Definition's Network Configuration](ECS-Service-Definition.md) when launching the cluster. |
| `host`       | Bypasses Docker's built-in network isolation. A `Direct-to-Host` network connection. In this mode multiple instances of the task **cannot run on the same EC2 host**                                                                                                                                                                      |
| `none`       | No networking for the task.                                                                                                                                                                                                                                                                                                               |