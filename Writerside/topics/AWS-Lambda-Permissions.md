# AWS Lambda Permissions
<tldr>
    <p>Minimal permissions required are granting access to `CloudWatch`.</p>
    <p>They are provided by `AWSLambdaBasicExecutionRole` managed policy.</p>
    <code-block lang="json">
        {
          "Version" : "2012-10-17",
          "Statement" : [
            {
              "Effect" : "Allow",
              "Action" : [
                "logs:CreateLogGroup",
                "logs:CreateLogStream",
                "logs:PutLogEvents"
              ],
              "Resource" : "*"
            }
          ]
        }
    </code-block>
</tldr>
There are two main categories of permission to consider when working with Lambda:

[](#permissions-that-lambda-needs-to-access-other-aws-resources-and-perform-api-actions)

[](#permissions-that-other-aws-users-and-entities-need-to-call-lambda)

## Permissions that Lambda needs to access other AWS resources and perform API actions
Permissions that a Lambda function needs are defined in a special IAM role called an _execution role_.
### Create an execution role in IAM
To create an execution role in the IAM console:
1. Open the _Roles page_ in the IAM console. 
2. Choose **Create role**. 
3. Under **Trusted entity type**, choose **AWS service**. 
4. Under **Use case**, choose **Lambda**. 
5. Choose **Next**. 
6. Select the AWS managed policies that you want to attach to your role. For example, if your function needs to access DynamoDB, select the AWSLambdaDynamoDBExecutionRole managed policy. 
7. Choose **Next**. 
8. Enter a **Role name** and then choose **Create role**.
### Add required permission policies for your lambda
At a minimum, _your execution role must have access to Amazon CloudWatch_ because Lambda functions log to CloudWatch Logs by default.

You can attach the `AWSLambdaBasicExecutionRole` managed policy to your execution role to satisfy this requirement.

If your Lambda requires access to additional resources, attach those permission policies to your execution role.

For example, if you need access to `DynamoDB`, attach the following policy to the role:
```json
{
  "Version": "2012-10-17",
  "Statement": {
      "Sid": "Stmt1428341300017",
      "Action": [
        "dynamodb:GetItem",
        "dynamodb:PutItem"
      ],
      "Effect": "Allow",
      "Resource": "arn.aws.dynamodb:ap-southeast-2:xxxxx:xxxx"
    }
}
```
## Permissions that other AWS users and entities need to call Lambda
As an example, let's grant the API Gateway permission to invoke a Lambda function.

Amazon API Gateway gets permission to invoke your function from the **function's resource-based policy**.

Put the following into lambda's resource-based policy:
```json
{
  "Version": "2012-10-17",
  "Id": "default",
  "Statement": [
    {
      "Sid": "nodejs-apig-functiongetEndpointPermissionProd-BWDBXMPLXE2F",
      "Effect": "Allow",
      "Principal": {
        "Service": "apigateway.amazonaws.com"
      },
      "Action": "lambda:InvokeFunction",
      "Resource": "arn:aws:lambda:us-east-2:111122223333:function:nodejs-apig-function-1G3MXMPLXVXYI",
      "Condition": {
        "StringEquals": {
          "aws:SourceAccount": "111122223333"
        },
        "ArnLike": {
          "aws:SourceArn": "arn:aws:execute-api:us-east-2:111122223333:ktyvxmpls1/*/GET/"
        }
      }
    }
  ]
}
```
> When you add an API to your function by using the Lambda console, using the API Gateway console, or in an AWS SAM template, the function's resource-based policy is updated automatically.
> 
{style="note"}