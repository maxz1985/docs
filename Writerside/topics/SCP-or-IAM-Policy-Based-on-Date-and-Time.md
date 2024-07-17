# SCP or IAM Policy Based on Date and Time

SCP/IAM policies can have a `Condition` attribute specifying Date and Time when the policy action is valid.

This effectively allows policies to act only during a specific time window or be "expired" at a certain date.

Example:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "service-prefix:action-name",
            "Resource": "*",
            "Condition": {
                "DateGreaterThan": {"aws:CurrentTime": "2020-04-01T00:00:00Z"},
                "DateLessThan": {"aws:CurrentTime": "2020-06-30T23:59:59Z"}
            }
        }
    ]
}
```
This policy restricts access to actions that occur between April 1, 2020, and June 30, 2020 (UTC), inclusive

SEE: [AWS: Allows access based on date and time](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_examples_aws-dates.html)