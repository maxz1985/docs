# Signed URLs

Signed URLs are used to share a resource with expiration time limit and select users.

Signed URLs contain a signature generated using a private key which makes them unique.

CloudFront validates the URL signature using a corresponding public key associated with CloudFront distribution.

## Example of a signed URL
```shell
https://d111111abcdef8.cloudfront.net/sample.txt?Expires=1618477793&Signature=htGZyq1q7u...&Key-Pair-Id=APKAIXXXXXXXXXXXX
```
{type="wide"}
`https://d111111abcdef8.cloudfront.net/sample.txt`
: Resource URL

`?Expires=1618477793`
: Expiration time typically included as a query parameter

`&Signature=htGZyq1q7u`
: Signature created using the private key of a key pair. This signature allows CloudFront to verify that the URL was generated by someone with access to the private key and that it has not been modified.  Typically included as a query parameter.

`&Key-Pair-Id=APKAIXXXXXXXXXXXX`
: Key Pair ID. Tells CloudFront which public key to use to verify the signature. The public key must be associated with your CloudFront distribution.