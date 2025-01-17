# IAM Policy Operators

## ARN Operators

| Condition Operator           | Description                                                                                                                                                                                                                                                                                         |
|------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `ArnEquals`, `ArnLike`       | **Case-sensitive** matching of the `ARN`. Each of the six colon-delimited components of the `ARN` is checked separately and each can include multi-character match wildcards (`*`) or single-character match wildcards (`?`). The `ArnEquals` and `ArnLike` condition operators behave identically. |
| `ArnNotEquals`, `ArnNotLike` | Negated matching for `ARN`. The `ArnNotEquals` and `ArnNotLike` condition operators behave identically.                                                                                                                                                                                             |

> `ArnLike` can be used for **case-insensitive** matching if you wildcard components of the `ARN` for which the case should be ignored.
> 
{style="note"}
