# SAML vs OAuth 2.0 vs. OpenID Connect (OIDC)

| Protocol            | SAML                                                                                                                                              | OAuth                                                                                          | OpenId (OIDC)                                                                               |
|---------------------|---------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| What is it?         | Open standard for **authentication** and **authorization**                                                                                        | Open standard for **authorization**                                                            | Open standard for **authentication**                                                        |
| History             | Developed by OASIS in 2001                                                                                                                        | Developed by Twitter and Google in 2006                                                        | Developed by OpenID foundation in 2014                                                      |
| Current version     | 2.0 Released in 2005                                                                                                                              | 2.0 Released in 2012                                                                           | OpenId Connect 1.0 released in 2014                                                         |
| Purpose             | Allows 2 web entities to exchange authentication and authorization data                                                                           | Enables delegated authorization for internet resources                                         | Provides authentication layer over OAuth2.0                                                 |
| Use case            | To allow use of SSO to access a web service. Typically used in corporate environments to access multiple websites using a single corporate login. | To provide temporary resource access to a 3rd party application on behalf of a legitimate user | To authenticate users to your web or mobile app without requiring them to create an account |
| Primary use case    | SSO for enterprise apps                                                                                                                           | API authentication                                                                             | SSO for consumer apps                                                                       |
| Format              | XML                                                                                                                                               | JSON                                                                                           | JSON                                                                                        |
| Supported Protocols | HTTP, SOAP, and anything that can transport XML                                                                                                   | HTTP                                                                                           | XRDS, HTTP                                                                                  |

## SAML
SAML stands for Security Assertion Markup Language,
and it is an XML-based standard for **authentication** and **authorization**.
SAML enables single sign-on (SSO),
which means users can log in once to a central identity provider (IdP)
and access multiple service providers (SPs) without entering their credentials again.

For example, you can use SAML to log in to your company's intranet and access various applications and services.

SAML **uses assertions** to exchange information about the user's identity,
attributes, and permissions between the IdP and the SPs

## OAuth 2.0
OAuth is an open standard for **authorization**
that allows users
to grant access to their resources on one platform to another platform without sharing their credentials.

For example, you can use OAuth to let a third-party app access your Google Drive files or your Facebook photos.

OAuth **uses tokens** to represent the scope and duration of the access granted by the user.

_OAuth is not an authentication protocol_,
meaning it doesn't verify the identity of the user,
but _it relies on other methods, such as `OpenID Connect`, to do so_.

## OpenID Connect (OIDC)
OpenID is an open standard for authentication
that allows users to use their existing online identities to log in to different websites and services.

For example, you can use OpenID to log in to Stack Overflow with your Google or Facebook account.

OpenID **uses identifiers** to represent the user's identity,
**which are URLs or URIs that point to an OpenID provider (OP) that verifies the user's credentials**.

_OpenID doesn't provide authorization_,
meaning it doesn't specify what the user can do or access on the relying party (RP),
but _it can be combined with other protocols, such as `OAuth`, to do so._