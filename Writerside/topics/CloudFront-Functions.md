# CloudFront Functions

CloudFront functions are simple JavaScript programs executed at the edge.

They provide **lower latency and cost** compared to Lambda@Edge but are designed to perform simpler manipulations.

## Use Cases
* **HTTP header manipulation**: View, add, modify, or delete any of the **request/response headers**.
* **Cache-key manipulations and normalization**: Transform HTTP request attributes (such as URL, headers, cookies, and query strings) to construct the cache-key
* **URL rewrites and redirects**: Generate a response to redirect requests to a different URL.
* **Access authorization**: Implement access control and authorization for the content delivered through CloudFront by creating and validating user-generated tokens.
