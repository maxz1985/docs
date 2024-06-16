# SAML Federated Access to AWS API

> A pre-requisite for this is to have a [](SAML-Identity-Provider-in-IAM.md)
>
{style="note"}

## Scenario
You need to provide users, already **authenticated** by your own corporate SAML2.0 identity provider (IdP),
**federated access to AWS API**.

(Also allows access to AWS Console, but not its primary goal.)

![saml-federated-access-to-aws-api.png](saml-federated-access-to-aws-api.png)

1. Users make a request to IdP and get authenticated
2. IdP sends a SAML assertion to users
3. Users forward this SAML assertion to STS using `AssumeRolewithSAML` API
4. STS returns a temporary security credential that allows users to access the Amazon S3 bucket

[Using SAML-based federation for API access to AWS](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_saml.html#CreatingSAML-configuring)