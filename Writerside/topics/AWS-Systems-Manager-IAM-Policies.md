# Systems Manager IAM Policies

Suppose you need an _IAM policy_ to allow an application to decrypt _CMK-encrypted SecureStrings_
stored in _Systems Manager Parameter Store_. We should only allow access to parameters that start with `prod-`

The key `Action` for this policy would be `ssm:GetParameters` or `ssm:GetParameter` or `ssm:GetParameter*`

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ssm:GetParameters"
            ],
            "Resource": [
                "arn:aws:ssm:us-east-2:123456789012:parameter/prod-*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "kms:Decrypt"
            ],
            "Resource": [
                "arn:aws:kms:us-east-2:123456789012:key/4914ec06-e888-4ea5-a371-5b88eEXAMPLE"
            ]
        }
    ]
}
```
* Action: `ssm:GetParameter` – permits caller to use `GetParameter` ssm API submitting a parameter name to get its value. **One parameter per call**
* Action: `ssm: GetParameters` – permits caller to use `GetParameters` ssm API submitting a **list** of parameter names to get their values. **Multiple parameter names per call.**
* Action: `ssm:GetParameter*` – permits caller to use **EITHER** `GetParameter` or `GetParameters`