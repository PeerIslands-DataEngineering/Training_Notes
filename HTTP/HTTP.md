### Introduction to HTTP

HTTP (Hypertext Transfer Protocol) is a protocol used for transmitting hypermedia documents, such as HTML, across the internet. It is the foundation of any data exchange on the Web and a protocol used for communication between a web browser (client) and a web server.

### HTTP Methods

HTTP defines several methods that indicate the desired action to be performed on the identified resource. Here are the most commonly used HTTP methods:

1. **GET**: 
   - Retrieves data from the server. 
   - Should not change the state of the resource (idempotent).
   - Commonly used to request web pages or resources.

   ```http
   GET /index.html HTTP/1.1
   Host: www.example.com
   ```

2. **POST**: 
   - Submits data to the server, typically causing a change in state or side effects on the server.
   - Commonly used for form submissions or uploading data.

   ```http
   POST /submit-form HTTP/1.1
   Host: www.example.com
   Content-Type: application/x-www-form-urlencoded

   name=John&age=30
   ```

3. **PUT**: 
   - Updates a resource or creates it if it does not exist (idempotent).
   - Often used for updating resources.

   ```http
   PUT /user/123 HTTP/1.1
   Host: www.example.com
   Content-Type: application/json

   {
     "name": "John Doe",
     "age": 30
   }
   ```

4. **DELETE**: 
   - Deletes a specified resource (idempotent).

   ```http
   DELETE /user/123 HTTP/1.1
   Host: www.example.com
   ```

5. **PATCH**: 
   - Applies partial modifications to a resource.
   - Not necessarily idempotent.

   ```http
   PATCH /user/123 HTTP/1.1
   Host: www.example.com
   Content-Type: application/json

   {
     "age": 31
   }
   ```

6. **HEAD**: 
   - Similar to GET but only retrieves the headers and status line, without the body.
   - Useful for checking if a resource exists or retrieving metadata.

   ```http
   HEAD /index.html HTTP/1.1
   Host: www.example.com
   ```

7. **OPTIONS**: 
   - Describes the communication options for the target resource.
   - Used for CORS preflight requests.

   ```http
   OPTIONS /index.html HTTP/1.1
   Host: www.example.com
   ```

### HTTP Status Codes

HTTP status codes are issued by the server in response to a client's request and indicate whether a specific HTTP request has been successfully completed. Status codes are divided into five categories:

1. **1xx (Informational)**:
   - The request was received, and the process is continuing.

   - **100 Continue**: The client should continue with its request.
   - **101 Switching Protocols**: The server is switching protocols as requested by the client.

2. **2xx (Success)**:
   - The action was successfully received, understood, and accepted.

   - **200 OK**: The request was successful.
   - **201 Created**: The request was successful, and a new resource was created.
   - **202 Accepted**: The request has been accepted for processing but is not yet complete.
   - **204 No Content**: The server successfully processed the request, but no content is returned.

3. **3xx (Redirection)**:
   - Further action must be taken to complete the request.

   - **301 Moved Permanently**: The requested resource has been permanently moved to a new URL.
   - **302 Found**: The requested resource is temporarily available at a different URL.
   - **304 Not Modified**: The resource has not been modified since the last request.

4. **4xx (Client Error)**:
   - The request contains bad syntax or cannot be fulfilled.

   - **400 Bad Request**: The server could not understand the request due to invalid syntax.
   - **401 Unauthorized**: Authentication is required and has failed or not been provided.
   - **403 Forbidden**: The client does not have permission to access the requested resource.
   - **404 Not Found**: The server cannot find the requested resource.

5. **5xx (Server Error)**:
   - The server failed to fulfill an apparently valid request.

   - **500 Internal Server Error**: The server encountered an unexpected condition.
   - **501 Not Implemented**: The server does not support the functionality required to fulfill the request.
   - **502 Bad Gateway**: The server received an invalid response from the upstream server.
   - **503 Service Unavailable**: The server is currently unable to handle the request due to temporary overloading or maintenance.

### Example of an HTTP Transaction

#### Request:
```http
GET /index.html HTTP/1.1
Host: www.example.com
```

#### Response:
```http
HTTP/1.1 200 OK
Content-Type: text/html
Content-Length: 123

<!DOCTYPE html>
<html>
<head>
  <title>Example</title>
</head>
<body>
  <h1>Hello, World!</h1>
</body>
</html>
```

In this example:
- The client sends a `GET` request for the `/index.html` resource.
- The server responds with a `200 OK` status code, indicating the request was successful, and provides the requested HTML content.

Understanding HTTP methods and status codes is fundamental for working with web technologies, as they form the basis of client-server communication on the web.