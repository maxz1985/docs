# S3 Object Storage

## Prefixes (folder/subfolder path)
S3 is a flat object storage, and while it does have a concept of "folders" and "subfolders,"
they're called `prefixes`.

The term `prefix` is used because in a flat storage model, the folders and subfolders constitute 
the **beginning** part of an object's key, which is used to organize objects within a bucket.

Folders and subfolders constitute a virtual directory path that does in fact precede or `prefix` the object key.

```shell
subfolder1/subfolder2/object_name
```
{type="wide"}
`subfolder1/`
: prefix

`subfolder1/subfolder2/`
: longer prefix that includes a subfolder

`object_name`
: the actual name of the object

## Applying Policies to Prefixes
You can also apply IAM policies or bucket policies to specific prefixes to control access.

The policy below allows all S3 operations on all objects in `my-unique-bucket`
whose key (path) starts with a prefix of `project1/subfolder1/`
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:*",
      "Resource": [
        "arn:aws:s3:::/project1/subfolder1/*"
      ]
    }
  ]
}
```