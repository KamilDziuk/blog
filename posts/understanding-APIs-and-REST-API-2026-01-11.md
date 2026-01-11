
# Understanding APIs and REST API
2026-01-11

**Tags:**  `REST API` 

## Introduction

APIs (Application Programming Interfaces) are the backbone of modern web development. Almost every application today — from simple websites to complex SaaS platforms — relies on APIs to communicate between systems.

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

You don’t need to know how the kitchen works — only how to order.

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

```

GET    /api/users
GET    /api/users/1
POST   /api/users
PUT    /api/users/1
DELETE /api/users/1

````

Each URL represents a **resource**.

---

## REST API Response Example (JSON)

```json
{
  "id": 1,
  "name": "John Doe",
  "email": "john@example.com"
}
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
fetch("https://api.example.com/users", {
  method: "POST",
  headers: {
    "Content-Type": "application/json"
  },
  body: JSON.stringify({
    name: "Alice",
    email: "alice@example.com"
  })
})
.then(res => res.json())
.then(data => console.log(data));
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

app.get("/api/users", (req, res) => {
  res.json(users);
});

app.post("/api/users", (req, res) => {
  const user = req.body;
  users.push(user);
  res.status(201).json(user);
});
```

---

## REST API Status Codes

| Code | Meaning      |
| ---- | ------------ |
| 200  | OK           |
| 201  | Created      |
| 400  | Bad Request  |
| 401  | Unauthorized |
| 404  | Not Found    |
| 500  | Server Error |

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



