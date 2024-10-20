# Custom Identity Broker Federated Access to AWS

## Scenario
Users authenticated with your **non-SAML, non-OIDC compatible** on-prem IdP (Id Provider) need access to AWS (Both Console and API)

Typical use case is federating with on-prem Classic AD (no ADFS).

## Implementation

Use Custom Identity Broker

![custom-identity-broker-federated-access-to-aws-01.png](custom-identity-broker-federated-access-to-aws-01.png) {thumbnail="true"}

### Prerequisites

* The identity broker application has permissions to access IAM's token service (STS) API to create temporary security credentials. Either: 
  * **IAM user or role** the broker app will be running AS when calling `AssumeRole`
  * **IAM user** the broker app will be running AS when calling `GetFederationToken` (old style, not recommended)
* The identity broker application is able to verify that employees are authenticated within the existing authentication system. 
* Users are able to get a temporary URL that gives them access to the AWS Management Console (which is referred to as single sign-on).
* IAM Role or Roles that broker will be getting STS credentials for (Roles to be assumed for the users)

### Steps

* Set up your Identity Provider (IdP) for Federated access
* Broker authenticates users with your IdP using corporate data store or Active Directory Attributes
* Broker gets STS credentials for the user by calling `AssumeRole` or `GetFederationToken`
* Broker passes the temporary credentials to corporate user