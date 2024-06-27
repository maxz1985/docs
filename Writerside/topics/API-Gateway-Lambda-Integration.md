# API Gateway Lambda Integration

## Question
You have created an API Gateway API to expose the `GET /user?user={name}` method to invoke a Lambda function.
The method has a query string called "user." 
The Lambda function will respond with a message of `Hello, {name}!`. 
You have deployed the Lambda function as follows:

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