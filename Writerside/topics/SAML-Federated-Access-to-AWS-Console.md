# SAML Federated Access to AWS Console
> A pre-requisite for this is to have a [](SAML-Identity-Provider-in-IAM.md)
> 
{style="note"}

## Scenario
You need to provide users, already **authenticated** by your own corporate SAML2.0 identity provider (IdP), 
federated access to AWS console with **automatic opening of AWS Console page**.

![saml-federated-access-to-aws-console.png](saml-federated-access-to-aws-console.png) {thumbnail="true"}

[Enabling SAML 2.0 federated users to access the AWS Management Console](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_enable-console-saml.html)

> This specific use of SAML differs from the more general one illustrated at
> [SAML 2.0 federation](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_saml.html)
> because **this workflow opens the AWS Management Console on behalf of the user**.
> This requires the use of the **AWS sign-in endpoint**
> (ex. `us-east-2.signin.aws.amazon.com`) instead of directly calling the AssumeRoleWithSAML API.
> The endpoint calls the API for the user and returns a URL that automatically redirects the user's browser to the 
> AWS Management Console.
> 
{style="note"}