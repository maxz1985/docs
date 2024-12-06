# Custom Identity Broker Federated Access to AWS

## Scenario
Users authenticated with your **non-SAML, non-OIDC compatible** on-prem IdP (Id Provider) need access to AWS (Both Console and API)

Typical use case is federating with on-prem Classic AD (no ADFS).

## Implementation

Use Custom Identity Broker

![custom-identity-broker-federated-access-to-aws-01.png](custom-identity-broker-federated-access-to-aws-01.png) {thumbnail="true"}

### Prerequisites
>When the Custom Identity Broker runs on-prem, use TPM, HSM or HashiCorp Vault to store **broker's own IAM credentials**

* The identity broker application has permissions to access IAM's token service (STS) API to create temporary security credentials. Either: 
  * **IAM user or role** the broker app will be running AS when calling `AssumeRole`
  * **IAM user** the broker app will be running AS when calling `GetFederationToken`
* The identity broker application is able to verify that employees are authenticated within the existing authentication system. 
* Users are able to get a temporary URL that gives them access to the AWS Management Console (which is referred to as single sign-on).
* IAM Role or Roles that broker will be getting STS credentials for (Roles to be assumed for the users)

### Steps

* Set up your on-prem Identity Provider (IdP) for Federated access
* **Broker** authenticates users **against your IdP (LDAP)** using corporate data store or Active Directory Attributes
* Broker gets STS credentials for the user by calling `AssumeRole`(`arg:` `RoleArn`) or `GetFederationToken`(`arg:` `FederatedUserName`, `arg:` `PolicyJSON`)
* AWS STS returns temporary credentials to the broker
* Broker passes the temporary credentials to corporate user

## `AssumeRole` vs `GetFederationToken`
| **Aspect**                  | **AssumeRole**                                                                               | **GetFederationToken**                                                                                  |
|-----------------------------|----------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|
| **Broker Credentials**      | AWS credentials (IAM User or IAM Role) with permission to call `AssumeRole` for target roles | AWS credentials of an IAM User with permission to call `GetFederationToken` and pass specified policies |
| **Permission Source**       | IAM Role policies                                                                            | Policy provided in API call                                                                             |
| **Identity Representation** | Assumed Role                                                                                 | Federated User                                                                                          |
| **Session Duration**        | Up to 12 hours                                                                               | Up to 36 hours                                                                                          |
| **Policy Size Limit**       | 2,048 characters (session policy)                                                            | 2,048 characters                                                                                        |
| **Use Case**                | Role-based access control                                                                    | User-specific permissions                                                                               |
| **Logging Identity**        | Assumed role's ARN                                                                           | Federated user's name                                                                                   |
| **Caller Identity**         | Role ARN (original caller is in metadata)                                                    | Federated user's name                                                                                   |

### Permission policy example for `AssumeRole`
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "sts:AssumeRole",
      "Resource": [
        "arn:aws:iam::123456789012:role/AdminRole",
        "arn:aws:iam::123456789012:role/OperatorRole",
        "arn:aws:iam::123456789012:role/ReadOnlyRole"
      ]
    }
  ]
}
```
> Wildcarding the resource like `"Resource": "*"` is possible but not recommended as it creates an overly permissive policy.

> Each **Role** mentioned in `Resource` section should have a **Trust Policy** attached to it that **allows** this identity broker to **assume** the role.
> 
> Trust policy example:
> 
{style="note"}
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::123456789012:user/IdentityBroker"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```
### Permission policy example for `GetFederationToken`
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "sts:GetFederationToken",
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": "*",
      "Resource": "*"
    }
  ]
}
```
### Permissions for `GetFederatedToken`
The federated user's effective permissions are the intersection of:
1. The policy attached to the IAM user making the GetFederationToken call. (Policy attached to Custom Broker)
2. The policy provided in the Policy parameter of the GetFederationToken call.

This means that the federated user can't gain more permissions than the IAM user (identity broker) already possesses.

This mechanism is crucial for maintaining the principle of least privilege and preventing privilege escalation.