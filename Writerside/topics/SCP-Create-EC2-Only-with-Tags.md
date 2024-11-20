# SCP Create EC2 Only with Tags

The following SCP allows EC2 creation ONLY if one of the specific tags is present:
```json
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Action": "ec2:RunInstances",
    "Resource": "*",
    "Condition": {
      "ForAllValues:StringEquals": {
        "aws:TagKeys": [
          "department",
          "costcenter",
          "manager"
        ]
      }
    }
  }
}
```
>NOTE the usage of `ForAllValues:StringEquals` and `aws:TagKeys`
> 
{style="note"}

Here is another SCP that requires tagging of ALL resources created:
```JSON
{
    "Version": "2012-10-17",
    "Statement": {
        "Effect": "Allow",
        "Action": "organizations:*",
        "Resource": "*",
        "Condition": {
            "ForAllValues:StringEquals": {
                "aws:TagKeys": [
                    "department",
                    "costcenter",
                    "manager"
                ]
            }
        }
    }
}
```