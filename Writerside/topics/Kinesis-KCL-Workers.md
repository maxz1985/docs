# Kinesis KCL Workers

## Where do they run?

A `KCL worker` is a program. You can run it on any compute resource:
* `Amazon EC2` instances (a common choice for scalable deployments).
* `AWS Fargate` or `ECS` (if you want containerized workers).
* `AWS Lambda` (if your processing logic is lightweight, although you wouldn’t technically use KCL here; you would use `Kinesis Lambda triggers` instead).
* Even on-premises or another cloud, if desired, as long as it has connectivity to AWS.

The worker connects to the Kinesis stream specified in its configuration.

## KCL output and use cases
| **Destination**              | **Use Case**                                                |
|------------------------------|-------------------------------------------------------------|
| **Amazon S3**                | Long-term storage, batch processing by `EMR`, or data lakes |
| **DynamoDB**                 | Low-latency querying and dynamic dashboards                 |
| **RDS**                      | Relational processing and transactional queries             |
| **Redshift**                 | Analytical processing and business intelligence             |
| **Elasticsearch/OpenSearch** | Real-time search and monitoring                             |
| **Kinesis Data Streams**     | Multi-step stream processing pipelines                      |
| **Kinesis Firehose**         | Direct delivery to `S3`, `Redshift`, or `Elasticsearch`     |
| **CloudWatch**               | Monitoring, logging, and metrics                            |
| **SQS**                      | Decoupling tasks, buffering, and downstream processing      |
| **External Systems**         | Integrations with on-premises or third-party services       |
| **Local File System**        | Temporary storage during testing or debugging               |
| **Message Queues**           | Further asynchronous processing                             |
| **SNS**                      | Real-time notifications and alerts                          |
