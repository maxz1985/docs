# HTTP Headers
HTTP request and response headers are NOT part of HTML document.
They are used to control request and response behaviour.
## Request Headers
```shell
GET /index.html HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
```

{type="narrow"}
`User-Agent`
: provides information about the client making the request. It allows servers to tailor responses based on the client’s capabilities

`Host`
: (host headers) specify the domain name of the server to which the request is directed enabling a single HTTP server to service requests for **multiple websites**

## Response Headers
```shell
HTTP/1.1 200 OK
Date: Mon, 12 Jul 2024 14:50:00 GMT
Content-Type: text/html; charset=UTF-8
Content-Length: 1024
Connection: keep-alive
Cache-Control: max-age=3600, public
ETag: "686897696a7c876b7e"
Last-Modified: Mon, 12 Jul 2024 14:00:00 GMT
Accept-Ranges: bytes
```

{type="medium"}
`Cache-Control`
: tells the browser and intermediary caches like **CloudFront** how long to cache the content.
In this case, `max-age=3600, public` means the content should be cached for `3600` seconds (1 hour) and can be cached by any cache.