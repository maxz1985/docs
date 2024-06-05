# Export EC2 to S3 as VM Image

## Prepare S3 Bucket

[Exporting an instance as a VM using VM Import/Export](https://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html "Exporting an instance as a VM using VM Import/Export")

S3 bucket has to be created using `ACL Enabled` Object Ownership mode.

![export-ec2-to-s3-as-vm-image-01.png](export-ec2-to-s3-as-vm-image-01.png) {thumbnail="true"}

Once bucket is created, add permissions for the AWS Global Identity to write to it:
 
<ui-path>`{bucket name} -> Permissions -> ACL`, `Edit`</ui-path>

Add an external account with the canonical ID
```Shell
c4d8eabf8db69dbe46bfe0e517100c554f01200b104d59cd408e777ba442a322
```
and permissions `Object : Write` and `Bucket ACL: Read`

![export-ec2-to-s3-as-vm-image-02.png](export-ec2-to-s3-as-vm-image-02.png){thumbnail="true"}

## Create an `export.json` file

The `export.json` file will define the type of export you're requesting and destination
File content:
```json
{
  "ContainerFormat": "ova",
  "DiskImageFormat": "VMDK",
  "S3Bucket": "ec2-export-image",
  "S3Prefix": "max-al2"
}
```

Here we export to `ova` format using the `VMDK` virtual disk image format, to the S3 Bucket we previously created under the folder named `max-al2`.

## Run `ec2 create-instance-export-task` command

Get the instance ID of the EC2 you're exporting, then:

```shell
aws ec2 create-instance-export-task --instance-id i-0c3baa39c411bff1f --target-environment vmware --export-to-s3-task file://export.json
```

Response:

```json
{
    "ExportTask": {
        "ExportTaskId": "export-i-00098dc9fd91f4b84",
        "ExportToS3Task": {
            "ContainerFormat": "ova",
            "DiskImageFormat": "vmdk",
            "S3Bucket": "ec2-export-image",
            "S3Key": "max-al2export-i-00098dc9fd91f4b84.ova"
        },
        "InstanceExportDetails": {
            "InstanceId": "i-0c3baa39c411bff1f",
            "TargetEnvironment": "vmware"
        },
        "State": "active"
    }
}
```

## Check on the task's progress

Get the `"ExportTaskId": "export-i-00098dc9fd91f4b84"` from the output of export task command above.

Run:

```shell
 aws ec2 describe-export-tasks --export-task-ids export-i-00098dc9fd91f4b84
```

Response:

```json
{
    "ExportTasks": [
        {
            "ExportTaskId": "export-i-00098dc9fd91f4b84",
            "ExportToS3Task": {
                "ContainerFormat": "ova",
                "DiskImageFormat": "vmdk",
                "S3Bucket": "ec2-export-image",
                "S3Key": "max-al2export-i-00098dc9fd91f4b84.ova"
            },
            "InstanceExportDetails": {},
            "State": "active"
        }
    ]
}
```

The export can take quite some time. Once the task is done, you will see the `"State": "completed"` in the output.