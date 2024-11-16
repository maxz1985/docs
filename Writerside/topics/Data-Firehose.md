# Data Firehose

Amazon Data Firehose (former Kinesis Data Firehose) is an **ETL Service** for ingesting and delivering large volumes of data.
**Doesn't store the data it is processing**

## Sources and Destinations

```mermaid
flowchart LR
    kinesis_data_streams[Kinesis Data Streams] --> data_firehose
    msk[Amazon MSK Managed Streaming for Kafka] --> data_firehose
    api[FireHose `Direct PUT API`] --> data_firehose
    cloudwatch[CloudWatch Logs] --> data_firehose
    waf[AWS WAF] --> data_firehose
    network_firewall[AWS Network Firewall Logs] --> data_firehose
    sns[Amazon SNS] --> data_firehose
    iot[AWS IoT] --> data_firehose

    data_firehose[Amazon Data Firehose]
    
    data_firehose --> s3[Amazon S3]
    data_firehose --> redshift[Amazon Redshift]
    data_firehose --> open_search[Amazon OpenSearch Service]
    data_firehose --> snowflake[SnowFlake]
    data_firehose --> iceberg[Apache Iceberg Tables]
    data_firehose --> http[HTTP endpoints]
    data_firehose --> datadog[DataDog]
    data_firehose --> new_relic[New Relic]
    data_firehose --> mongo[MongoDB]
    data_firehose --> splunk[Splunk]
```

## Save Kinesis Streams Data to S3

`Kinesis Data Streams` can be saved to `S3` for backup and archiving purposes using `Data Firehose` with a **Delivery Stream**

![data-firehose-01.png](data-firehose-01.png) {thumbnail="true"}

Alternatively, you can use a *Consumer* which is an `Amazon Kinesis Data Streams Application` that you develop to 
read data from the streams and output to a specific destination.

One of those consumers is [`Amazon Kinesis S3 Connector`](https://github.com/amazon-archives/amazon-kinesis-connectors?tab=readme-ov-file) 

![data-firehose-02.png](data-firehose-02.png) {thumbnail="true"}

## High-Level Architecture of Kinesis _Data Streams_

`Data Streams` is for **real-time** and is capable of employing complex data processing logic.

**Stores data in a stream for a set period of time**

![data-firehose-03.png](data-firehose-03.png){thumbnail="true"}