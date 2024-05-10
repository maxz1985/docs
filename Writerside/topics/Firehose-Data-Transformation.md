# Firehose Data Transformation

```mermaid
flowchart LR
producer --record--> lambda_buffer
lambda_buffer --buffered batch of records--> lambda
lambda --transformed records--> destination_buffer
destination_buffer -->S3

subgraph firehose 
    lambda_buffer
    destination_buffer
end
```