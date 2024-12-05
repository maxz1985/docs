# IAM Roles For SAML Federation

In order for federated users to perform actions in AWS, they have to assume an **IAM Role**.
> If there is a requirement to use different levels of access like `Dev` and `Test` – create a `Permission Policy` and `IAM Role` for each group.
> 
> Each Role will have the **same** Trust Policy but **its own** Permission Policy

The roles SAML users assume have to have:
* A `Trust Policy` that allows the `IAM IdP` to **authenticate** users.
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
> * The `Principal` of the Trust Policy is the `ARN` of the `SAML Provider` that you set up in [](SAML-Identity-Provider-in-IAM.md)
> * The `Action` allowed is `sts:AssumeRoleWithSAML`
> * Even though `SAML Provider` is the `Principal` of the trust policy, it doesn't assume the role, only authenticates federated user. This is specific to a trust policy used for federation.

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
## SAML Assertion

Configure your `External On-Prem IdP` to send required **attributes** in the SAML assertion.

For the Permission Policy above, we should send the `username` attribute. 

When using multiple IAM Roles based on user's group membership,
send `Role` attribute indicating which role the user is allowed to assume.
```xml
<saml:AttributeStatement>
    <!-- User's Username -->
    <Attribute Name="https://aws.amazon.com/SAML/Attributes/PrincipalTag:username">
        <AttributeValue>john.doe</AttributeValue>
    </Attribute>
    <!-- User's Role -->
    <Attribute Name="https://aws.amazon.com/SAML/Attributes/Role">
        <AttributeValue>arn:aws:iam::AWS_ACCOUNT_ID:role/DevRole,arn:aws:iam::AWS_ACCOUNT_ID:saml-provider/YOUR_SAML_PROVIDER_NAME</AttributeValue>
    </Attribute>
</saml:AttributeStatement>
```
## Create a role

Finally, create a role like `S3FederatedSAMLAccessDev`
and attach **both** the `Trust Policy` and the`Permission Policy` to the role.

User calls `AssumeRoleWithSAML` providing the following parameters: (`SAML Assertion`, `ARN of IAM Role to assume`, `ARN of the SAML Provider`)

The `SAML Assertion` comes from the `External On-Prem IdP` after successful authentication.
```bash
aws sts assume-role-with-saml \
  --role-arn arn:aws:iam::123456789012:role/DevRole \
  --principal-arn arn:aws:iam::123456789012:saml-provider/YourSAMLProvider \
  --saml-assertion file://saml_assertion.txt

```