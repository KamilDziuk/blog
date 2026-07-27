
# Understanding APIs / REST API / Web API
2026-01-11

**Tags:**  `REST API` `API` `Web API` `Node.js` `Express.js`  `Fetch API` `REST Architecture` `HTTP Methods` `Client-Server Architecture` `HTTP` `JSON`  `axios`     

## Introduction

APIs (Application Programming Interfaces) are the backbone of modern web development. Almost every application today - from simple websites to complex SaaS platforms - relies on APIs to communicate between systems.

APIs are more important than ever due to:
- cloud-native architectures
- microservices
- headless CMS solutions
- mobile and IoT integrations

This article explains **what APIs are**, **how REST APIs work**, and shows **practical JavaScript examples** for both consuming and creating REST APIs.

---

## What Is an API?

An **API (Application Programming Interface)** is a set of rules that allows different software systems to communicate with each other.

### Real-world analogy:
Think of an API as a waiter in a restaurant:
- You (client) request food
- The waiter (API) sends the request to the kitchen (server)
- The kitchen prepares the food
- The waiter brings it back to you

You don’t need to know how the kitchen works - only how to order.

---

## Types of APIs

- **REST APIs** (most common)
- GraphQL APIs
- SOAP APIs
- WebSockets
- gRPC APIs

This article focuses on **REST APIs**, which dominate web development.

---

## What Is a REST API?

**REST (Representational State Transfer)** is an architectural style for designing networked applications.

A REST API:
- Uses HTTP
- Is stateless
- Operates on resources
- Returns data (usually JSON)

### Common HTTP Methods

| Method | Purpose |
|------|--------|
| GET | Retrieve data |
| POST | Create new data |
| PUT | Update entire resource |
| PATCH | Update part of a resource |
| DELETE | Remove data |

---

## REST API Resource Example

A REST API for users might look like:

````
GET    /users        - list all users
POST   /users         - create a user

GET    /color         - get current color setting
POST   /color         - set/update color setting

PUT    /users/:id     - update a user
DELETE /users/:id     - delete a user
````

Each URL represents a **resource**.

---

## REST API Response Example (JSON or Plain Text)
Content-Type: application/json

````json
{
  "id": 1,
  "name": "John Doe",
  "email": "john@example.com"
}
````
Content-Type: text/plain

````text
red
````
---

## REST API Principles

1. **Stateless** – no session stored on server
2. **Client-Server separation**
3. **Uniform interface**
4. **Cacheable responses**
5. **Layered system**

---

## Consuming a REST API in JavaScript (Fetch API)

### Example: GET request

```js
fetch("https://api.example.com/users")
  .then(response => response.json())
  .then(data => {
    console.log("Users:", data);
  })
  .catch(error => {
    console.error("Error:", error);
  });
```

---

### Example: POST request

```js
fetch("/users", {
  method: "POST",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    name: "Larysa",
    email: "larysa@example.com"
  })
});
```

### Using  axios

Advantages of Axios:
- **Automatic JSON parsing** - no need to call `response.json()`. The response data is available directly in `response.data`.
- **Better error handling** - Axios automatically throws an error for HTTP `4xx` and `5xx` responses.
- **Interceptors** - automatically add headers (e.g. a JWT token) to every request or handle all responses and errors in one place.
- **Timeout support** - easily set a maximum waiting time for a server response. Axios automatically aborts the request if the timeout is exceeded.
- **Cleaner syntax** - code is usually shorter and easier to read than with `fetch`.

#### POST

```js
  axios
      .post("/color", {
       colorType: chatBackgroundColor,
      })
      .then(() => {
        console.log("success")
      })
      .catch((err) => {
        console.error("error", err.message);
      });
```

#### GET

```js
axios
  .get("/color", {
    params: {
        background: "#fff",
    }
  })
  .then((response) => {
    console.log(response.data);
  })
  .catch((err) => {
    console.error("error", err.message);
  });
```

---

## Creating a REST API with Node.js & Express

### Setup

```bash
npm init -y
npm install express
```

---

### Basic Express Server

```js
const express = require("express");
const app = express();

app.use(express.json());

app.listen(3000, () => {
  console.log("Server running on port 3000");
});
```

---

### REST Endpoints Example

```js
let users = [];

app.get("/users", (req, res) => {
  res.json(users);
});

app.post("/users", (req, res) => {
  const user = req.body;
  users.push(user);
  res.status(201).json(user);
});
```

---

## REST API Status Codes

HTTP status codes inform the client about the outcome of a request. They are divided into several categories:

**1xx** - Informational responses
**2xx** - Successful responses
**3xx** - Redirection messages
**4xx** - Client errors
**5xx** - Server errors

| Code | Meaning      | Description |
|------|--------------|------------|
| 100  | Continue     | Indicates that the initial part of the request has been received successfully.|
| 101  | Switching Protocols    | Indicates that the initial part of the request has been received successfully.|
| 200  | OK           | The request completed successfully.|
| 201  | Created      | A new resource has been successfully created.|
| 202  | Accepted     | The request has been accepted for processing but is not yet complete.|
| 204  | No Content   | The request succeeded, but there is no response body.| 
| 301  | Moved Permanently   | The requested resource has permanently moved to another URL.| 
| 302  | Found   | Temporary redirect to another location.| 
| 304  | Not Modified | Cached version is still valid.| 
| 400  | Bad Request  | The client sent invalid or malformed data.| 
| 401  | Unauthorized | Authentication is required or the credentials are invalid.| 
| 404  | Not Found    | The requested resource does not exist.|
| 405  | Method Not Allowed  | The HTTP method is not supported for the requested resource.| 
| 409  | Conflict  | The request conflicts with the current state of the resource.| 
| 422  | Unprocessable Content    | The request format is correct, but the data fails validation. |
| 429  | Too Many Requests   | The client exceeded the allowed request limit.| 
| 500  | Internal Server Error | An unexpected server-side error occurred.|
| 501  | Not Implemented |The server does not support the requested functionality.| 
| 502  | Bad Gateway | An upstream server returned an invalid response.| 
| 503  | Service Unavailable | The server is temporarily unavailable.|
| 504  | Gateway Timeout | The upstream server took too long to respond.| 

---

## Security Best Practices

* Use HTTPS
* Validate input data
* Use authentication (JWT, OAuth)
* Rate limiting
* CORS configuration

---

## REST API - Best Practices

* API-first development
* OpenAPI (Swagger) documentation
* Versioning (`/v1/`, `/v2/`)
* Monitoring & logging
* AI-powered API testing

---

## Conclusion

REST APIs remain the foundation of modern web applications. They are simple, scalable, and widely supported. Mastering REST APIs and JavaScript integration is a must-have skill for every web developer.

##  Sources

[axios](https://github.com/axios/axios?tab=readme-ov-file)

[amazon](https://aws.amazon.com/what-is/api/)

[redhat](https://www.redhat.com/en/topics/api/what-is-a-rest-api)

[postman](https://blog.postman.com/rest-api-examples/)
