# Example Policy
The policy below will allow users to perform read-only activities on the `EC2` instances and `Auto-Scaling Groups`.

`Describe` is the minimal read-only permission.

Note that `CloudWatch` permissions should be added as a good measure for read-only access to logs. 
```json
{  "Version": "2012-10-17",  
   "Statement": [    
    {      
        "Sid": "VisualEditor0",     
        "Effect": "Allow",      
        "Action": [
            "ec2:Describe*"      
        ],      
        "Resource": "*"    
    },    
    {      
        "Sid": "VisualEditor0",
        "Effect": "Allow",
        "Action": [
            "autoscaling:Describe*"
        ],      
        "Resource": "*"    
    },
    {      
        "Sid": "VisualEditor0",
        "Effect": "Allow",
        "Action": [
            "cloudwatch:ListMetrics",
            "cloudwatch:Describe*",
            "cloudwatch:GetMetricStatistics"
        ],
        "Resource": "*"
    } 
    ]      
}
```