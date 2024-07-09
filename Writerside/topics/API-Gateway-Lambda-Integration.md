<show-structure depth="3"/>

# API Gateway Lambda Integration

## Question
You've created an API Gateway API to expose the `GET /user?user={name}` method to invoke a Lambda function.
The method has a query string called "user."
The Lambda function will respond with a message of `Hello, {name}!`. 
You've deployed the Lambda function as follows:
```Javascript
exports.handler = function (event, context, callback) {
    var res = {
        "statusCode": 200,
        "headers": {
            "Content-Type": "*/*"
        }
    };
    if (event.user == null) {
        callback(new Error('Missing the required greeter parameter.'));
    } else {
        res.body = "Hello, " + event.user + "!";
        callback(null, res);
    }
};
```
How would you set up the Lambda integration with the API method properly?
## Suggested Answers

|   |                                                                                                                                                                                                                          |
|---|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| A | Use AWS CLI `aws apigateway put-integration-response` to set up the Lambda proxy integration. Configure the mapping of the user query string parameter to the user property of the JSON payload for the Lambda function. |
| B | Use AWS CLI `aws apigateway put-method` to set up the Lambda custom integration. Configure the mapping of the user query string parameter to the event property for the Lambda function.                                 |
| C | Use AWS CLI `aws apigateway put-integration` to set up the Lambda proxy integration. Use a template file to configure the mapping of the user query string parameter to the event property for the Lambda function.      |
| D | Use AWS CLI `aws apigateway put-integration` to set up the Lambda custom integration. Configure the mapping of the user query string parameter to the user property of the JSON payload for the Lambda function.         |

## Working through the problem
<tldr>
<p>The question asks to use <code>?user={name}</code> query string, but presented lambda code expects to find the name in the <code>event.user</code> property directly.</p>
<p>That means that <code>?user={name}</code> <b>mapping</b> has to be <b>configured in API Gateway</b>.</p>
<p>Which in turn means this is a <b>custom integration</b>.</p>
<p>In presented lambda code, setting <code>res.headers</code> and <code>res.body</code> directly is a red herring designed to make you think that this is a proxy integration.</p>
</tldr>

### Two types of Lambda Integration with API Gateway
* In Lambda **Proxy** Integration, **the entire API Gateway request is sent directly to the Lambda function**. 
  This includes all request headers, the query string parameters,
  path parameters, the HTTP method, and the body of the request.
  The Lambda function then returns a response that must be in a specific format.
* Lambda **Custom** Integration allows more control over the integration request and response mappings.
  Primarily custom integration allows **to map request and response parameters to properties of the `event` and `response`.**
  This means you can customize how **API Gateway transforms the incoming request** before it sends it to the Lambda function
  and how it transforms the Lambda output before returning it to the client.
* See comparison of two implementations [](#custom-vs-proxy-implementation-comparison)

### A
"A" mentions `aws apigateway put-integration-response`
CLI, which is used to configure API Gateway **responses** with lambda **custom** (not proxy) integration.

As the question asks about configuring the `GET /user?user={name}` **request**, we discard that.

### B
"B" mentions `aws apigateway put-method` CLI, which is used to create a method in API Gateway.

`put-method` doesn't have a parameter to "configure mapping," so discard this answer.

[](#put-method-example)
### C
"C" finally mentions the correct CLI for creating lambda **request** integration—`put-integration`

However, it also insists that you should use `put-integration` to set up a "Lambda **proxy** integration"
AND configure query string parameter mapping to the `event` property. 

In proxy integrations, query string parameters mapping is NOT set up.

See this example of how to use `put-integration` in proxy scenarios.[](#put-integration-examples-for-proxy-and-custom)

Discard this answer as well.

> There is a red herring in the presented Lambda code to make you think this is a proxy integration implementation.<br/><br/>
> NOTE how the code operates with `res.headers` and `res.body` properties directly.
> This is a hallmark of a lambda written for proxy integration.[](#custom-vs-proxy-implementation-comparison)<br/><br/>
> HOWEVER, As the code expects the `user`
> request parameter to be already mapped to `event.user` property without any request parsing,
> we know that this is a **custom** integration,
> as such mapping would only be possible using API Gateway Integration Request mapping.<br/><br/>
> So this is actually a **Custom integration masquerading as Proxy**.
>
{style="warning"}

> NOTE that it is **possible to mix** Proxy and Custom approaches in lambda code.<br/>
> It doesn't add to code clarity, and neither will it follow the idiomatic approach.<br/><br/>
> But even with custom API Gateway integration,
> you can operate on properties like `response.header` and `response.body` in the lambda code directly
> provided you don't mess them up.<br/>
> see [](#how-this-question-s-lambda-code-should-have-been-written)
> 
{style="note"}

### D
"D" is finally presenting the correct answer.

We're asked to set up a **custom integration** using `aws apigateway put-integration` CLI **with** parameter mapping.

That would translate to CLI being used like in a custom example here [](#put-integration-examples-for-proxy-and-custom)


### Custom vs Proxy Implementation comparison
<table>
    <tr>
        <td>Custom</td>
        <td>Proxy</td>
    </tr>
    <tr>
        <td>
            <code-block lang="javascript">
                export async function handler(event) {
                    console.log('Received event:', JSON.stringify(event, null, 2));
                    const name = event.name || 'World';
                    const response = {
                        message: `Hello, ${name}!`,
                        input: event
                    };
                    return response;
                }
            </code-block>
        </td>
        <td>
            <code-block lang="javascript">
                export async function handler(event) {
                  console.log('Received event:', JSON.stringify(event, null, 2));
                  const name = event.queryStringParameters && event.queryStringParameters.name ? event.queryStringParameters.name : 'World';
                  const response = {
                      statusCode: 200,
                      headers: {
                          "Content-Type": "application/json"
                      },
                      body: JSON.stringify({
                          message: `Hello, ${name}!`
                      })
                  };
                  return response;
                }
            </code-block>
        </td>
    </tr>
    <tr>
      <td>
        <ul>
          <li>The <code>event</code> object will be a JSON object formatted by API Gateway's Integration Request mapping template</li>
          <li>Create a structured <code>response</code> according to API Gateway's Integration Response mapping template</li>
          <li>Return the response to API Gateway</li>
          <li>API Gateway's Integration Request mapping
            <code-block lang="json">
              {
              "name": "$input.path('$.name')"
              }
            </code-block>
          </li>
          <li>API Gateway Integration Response mapping
            <code-block lang="json">
              #set($inputRoot = $input.path('$'))
              {
              "message": "$inputRoot.message",
              "inputReceived": "$inputRoot.input"
              }
            </code-block>
          </li>
        </ul>
      </td>
      <td>
        <ul>
          <li> NOTE extracting the string parameters from the request <b>MANUALLY</b></li>
          <li> NOTE setting <code>response</code> properties like <code>headers</code> and <code>body</code> directly</li>
          <li> Return response to API Gateway</li>
        </ul>
      </td>
    </tr>
</table>

### `put-method` example
Example usage of `put-method`
```shell
aws apigateway put-method \
    --region us-east-1 \
    --rest-api-id example123 \
    --resource-id abcde1 \
    --http-method GET \
    --authorization-type NONE \
    --api-key-required
```
### `put-integration` examples for proxy and custom
<table>
  <tr>
    <td>Proxy</td>
    <td>Custom</td>
  </tr>
  <tr>
    <td>
      <code-block lang="shell">
        aws apigateway put-integration \
        --region us-east-1 \
        --rest-api-id example123 \
        --resource-id abcde1 \
        --http-method GET \
        --type AWS_PROXY \
        --integration-http-method POST \
        --uri arn:aws:apigateway:us-east-1:lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-1:123456789012:function:YourLambdaFunction/invocations
      </code-block>
    </td>
    <td>
      <code-block lang="shell">
        aws apigateway put-integration \
        --region us-east-1 \
        --rest-api-id example123 \
        --resource-id abcde1 \
        --http-method GET \
        --type AWS \
        --integration-http-method POST \
        --uri 'arn:aws:apigateway:us-east-1:lambda:path/2015-03-31/functions/arn:aws:lambda:us-east-1:123456789012:function:YourLambdaFunction/invocations' \
        --request-templates '{"application/json": "{\"param1\": \"$input.params(\'param1\')\"}"}'
      </code-block>
    </td>
  </tr>
  <tr>
    <td>
      <ul>
        <li><code>--type AWS_PROXY</code> indicates that the integration is set up for Lambda Proxy Integration.</li>
        <li><code>--integration-http-method</code> refers to the HTTP method that API Gateway uses to submit the request to Lambda, which is typically <code>POST</code> irrespective of the original method.</li>
      </ul>
    </td>
    <td>
      <ul>
        <li><code>--type AWS</code> indicates that the integration is set up for Lambda Custom Integration.</li>
        <li><code>--request-templates</code> sets the mapping of input parameters from URL to <code>event</code> object</li>
      </ul>
    </td>
  </tr>
</table>

### How this question's lambda code should have been written
The below implementation has been tested to work as expected in AWS.
```Javascript
export function handler (event, context, callback) {
    // Define the response object with appropriate headers for JSON content
    var response = {
        statusCode: 200,
        headers: {
            "Content-Type": "application/json"
        }
    };

    // Check if the 'user' parameter is provided in the event object
    if (!event.user) {
        // If 'user' is missing, return an HTTP 400 Bad Request error with a meaningful error message
        response.statusCode = 400;
        response.body = JSON.stringify({
            error: "Missing the required 'user' parameter."
        });
        callback(null, response);
    } else {
        // If 'user' is provided, create a greeting message and send a successful response
        response.body = JSON.stringify({
            message: "Hello, " + event.user + "!"
        });
        callback(null, response);
    }
}
```
* convert the function from CommonJS Module to ES Module by changing the signature. CommonJS modules are obsolete
* update `response.headers` value to `application/json`. The `*/*` might not behave as expected
* if `user` parameter is missing, return `400 Bad Request`. The original implementation would have responded with `502 Bad Gateway`, which is misleading
* use `JSON.stringify` to serialize JSON objects in response to strings
* callback with `null` to use meaningful response codes and avoid API Gateway errors

### Lambda code example for proxy integration handling different HTTP methods
The **typical** way of setting up the Lambda Proxy Integration in API Gateway is through creating `ANY`**method**.

In this case, the Lambda code will handle the parsing based on which HTTP method is used.
```Javascript
export async function handler(event) {
    const httpMethod = event.httpMethod;
    switch (httpMethod) {
        case 'GET':
            return {
                statusCode: 200,
                headers: {"Content-Type": "application/json"},
                body: JSON.stringify({message: "GET request received"})
            };
        case 'POST':
            return {
                statusCode: 200,
                headers: {"Content-Type": "application/json"},
                body: JSON.stringify({message: "POST request received"})
            };
        default:
            return {
                statusCode: 405,
                headers: {"Content-Type": "application/json"},
                body: JSON.stringify({error: "Method Not Allowed"})
            };
    }
}
```
