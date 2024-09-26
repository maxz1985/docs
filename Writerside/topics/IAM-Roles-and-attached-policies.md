# IAM Roles and attached policies

There are three types of policies that can be attached to an IAM Role.

| Policy Type                               | Description                                                                                                                                                                                                                                                                                   |
|-------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Identity-Based Policy (Permission Policy) | These policies define the permissions that the user of the role is able to perform (or is denied from performing), and on which resources.                                                                                                                                                    |
| Trust Policy                              | The trust policy defines which principals can assume the role, and under which conditions. A trust policy is a specific type of resource-based policy for IAM roles.                                                                                                                          |
| Permission Boundary                       | A permissions boundary is an advanced feature for using a managed policy to set the maximum permissions for a role. A principal’s permissions boundary allows it to perform only the actions that are allowed by both its identity-based permissions policies and its permissions boundaries. |


Example **Identity-Based** (permission) policy:

Allow read access to S3.
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::example-bucket/*"
    }
  ]
}
```

Example **Trust** policy:

Allow Google Web Identity Provider to perform `AssumeRoleWithWebIdentity` for IAM Web Identity Federation
```json
{
  "Version": "2012-10-17",
  "Statement": {
    "Effect": "Allow",
    "Principal": {
      "Federated": "arn:aws:iam::123456789012:oidc-provider/accounts.google.com"
    },
    "Action": "sts:AssumeRoleWithWebIdentity",
    "Condition": {
      "StringEquals": {
        "accounts.google.com:aud": "your-client-id"
      }
    }
  }
}
```

Example **Permission Boundary** policy

> **Permission Boundary** Doesn't Grant Permissions on Its Own
> 
> A permission boundary doesn't grant permissions by itself. _The role or user must still have identity-based policies granting the required actions_. The permission boundary only restricts what actions can be allowed by those policies.
> 
{style="warning"}

The resulting set of permissions for the role is what is allowed by BOTH `Permission Policy` and `Trust Policy`
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:PutObject",
        "s3:GetObject",
        "s3:DeleteObject"
      ],
      "Resource": "arn:aws:s3:::example-bucket/*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "dynamodb:PutItem",
        "dynamodb:GetItem",
        "dynamodb:DeleteItem"
      ],
      "Resource": "arn:aws:dynamodb:us-west-2:123456789012:table/example-table"
    }
  ]
}
```