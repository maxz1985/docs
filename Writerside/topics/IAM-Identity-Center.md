# IAM Identity Center

IAM Identity Center is used for SAML federation with **multiple AWS Accounts** that are members of **AWS Organizations**

| **Feature**                 | **IAM SAML Federation**                                         | **IAM Identity Center with AWS Managed AD**                                        |
|-----------------------------|-----------------------------------------------------------------|------------------------------------------------------------------------------------|
| **Intermediary Required**   | Requires ADFS or another SAML-compliant IdP (e.g., Okta, Ping). | AWS Managed Microsoft AD acts as the intermediary.                                 |
| **Direct AD Trust**         | No; relies on external IdP for federation.                      | Yes; direct trust between AWS Managed AD and on-prem AD.                           |
| **Setup Complexity**        | Higher; manual role and policy mappings in IAM.                 | Moderate; AWS Managed AD simplifies domain trust but still requires configuration. |
| **Multi-Account Support**   | Requires manual cross-account role configuration.               | **Built-in multi-account support** via **AWS Organizations**.                      |
| **Use Case Scope**          | Best for single AWS account setups.                             | Best for **multi-account AWS Organizations** setups.                               |
| **Third-Party App Support** | Limited to AWS services (e.g., Console, APIs).                  | Supports AWS and third-party SaaS apps (e.g., Salesforce, Slack).                  |
| **User Management**         | Requires managing SAML assertions and IAM roles manually.       | Simplified via permission sets and centralized management in Identity Center.      |
| **Session Duration**        | Configurable but tied to IAM roles (default 1 hour).            | Configurable per permission set in Identity Center.                                |
| **Pricing**                 | Free (IAM-based).                                               | Free (AWS Managed AD is paid, but Identity Center is free).                        |
