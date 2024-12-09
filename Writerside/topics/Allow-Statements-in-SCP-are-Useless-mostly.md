# Allow Statements in SCP are Useless (mostly)

<tldr>
    <ul>
    <li>Put <code>DENY</code> statements into <code>SCP</code> to define maximum permission boundaries.</li>
    <li>Put <code>ALLOW</code> statements in <code>IAM Policies</code>.</li>
    </ul>
</tldr>

`SCP`s are **allow** by default while `IAM Policies` are **deny** by default.

## Using `Allow` statements in `SCP` (don't).

Let us say we want to restrict the creation of `0.0.0.0/0` security group rules organization-wide.

Naturally, we turn to `SCP` to solve this. Here are the two versions of the SCP we could use:
<table>
    <tr>
        <td>
            <code-block lang="json">
                {
                    "Version": "2012-10-17",
                    "Statement": [
                        {
                            "Effect": "Deny",
                            "Action": "ec2:AuthorizeSecurityGroupIngress",
                            "Resource": "*",
                            "Condition": {
                                "IpAddress": {
                                    "ec2:cidr": "0.0.0.0/0"
                                }
                            }
                        }
                    ]
                }
            </code-block>
        </td>
        <td>
            <code-block lang="JSON">
                {
                    "Version": "2012-10-17",
                    "Statement": [
                        {
                            "Effect": "Allow",
                            "Action": "ec2:AuthorizeSecurityGroupIngress",
                            "Resource": "*",
                            "Condition": {
                                "NotIpAddress": {
                                    "ec2:cidr": "0.0.0.0/0"
                                }
                            }
                        }
                    ]
                }
            </code-block>
        </td>
    </tr>
</table>

You've noted the differences:

| Deny               | Allow              |
|--------------------|--------------------|
| `"Effect": "Deny"` | `Effect": "Allow"` |
| `"IpAddress":`     | `"NotIpAddress":`  |

On the surface, both policies seem to do the same thing. So which one is better? 

The answer is that the **"Allow" version is unusable**.

`SCP`s are _allow by default_; they're designed as permission boundaries defining maximum permissions allowed.
They're intended for use with `DENY` statements.

Oh! But we have a `Condition`! Well, it turns out the `Condition` in the `SCP` `Allow Statements` are ignored when the [AWS Permissions Evaluation process runs.](AWS-Permission-Evaluation.md)

When an **action is Allowed** in the `SCP`, the **action's condition is ignored**.

All I have to do to circumvent the `Allow` `SCP` is to create the following `IAM Policy` for myself:
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "ec2:AuthorizeSecurityGroupIngress",
            "Resource": "*"
        }
    ]
}
```
After the `Allow` `SCP` is evaluated and the condition of `"NotIpAddress": {"ec2:cidr": "0.0.0.0/0"}` is **ignored**, 
the `"ec2:AuthorizeSecurityGroupIngress"` action is allowed organization-wide and the `IAM Policy` will 
grant the ability to create any type of security group rules.

## Oh yes, there is ONE special case for SCP Allow

`SCP Allow` can be used when the **parent** (organization root or higher-level OU) **applies a deny policy**, 
and you need to **grant selective permissions to child** accounts or OUs.

* "Allow" SCPs Only Work in a Deny-by-Default Model:
    * For "Allow" SCPs to have any effect, a higher-level SCP (root or parent OU) must apply a broad deny policy.
    * Without a deny policy at the parent level, the "Allow" SCP is redundant because actions are allowed by default.
* IAM Policies Still Apply:
    * Even if the "Allow" SCP permits an action, the action will not succeed unless an IAM policy in the child account also explicitly allows it.
