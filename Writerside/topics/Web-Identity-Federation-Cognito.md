# Web Identity Federation Cognito

## Scenario
A mobile app user needs access to an AWS Resource.

## Web Identity Federation with Cognito

The diagram below tries to combine 2 authflows, - Enhanced and Basic, unsuccessfully.<br/> For details see [](#enhanced-simplified-authflow)and [](#basic-classic-authflow)
> Note in both scenarios, Amazon Cognito **has been configured to trust** your IdP Login.
> 
{style="note"}

![web-identity-federation-cognito.png](web-identity-federation-cognito.png) {thumbnail="true"}

> The basic(classic) authflow gives you more granular control over the credentials that you distribute to your users.<br/>
> The `GetCredentialsForIdentity`
> request of the **enhanced** authflow requests credentials for **one particular role** based on the contents of an access token.<br/>
> The `AssumeRoleWithWebIdentity`
> request in the **classic** authflow allows you
> to request credentials for **any IAM role that you have configured with a sufficient trust policy**.
> You can also request a custom role session duration.

## Enhanced (simplified) Authflow

<ui-path><p><code>GetId</code>-> <code>GetCredentialsForIdentity</code></p></ui-path>

![web-identity-federation-cognito-02.png](web-identity-federation-cognito-02.png) {thumbnail="true"}

1. A customer starts your app on a mobile device. The app asks the user to sign in
2. The app uses Login with Amazon, Facebook, Google, or any other OpenID Connect (OIDC)–compatible IdP to accept the user's credentials.
An `IdP ID Token` is returned by the IdP
3. The app submits the `IdP Token` to Cognito's  `GetId` API
4. Your identity pool returns an `identity ID`
5. The app combines the `identity ID` with the `IdP ID Token` in a `GetCredentialsForIdentity` request to Cognito
6. (_in background_) Cognito performs "GetOpenIdToken" and "AssumeRoleWithWebIdentity" FOR YOU, you're NOT making these calls with enhanced(simplified) authflow.
7. Your identity pool returns **AWS Credentials**
8. Your application signs AWS API requests with the temporary credentials.

> The flow is "Simplified" or "Enhanced" because in Step 5, Cognito calls STS for you to exchange
> Amazon Cognito Token that it generates for STS credentials and returns the credentials.<br/>
> Compare with "Classic" authflow where you have to make three calls.
> 
{style="note"}

## Basic (classic) Authflow

<ui-path><p><code>GetId</code>-> <code>GetOpenIdToken</code>-> <code>AssumeRoleWithWebIdentity</code></p></ui-path>

![web-identity-federation-cognito-03.png](web-identity-federation-cognito-03.png) {thumbnail="true"}

1. A customer starts your app on a mobile device. The app asks the user to sign in
2. The app uses Login with Amazon, Facebook, Google,
   or any other OpenID Connect (OIDC)–compatible IdP to accept the user's credentials.
   An `IdP ID Token` is returned by the IdP
3. The app submits the `IdP Token` to Cognito's  `GetId` API
4. Your identity pool returns an `identity ID`
5. The app combines the `identity ID` with `IdP ID Token` in a `GetOpenIdToken` request to Cognito
6. `GetOpenIdToken` returns a new **`Cognito's OAuth 2.0 token`** **issued by your identity pool**
7. Your application presents the `Cognito's OAuth 2.0 token` in an `AssumeRoleWithWebIdentity` request to **STS**
8. AWS Security Token Service (AWS STS) returns AWS credentials.
9. Your application signs AWS API requests with the temporary credentials.

## Steps to set up
1. Sign up with Amazon, Facebook, Google, or any other OpenID Connect (OIDC)–compatible IdP and 
configure one or more apps with the provider.
2. Go to Amazon Cognito in the AWS Management Console. Use the Amazon Cognito wizard to create an **identity pool**, 
which is a **container that Amazon Cognito uses to keep end user identities organized for your apps**.<br/>
When you set up an identity pool, 
**Amazon Cognito creates one or two IAM roles** 
(one for authenticated identities, and one for unauthenticated "guest" identities) that define permissions for Amazon Cognito users.
3. Integrate [AWS Amplify](https://docs.amplify.aws) into your app, and import the files required to use Amazon Cognito.
4. In your app, create an instance of the Amazon Cognito credentials provider, passing the identity pool ID, 
your AWS account number, and the Amazon Resource Name (ARN) of the roles that you associated with the identity pool.
5. When your app accesses an AWS resource, pass the credentials provider instance to the client object,
which passes temporary security credentials to the client.
The permissions for the credentials are based on the role or roles that you defined earlier.

[Identity pools (federated identities) authentication flow](https://docs.aws.amazon.com/cognito/latest/developerguide/authentication-flow.html)

[Using Amazon Cognito for mobile apps](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_oidc_cognito.html)
