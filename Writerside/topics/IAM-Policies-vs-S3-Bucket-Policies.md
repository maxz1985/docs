# IAM Policies vs S3 Bucket Policies

There are two types of policies for S3 Access, – IAM and Bucket.  
> Visually, the resource (bucket) policy can be distinguished by the presence of the `Principal` parameter in the policy.
> 
{style="note"}

## IAM Policy (Identity-based Policy)

The policy is attached to an **Identity** (users, groups, roles) and defines what **Actions** the Identity can perform on which **Resources**. 

The IAM Policy Example below grants full permissions for the S3 bucket `my_bucket` to an **Identity** that this policy is attached to.

```JSON
{
  "Version": "2012-10-17",
  "Statement":[
    {
    "Effect": "Allow",
    "Action": "s3:*",
    "Resource": ["arn:aws:s3:::my_bucket/*"]
    }
  ]
}
```

## Bucket Policy (Resource-based Policy)

The policy is attached to a **Resource** and defines which **Identities** can perform what **Actions** on *this* resource.

The bucket policy below grants the ability to use S3 `GET Bucket` (`ListObjects`) operation to the IAM role `Role-name` from the AWS account `111122223333` 

```JSON
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::111122223333:role/Role-name"
      },
      "Action": "s3:ListBucket",
      "Resource": "arn:aws:s3:::my_bucket"
    }
  ]
}
```

## Identity-based Policies are used when

- Controlling access to more AWS services other than S3. IAM policies will be simpler to manage since you can centrally manage your permissions in IAM, instead of spreading them between IAM and S3.
- **Managing many S3 buckets, each with different permissions requirements.** IAM policies will be simpler to manage since you don’t have to define a large number of S3 bucket policies and can instead rely on fewer, more detailed IAM policies.
- There is a need to keep access control policies in the IAM environment.

## S3 Resource-based Policies are used when

- There is a need for a simple way to grant **cross-account access** to your S3 environment, without using IAM roles. see also [](Cross-Account-Access.md)
- IAM policies bump up against the size limit (up to 2 KB for users, 5 KB for groups, and 10 KB for roles). S3 supports bucket policies of up to 20 KB.
- There is a need to keep access control policies in the S3 environment.
- There is a requirement to apply common security controls to the principals who interact with S3 buckets, such as restricting the IP addresses or VPC a bucket can be accessed from.

Consider your answer to this audit question to select the right approach:

- If you’re more interested in “**What can this user do in AWS?**”, then IAM policies are probably the way to go. You can answer this question by looking up an IAM user and then examining their IAM policies to see what rights they have.
- If you’re more interested in “**Who can access this S3 bucket?**”, then S3 bucket policies will likely suit you better. You can answer this question by looking up a bucket and examining the bucket policy.