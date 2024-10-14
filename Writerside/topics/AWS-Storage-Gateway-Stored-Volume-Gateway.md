# AWS Storage Gateway Stored Volume Gateway

![aws-storage-gateway-stored-volume-gateway-01.png](aws-storage-gateway-stored-volume-gateway-01.png) {thumbnail="true"}

`Gateway` is **a software** running on a machine (EC2, VM, or on-prem), which is presenting an `ISCSI` target to clients.
 
`Gateway` is using _its own local storage_ to store volume data. It also **asynchronously replicates** the volume content
to `S3` in the form of **EBS Snapshots**

> When designing AWS disaster recovery strategy for on-prem applications:
> * convert the EBS snapshot stored in S3 to an EBS Volume
> * create an EC2 instance to replace the on-prem server which was using the storage gateway volume
> * attach the EBS Volume to the EC2
