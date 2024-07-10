# EBS Volumes

| Volume type                                  | Use case                                                                                                                                                                                  | Max IOPS/Volume     |
|----------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------|
| EBS Provisioned IOPS SSD (io2 Block Express) | Largest, most I/O intensive, mission critical deployments of NoSQL and relational databases such as Oracle, SAP HANA, Microsoft SQL Server, and SAS Analytics                             | 256,000             |
| EBS Provisioned IOPS SSD (io1)               | I/O-intensive NoSQL and relational database. **ex** Cassandra requiring **extremely low latency** and high performance                                                                    | 64,000              |
| EBS General Purpose SSD (gp3)                | Virtual desktops, medium sized single instance databases such as Microsoft SQL Server and Oracle, latency sensitive interactive applications, boot volumes, and dev/**test environments** | 16,000 (1,000 MB/s) |
| EBS General Purpose SSD (gp2)                | Virtual desktops, medium sized single instance databases such as Microsoft SQL Server and Oracle, latency sensitive interactive applications, boot volumes, and dev/test environments     | 16,000   (250 MB/s) |
| Throughput Optimized HDD (st1)               | Big data, **data warehouses**, log processing                                                                                                                                             | 500                 |
| Cold HDD (sc1)                               | **Colder data** requiring fewer scans per day                                                                                                                                             | 250                 |

[Amazon EBS features](https://aws.amazon.com/ebs/features/)