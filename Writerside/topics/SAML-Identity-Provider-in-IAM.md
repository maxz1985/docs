# SAML Identity Provider in IAM

## Scenario
You want to **establish trust between your SAML-compatible IdP** (such as 
Shibboleth or Active Directory Federation Services) **and AWS**, 
so that **users in your organization authenticated by your IdP can access AWS resources**.

> This is a prerequisite for both [](SAML-Federated-Access-to-AWS-Console.md)and [](SAML-Federated-Access-to-AWS-API.md)
> 
{style="note"}

## Implementation

To configure IAM Identity Service based on third-party SAML IdP:

1. Get the SAML Metadata Document from IdP 
2. Create a SAML Identity Provider Object in AWS IAM Console. 
3. Configure SAML IdP with relying party trust 
4. In IdP, configure SAML assertions for the authentication response

[Create a SAML identity provider in IAM](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_create_saml.html)

To determine what federated users can do in AWS, create an IAM role.