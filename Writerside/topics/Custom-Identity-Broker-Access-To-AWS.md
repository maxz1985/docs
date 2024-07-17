# Custom Identity Broker Access To AWS

You can write a Custom Identity Broker
to create a URL that lets users who log in to your organization's network securely access the AWS Management Console.
* User authenticates to your own local identity system
* Broker verifies that user is authenticated
* Broker calls `AssumeRole` API (STS) to get temporary security credentials. `GetFederationToken` API can also be used but `AssumeRole` is the recommended way.
* Broker calls AWS federation endpoint with temporary credentials to get a sign-in token
* Broker constructs a URL for AWS Console that includes the sign-in token
* URL can be used to access AWS

Useful when you have to give users authenticated by **your own** identity provider access to AWS under a designated role.

If your organization uses a SAML-compatible Identity provider, no broker is required.

`DecodeAuthorizationMessage` API decodes additional information about the authorization status of a request.
Doesn't get tokens or credentials.

SAML2 can support roles.