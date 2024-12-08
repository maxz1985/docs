# AWS Organizations and Private Marketplace

AWS Provides private marketplace to create curated catalog Public Marketplace offerings available only to your Organization.

Private Marketplace can be instantiated in a management account of the AWS Organization.

Following is the best practice approach to administering Private Marketplace.

The organization contains multiple OUs each with multiple accounts.

In each OU there is an account nominated to be a "Shared Services Account" 
serving a special purpose of hosting resources common to all other accounts in the OU. 
(like Git server that all accounts  in the OU are using )


1. Create an IAM role named `procurement-manager-role` in **each** of the _Shared Services Accounts_.
2. Add the `AWSPrivateMarketplaceAdminFullAccess` managed policy to the role.
3. Create an organization root-level SCP to deny permissions to administer _Private Marketplace_ to everyone except `procurement-manager-role`
4. Create another organization root-level SCP to deny permissions to create the `procurement-manager-role` to everyone in the organization 

## SCP to deny permissions to administer `Private Marketplace` to everyone except the `procurement-manager-role`
```JSON
{
   "Version": "2012-10-17",
   "Statement": [
      {
         "Sid": "ProcurementPolicy",
         "Effect": "Deny",
         "Action": [
            "aws-marketplace:CreatePrivateMarketplace",
            "aws-marketplace:StartPrivateMarketplace",
            "aws-marketplace:StopPrivateMarketplace",
            "aws-marketplace:DescribePrivateMarketplaceStatus",
            "aws-marketplace:AssociateProductsWithPrivateMarketplace",
            "aws-marketplace:DisassociateProductsFromPrivateMarketplace",
            "aws-marketplace:ListPrivateMarketplaceProducts",
            "aws-marketplace:DescribePrivateMarketplaceProducts",
            "aws-marketplace:ListPrivateMarketplaceRequests",
            "aws-marketplace:DescribePrivateMarketplaceRequests",
            "aws-marketplace:UpdatePrivateMarketplaceSettings",
            "aws-marketplace:DescribePrivateMarketplaceSettings",
            "aws-marketplace:CreatePrivateMarketplaceProfile",
            "aws-marketplace:UpdatePrivateMarketplaceProfile",
            "aws-marketplace:DescribePrivateMarketplaceProfile"
         ],
         "Resource": "*",
         "Condition": {
            "StringNotLike": {
               "aws:PrincipalARN": "arn:aws:iam::*:role/procurement-manager"
            }
         }
      }
   ]
}
```
## SCP to deny permissions to create the `procurement-manager-role` to everyone in the organization
```JSON
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "DenyCreateProcurementManagerRole",
            "Effect": "Deny",
            "Action": [
                "iam:CreateRole",
                "iam:UpdateRole",
                "iam:PutRolePolicy",
                "iam:AttachRolePolicy"
            ],
            "Resource": "arn:aws:iam::*:role/procurement-manager-role"
        }
    ]
}

```
## `AWSPrivateMarketplaceAdminFullAccess` Managed Permission Policy
```JSON
{
  "Version" : "2012-10-17",
  "Statement" : [
    {
      "Sid" : "PrivateMarketplaceRequestPermissions",
      "Effect" : "Allow",
      "Action" : [
        "aws-marketplace:AssociateProductsWithPrivateMarketplace",
        "aws-marketplace:DisassociateProductsFromPrivateMarketplace",
        "aws-marketplace:ListPrivateMarketplaceRequests",
        "aws-marketplace:DescribePrivateMarketplaceRequests"
      ],
      "Resource" : [
        "*"
      ]
    },
    {
      "Sid" : "PrivateMarketplaceCatalogAPIPermissions",
      "Effect" : "Allow",
      "Action" : [
        "aws-marketplace:ListEntities",
        "aws-marketplace:DescribeEntity",
        "aws-marketplace:StartChangeSet",
        "aws-marketplace:ListChangeSets",
        "aws-marketplace:DescribeChangeSet",
        "aws-marketplace:CancelChangeSet"
      ],
      "Resource" : "*"
    },
    {
      "Sid" : "PrivateMarketplaceCatalogTaggingPermissions",
      "Effect" : "Allow",
      "Action" : [
        "aws-marketplace:TagResource",
        "aws-marketplace:UntagResource",
        "aws-marketplace:ListTagsForResource"
      ],
      "Resource" : "arn:aws:aws-marketplace:*:*:AWSMarketplace/*"
    },
    {
      "Sid" : "PrivateMarketplaceOrganizationPermissions",
      "Effect" : "Allow",
      "Action" : [
        "organizations:DescribeOrganization",
        "organizations:DescribeOrganizationalUnit",
        "organizations:DescribeAccount",
        "organizations:ListRoots",
        "organizations:ListParents",
        "organizations:ListOrganizationalUnitsForParent",
        "organizations:ListAccountsForParent",
        "organizations:ListAccounts",
        "organizations:ListAWSServiceAccessForOrganization",
        "organizations:ListDelegatedAdministrators"
      ],
      "Resource" : "*"
    }
  ]
}
```