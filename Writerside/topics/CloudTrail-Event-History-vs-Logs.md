# CloudTrail Event History vs. Logs

You may encounter references to both `Cloudtrail Event History` and `CloudTrail Logs` in AWS Documentation.

Here is the difference between the 2

| Aspect            | CloudTrail Event History                       | CloudTrail Logs                                           |
|-------------------|------------------------------------------------|-----------------------------------------------------------|
| Retention Period  | Last 90 days                                   | As long as configured in S3 bucket policies               |
| Storage Location  | CloudTrail service (not user-configurable)     | Amazon S3 bucket specified by user                        |
| Event Types       | Management events                              | Management events and optionally data events              |
| Search and Filter | Available in AWS Management Console, CLI, SDKs | Can be queried using Amazon Athena, AWS Glue, etc.        |
| Accessibility     | Immediate access via CloudTrail console        | Access via S3, can be analyzed with other AWS services    |
| Customization     | Limited to default settings                    | Highly customizable: can include all regions, event types |
| Use Case          | Quick inspection and troubleshooting           | Long-term audit, compliance, detailed analysis            |
