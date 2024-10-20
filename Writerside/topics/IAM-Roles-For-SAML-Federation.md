# IAM Roles For SAML Federation

In order for federated users to perform actions in AWS, they have to assume an **IAM Role**.
The roles SAML users assume have to have:
* A `Trust Policy` that allows the `IAM IdP` to assume the roles on their behalf.
* A `Permission Policy` which determines what the user can do in AWS.

## Trust Policy
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowSAMLProviderToAssumeRole",
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::123456789012:saml-provider/YourSAMLProvider"
      },
      "Action": "sts:AssumeRoleWithSAML",
      "Condition": {
        "StringEquals": {
          "SAML:aud": "https://signin.aws.amazon.com/saml"
        }
      }
    }
  ]
}
```
NOTE:
* The `Principal` of the Trust Policy is the `ARN` of the IAM IdP that you set up in [](SAML-Identity-Provider-in-IAM.md)
* The `Action` allowed is `sts:AssumeRoleWithSAML`

## Permission Policy (access S3 bucket matching username)
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowListingOfUserBucket",
      "Effect": "Allow",
      "Action": "s3:ListBucket",
      "Resource": "arn:aws:s3:::${aws:PrincipalTag/username}"
    },
    {
      "Sid": "AllowFullAccessToUserBucket",
      "Effect": "Allow",
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::${aws:PrincipalTag/username}",
        "arn:aws:s3:::${aws:PrincipalTag/username}/*"
      ]
    }
  ]
}
```
NOTE:
For this to work, you on-prem SAML IdP should be configured to send `username` attribute in the SAML Assertion
```xml
<!-- User's Username -->
<Attribute Name="https://aws.amazon.com/SAML/Attributes/PrincipalTag:username">
  <AttributeValue>john.doe</AttributeValue>
</Attribute>
```
## Create a role

Finally, create a role like `S3FederatedSAMLAccess` and attach **both** the `Trust Policy` and `Permission Policy` to the role.

NOTES: When a user makes an `AssumeRoleWithSAML` call, one of the arguments will be the `SAML Assertion` which contains `username` and `Role` attributes.

The `SAML Assertion` comes from the on-prem IdP after successful authentication.

