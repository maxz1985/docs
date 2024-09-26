# IAM Web Identity Federation

Web Identity Federation can be used with IAM also.

* Set up Web Federation Provider in IAM
* Create an **IAM Role** with **Trust Policy** (who can assume the role) and **Permission Policy** (what are the permissions)
* IAM Role's Trust Policy **defines** what Web Identity Provider can assume the role. 
* see [](IAM-Roles-and-attached-policies.md)

![iam-web-identity-federation-01.png](iam-web-identity-federation-01.png) {thumbnail="true"}

1. The app calls a third-party identity provider to authenticate the user and the app. The identity provider returns a web identity token to the app.
2. The app calls AWS STS using `AssumeRoleWithWebIdentity` and passes the web identity token AND the `IAM Role's ARN` as input. AWS STS authorizes the app and gives it temporary AWS access credentials. The app is allowed to assume the IAM role and access AWS resources in accordance with the role's permission policy.
3. The app calls DynamoDB to access the DynamoDB GameScores table. Because of the `Permission Policy` of the assumed `IAM Role`, the app is subject to the security policy associated with that role.

The following example policy document prevents the app from accessing data that doesn't belong to the user.
```json
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Sid":"AllowAccessToOnlyItemsMatchingUserID",
         "Effect":"Allow",
         "Action":[
            "dynamodb:GetItem",
            "dynamodb:BatchGetItem",
            "dynamodb:Query",
            "dynamodb:PutItem",
            "dynamodb:UpdateItem",
            "dynamodb:DeleteItem",
            "dynamodb:BatchWriteItem"
         ],
         "Resource":[
            "arn:aws:dynamodb:us-west-2:123456789012:table/GameScores"
         ],
         "Condition":{
            "ForAllValues:StringEquals":{
               "dynamodb:LeadingKeys":[
                  "${www.amazon.com:user_id}"
               ],
               "dynamodb:Attributes":[
                  "UserId",
                  "GameTitle",
                  "Wins",
                  "Losses",
                  "TopScore",
                  "TopScoreDateTime"
               ]
            },
            "StringEqualsIfExists":{
               "dynamodb:Select":"SPECIFIC_ATTRIBUTES"
            }
         }
      }
   ]
}
```