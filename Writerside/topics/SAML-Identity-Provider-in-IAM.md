# SAML Identity Provider in IAM

## Scenario
You want to **establish trust between your SAML-compatible IdP** (such as 
Shibboleth or Active Directory Federation Services) **and AWS**, 
so that **users in your organization authenticated by your IdP can access AWS resources**.

> This is a prerequisite for both [](SAML-Federated-Access-to-AWS-Console.md)and [](SAML-Federated-Access-to-AWS-API.md)
> 
{style="note"}

> Take note in terminology specifics.
> * `LDAP` or `On-prem IdP` – An entity that verifies a user's identity and provides authentication details to a service provider (like AWS) using a standard like SAML.
> * `AWS IAM IdP` – refers to a configured entity within IAM that allows users to federate access from an external identity source like LDAP, Active Directory, or another SAML-compatible system.

> Contrast this `AWS IAM Identity Provider` approach with [Custom Identity Broker](Custom-Identity-Broker-Access-To-AWS.md) approach. 
> 
>`Custom Identity Broker` or **the broker you created** is primarily intended for non-SAML federated scenarios such as Classic AD (not ADFS).
> 
>Custom Identity Broker runs as an **IAM User** that is allowed to assume roles for federated users. It doesn't require an `AWS IAM IdP`.
{style="note"}
## Implementation

To configure IAM Identity Service based on third-party SAML IdP:

1. Get the SAML Metadata Document from IdP 
2. Create a SAML Identity Provider Object in AWS IAM Console. 
3. Configure SAML IdP with relying party trust (tell the SAML IdP about AWS as a service provider)
4. In IdP, configure SAML assertions for the authentication response

[Create a SAML identity provider in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_saml.html)

To determine what federated users can do in AWS, create an IAM role or roles.