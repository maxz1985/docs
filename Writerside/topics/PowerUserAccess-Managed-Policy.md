# PowerUserAccess Managed Policy

```json
{
  "Version" : "2012-10-17",
  "Statement" : [
    {
      "Effect" : "Allow",
      "NotAction" : [
        "iam:*",
        "organizations:*",
        "account:*"
      ],
      "Resource" : "*"
    },
    {
      "Effect" : "Allow",
      "Action" : [
        "account:GetAccountInformation",
        "account:GetPrimaryEmail",
        "account:ListRegions",
        "iam:CreateServiceLinkedRole",
        "iam:DeleteServiceLinkedRole",
        "iam:ListRoles",
        "organizations:DescribeOrganization"
      ],
      "Resource" : "*"
    }
  ]
}
```
* Allow all actions **Except** `iam`, `organizations` and `account`
* Allow only the following **Specific** actions in
  * `account` - `GetAccountInformation`, `GetPrimaryEmail` and `ListRegions`
  * `iam` - `CreateServiceLinkedRole`, `DeleteServiceLinkedRole` and `ListRoles`
  * `organizations` - `DescribeOrganization`

This allows users to see the permissions in `AWS Organizations` to view the information about the user’s organization,
including the master account email and organization limitations.