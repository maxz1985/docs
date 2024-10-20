# SAML Federated Access to AWS API
<tldr><p><ui-path>User -> IdP -> User <code>AssumeRolewithSAML</code> -> STS -> Credentials</ui-path></p></tldr>
> A pre-requisite for this is to have a [](SAML-Identity-Provider-in-IAM.md) and [](IAM-Roles-For-SAML-Federation.md)
>
{style="note"}

## Scenario
You need to provide users, already **authenticated** by your own corporate SAML2.0 identity provider (IdP),
**federated access to AWS API**.

(Also allows access to AWS Console, but not its primary goal.)

![saml-federated-access-to-aws-api.png](saml-federated-access-to-aws-api.png)

1. _User_ makes a request to _On-prem IdP_ and gets authenticated
2. _On-prem IdP_ responds with a `SAML Assertion` which contains the list of `Role` attributes indicating the IAM roles user can assume. Other configured attributes like `username` may be included in the assertion.
3. Users forward this `SAML Assertion` to `STS` using `AssumeRolewithSAML` API
4. `STS` returns temporary security credentials that allow _User_ to access the Amazon S3 bucket

[Using SAML-based federation for API access to AWS](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_providers_saml.html#CreatingSAML-configuring)

## Example of a SAML Assertion returned from on-prem IdP
```xml
<?xml version="1.0" encoding="UTF-8"?>
<saml2p:Response xmlns:saml2p="urn:oasis:names:tc:SAML:2.0:protocol" ID="_abc123" Version="2.0" IssueInstant="2023-10-01T12:00:00Z" Destination="https://signin.aws.amazon.com/saml">
  <saml2:Issuer xmlns:saml2="urn:oasis:names:tc:SAML:2.0:assertion">https://your-idp.example.com/</saml2:Issuer>
  <saml2p:Status>
    <saml2p:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success"/>
  </saml2p:Status>
  <saml2:Assertion xmlns:saml2="urn:oasis:names:tc:SAML:2.0:assertion" ID="_def456" Version="2.0" IssueInstant="2023-10-01T12:00:00Z">
    <saml2:Issuer>https://your-idp.example.com/</saml2:Issuer>
    <saml2:Subject>
      <saml2:NameID Format="urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified">john.doe@example.com</saml2:NameID>
      <saml2:SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <saml2:SubjectConfirmationData NotOnOrAfter="2023-10-01T12:05:00Z" Recipient="https://signin.aws.amazon.com/saml"/>
      </saml2:SubjectConfirmation>
    </saml2:Subject>
    <saml2:Conditions NotBefore="2023-10-01T12:00:00Z" NotOnOrAfter="2023-10-01T12:05:00Z">
      <saml2:AudienceRestriction>
        <saml2:Audience>https://signin.aws.amazon.com/saml</saml2:Audience>
      </saml2:AudienceRestriction>
    </saml2:Conditions>
    <saml2:AttributeStatement>
      <!-- Role Attribute -->
      <saml2:Attribute Name="https://aws.amazon.com/SAML/Attributes/Role">
        <saml2:AttributeValue>arn:aws:iam::123456789012:role/AdminRole,arn:aws:iam::123456789012:saml-provider/YourSAMLProvider</saml2:AttributeValue>
        <saml2:AttributeValue>arn:aws:iam::123456789012:role/OperatorRole,arn:aws:iam::123456789012:saml-provider/YourSAMLProvider</saml2:AttributeValue>
      </saml2:Attribute>
      <!-- Session Duration (Optional) -->
      <saml2:Attribute Name="https://aws.amazon.com/SAML/Attributes/SessionDuration">
        <saml2:AttributeValue>3600</saml2:AttributeValue>
      </saml2:Attribute>
      <!-- User Attributes as Session Tags -->
      <saml2:Attribute Name="https://aws.amazon.com/SAML/Attributes/PrincipalTag:username">
        <saml2:AttributeValue>john.doe</saml2:AttributeValue>
      </saml2:Attribute>
      <saml2:Attribute Name="https://aws.amazon.com/SAML/Attributes/PrincipalTag:department">
        <saml2:AttributeValue>Engineering</saml2:AttributeValue>
      </saml2:Attribute>
      <!-- Additional User Attributes -->
      <saml2:Attribute Name="email">
        <saml2:AttributeValue>john.doe@example.com</saml2:AttributeValue>
      </saml2:Attribute>
      <saml2:Attribute Name="firstName">
        <saml2:AttributeValue>John</saml2:AttributeValue>
      </saml2:Attribute>
      <saml2:Attribute Name="lastName">
        <saml2:AttributeValue>Doe</saml2:AttributeValue>
      </saml2:Attribute>
    </saml2:AttributeStatement>
    <saml2:AuthnStatement AuthnInstant="2023-10-01T12:00:00Z" SessionIndex="_ghi789">
      <saml2:AuthnContext>
        <saml2:AuthnContextClassRef>
          urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport
        </saml2:AuthnContextClassRef>
      </saml2:AuthnContext>
    </saml2:AuthnStatement>
  </saml2:Assertion>
</saml2p:Response>

```
NOTE the following:
* `Role` attributes
```xml
<!-- Role Attribute -->
      <saml2:Attribute Name="https://aws.amazon.com/SAML/Attributes/Role">
        <saml2:AttributeValue>arn:aws:iam::123456789012:role/AdminRole,arn:aws:iam::123456789012:saml-provider/YourSAMLProvider</saml2:AttributeValue>
        <saml2:AttributeValue>arn:aws:iam::123456789012:role/OperatorRole,arn:aws:iam::123456789012:saml-provider/YourSAMLProvider</saml2:AttributeValue>
      </saml2:Attribute>
```
This user can assume `AdminRole` or `OperatorRole` AWS IAM roles.
The roles should have been set up according to [](IAM-Roles-For-SAML-Federation.md)

* There is a `username` custom attribute sent as a `PrincipalTag`
```xml
<!-- User Attributes as Session Tags -->
      <saml2:Attribute Name="https://aws.amazon.com/SAML/Attributes/PrincipalTag:username">
        <saml2:AttributeValue>john.doe</saml2:AttributeValue>
      </saml2:Attribute>
```