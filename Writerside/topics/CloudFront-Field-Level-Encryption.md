# CloudFront Field-Level Encryption

Used to encrypt sensitive data in POST requests like ex. Credit card numbers.

With Amazon CloudFront, you can enforce secure end-to-end connections to origin servers by using HTTPS.
The Origin HTTPS endpoint should present a valid certificate signed by an official certificate authority like, Comodo, DigiCert, or Symantec.

Field-level encryption **adds a layer of security** that lets you protect specific data in the POST request.

Specify the set of fields in POST requests that you want to be encrypted, and the **public key** to use to encrypt them.

The decrypting application should have a private key matching the public one.

![cloudfront-field-level-encryption-01.png](cloudfront-field-level-encryption-01.png) {thumbnail="true"}