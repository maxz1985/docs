# Loading Data into OpenSearch

## From Kinesis Data Firehose
Can be configured to DIRECTLY ingest data into OpenSearch

## From S3
Configure Kinesis Data Firehose to read S3,then have Firehose ingest into OpenSearch

OR

Use a Lambda function to ETL data into OpenSearch

## From CloudWatch
OpenSearch has Direct CloudWatch integration.

OR

Forward logs to Kinesis Data Firehose, then use `Firehose` to ingest into OpenSearch

OR

Use a Lambda function to ETL data into OpenSearch



