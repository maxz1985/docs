# ARN Components

An Amazon Resource Name (ARN) follows a standard format:
```shell
arn:partition:service:region:account-id:resource
```
{type="narrow"}
`partition`
: The AWS partition (for example, `aws` for standard regions, `aws-cn` for China regions, `aws-us-gov` for AWS GovCloud).

`service`
: The AWS service (for example, `s3`, `lambda`, `dynamodb`).

`region`
: The AWS region (for example, `us-west-2`, `us-east-1`).

`account-id`
: The 12-digit AWS account ID.

`resource`
: The specific resource identifier, which varies by service

## ex. ARN for KMS key:
```shell
arn:aws:kms:us-east-2:111122223333:key/1234abcd-12ab-34cd-56ef-1234567890ab
```
{type="wide"}
`key/1234abcd-12ab-34cd-56ef-1234567890ab`
: This is what a `resource identifier` looks like for a KMS service.
We're specifying a particular key with key ID `1234abcd-12ab-34cd-56ef-1234567890ab`.

## ex. ARN for IAM:
### User
```shell
arn:aws:iam::123456789012:user/JohnDoe
```
{type="medium"}
`::`
: NOTE the EMPTY `region` designator in this ARN.
This is because **`IAM` is a [Global Service](AWS-Global-Services.md) and doesn't have a region.**

`user/JohnDoe`
: A `resource identifier` for IAM Service. Specifying `JohnDoe` **user**.

### Group
```shell
arn:aws:iam::123456789012:group/Developers
```
{type="medium"}
`::`
: NOTE the EMPTY `region` designator in this ARN.
This is because **`IAM` is a [Global Service](AWS-Global-Services.md) and doesn't have a region.**

`group/Developers`
: A `resource identifier` for IAM Service. Specifying `Developers` **group**.

### IAM Role
```shell
arn:aws:iam::123456789012:role/AdminRole
```
{type="medium"}
`::`
: NOTE the EMPTY `region` designator in this ARN.
This is because **`IAM` is a [Global Service](AWS-Global-Services.md) and doesn't have a region.**

`role/AdminRole`
: A `resource identifier` for IAM Service. Specifying `AdminRole` **IAM role**.

## ex. ARN for S3 bucket
```shell
arn:aws:s3:::DOC-EXAMPLE-BUCKET
```
{type="medium"}
`::::`
: NOTE EMPTY `region` AND `account` designators in this ARN.
This is because **`S3` is a [Global Service](AWS-Global-Services.md) and doesn't have a region.** 
Also, **`S3` bucket names are also Globally Unique** regardless of account, so account ID is unnecessary.

`DOC-EXAMPLE-BUCKET`
: A `resource identifier` for S3 Bucket. Specifying `DOC-EXAMPLE-BUCKET` bucket name.
