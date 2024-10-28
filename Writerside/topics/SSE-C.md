# SSE-C

`SSE-C` enables customer to use **their own** `AES-256`encryption key to encrypt S3 objects at rest.

The main advantage is security. Using your own key prevents AWS from reading your files since the key resides with you.

The key is provided as part of the API request and is deleted from AWS memory when the object encryption process is complete.

> When using `SSE-C` you **cannot use** AWS Console to upload S3 objects since the encryption key has to be included
> in the `x-amz-server-side-encryption-customer-key` HTTP request header.
> 
> When using `SSE-C` you **cannot use** pre-signed URLs directly in a browser for the same reason. 
> 
> All `SSE-C` S3 operations need to be performed via API.
> 
{style="note"}

## Pre-Signed URLs with `SSE-C`
* For pre-signed URLs, specify the **algorithm** using the `x-amz-server-side-encryption-customer-algorithm` HTTP request header. The header value is `AES256`.
* When using the pre-signed URL to upload a new object, retrieve an existing object, or retrieve only object metadata,
  you must provide all the encryption headers in your client application's request.

## S3 Rest API Calls with `SSE-C`
At the time of object creation with the REST API, you can specify server-side encryption with customer-provided keys (SSE-C).
When you use SSE-C, you must provide encryption key information using the following request headers.

| HTTP Request Header                               | Description                                                                                                                                                                                                                              |
|---------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `x-amz-server-side-encryption-customer-algorithm` | Use this header to specify the encryption algorithm. The header value must be `AES256`.                                                                                                                                                  |
| `x-amz-server-side-encryption-customer-key`       | Use this header to provide the 256-bit, base64-encoded encryption key for Amazon S3 to use to encrypt or decrypt your data.                                                                                                              |
| `x-amz-server-side-encryption-customer-key-MD5`   | Use this header to provide the 128-bit, base64-encoded MD5 digest of the encryption key according to RFC 1321. Amazon S3 uses this header for a message integrity check to ensure that the encryption key was transmitted without error. |
