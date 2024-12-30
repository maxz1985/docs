# CloudFront Functions

**`CloudFront Functions`** are simple JavaScript programs executed at the edge.

They provide **lower latency and cost** compared to `Lambda@Edge` but are designed to perform simpler manipulations.

## Use Cases
* **HTTP header manipulation**: **View, add, modify, or delete** any of the **request/response headers**.
* **Cache-key manipulations and normalization**: Transform HTTP request attributes (such as URL, headers, cookies, and query strings) to construct the cache-key
* **URL rewrites and redirects**: Generate a response to redirect requests to a different URL.
* **Access authorization**: Implement access control and authorization for the content delivered through CloudFront by creating and validating user-generated tokens.

## Architecture

![cloudfront-functions-01.png](cloudfront-functions-01.png) {thumbnail="true"}

> * `CloudFront Functions` are executed **at one of the 218+ edge locations**. Being simple JavaScript programs, they do not require a dedicated execution environment. This results in lower latency and `1/16th`cost as compared to `Lambda@Edge`.
> * `Lambda@Edge` is executed **at one of the 13 Regional Edge Locations** and requires a full execution environment.
>
{style="note"}


|                                   | **`CloudFront Functions`**                   | **`Lambda@Edge`**                                                         |
|-----------------------------------|----------------------------------------------|---------------------------------------------------------------------------|
| **Runtime support**               | JavaScript<br/>(ECMAScript 5.1 compliant)    | Node.js, Python                                                           |
| **Execution location**            | 218+ CloudFront Edge Locations               | 13 CloudFront Regional Edge Caches                                        |
| **CloudFront triggers supported** | Viewer request<br/>Viewer response           | Viewer request<br/>Viewer response<br/>Origin request<br/>Origin response |
| **Maximum execution time**        | Less than 1 millisecond                      | 5 seconds (viewer triggers)<br/>30 seconds (origin triggers)              |
| **Maximum memory**                | 2MB                                          | 128MB (viewer triggers)<br/>10GB (origin triggers)                        |
| **Total package size**            | 10 KB                                        | 1 MB (viewer triggers)<br/>50 MB (origin triggers)                        |
| **Network access**                | NO                                           | YES                                                                       |
| **File system access**            | NO                                           | YES                                                                       |
| **Access to the request `body`**  | NO                                           | YES                                                                       |
| **Pricing**                       | Free tier available;<br/>charged per request | No free tier;<br/>charged per request and function duration               |


![cloudfront-functions-02.png](cloudfront-functions-02.png) {thumbnail="true"}

![cloudfront-functions-03.png](cloudfront-functions-03.png) {thumbnail="true"}

## Reference
[Introducing CloudFront Functions – Run Your Code at the Edge with Low Latency at Any Scale](https://aws.amazon.com/blogs/aws/introducing-cloudfront-functions-run-your-code-at-the-edge-with-low-latency-at-any-scale/)