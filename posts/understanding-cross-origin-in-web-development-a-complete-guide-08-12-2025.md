
# Understanding Cross-Origin in Web Development: A Complete Guide
2025-12-08

**Tags:** `cross-origin` `web-security` `web-development` `browser` `http` `https`

Modern web applications often communicate with servers, APIs, and external services. While this makes the web incredibly powerful, it also introduces important security challenges.  
One of the most fundamental security concepts in the browser environment is **Cross-Origin** — a rule that defines which websites can interact with each other.

This post breaks down what *Cross-Origin* means, why it exists, how CORS works, and shows both common and extreme cases you may encounter as a developer.

---

## What Does “Origin” Mean?

An **origin** is defined by the combination of:

- **protocol** (http, https)
- **domain** (example.com)
- **port** (optional — e.g., :3000)

### Example origins:

| URL | Origin |
|-----|--------|
| `https://example.com` | https + example.com + 443 |
| `http://localhost:3000` | http + localhost + 3000 |
| `https://api.example.com` | Different *subdomain* → different origin |

If **any** of the three components differ, the origin is different.

---

## Same-Origin Policy (SOP)

The **Same-Origin Policy** is a browser security mechanism that restricts websites from interacting with each other unless explicitly allowed.

The idea is simple:

> A web page can freely interact only with resources that share the same origin.

This prevents malicious websites from reading sensitive data from other sites (e.g., cookies, localStorage, sessions).

---

## What is Cross-Origin?
 
A **cross-origin request** happens when a web page on origin A tries to access a resource on origin B.

Examples:

- `https://myapp.com` → requests → `https://api.myapp.com`
- `http://localhost:3000` → requests → `http://localhost:5000`
- `https://frontend.com` → requests → `https://thirdpartyapi.com`

Browsers block these requests **unless the server explicitly allows them** using CORS.

---

# CORS: Cross-Origin Resource Sharing

**CORS** is a protocol that tells the browser which external origins are allowed to access a server’s resources.

### A server can allow access by adding headers like:

```http
Access-Control-Allow-Origin: https://myfrontend.com
````

Or allow all origins:

```http
Access-Control-Allow-Origin: *
```

---

## Common Cross-Origin Use Cases

### **1. Frontend + Backend Local Development**

Your React/Vue frontend runs at:

```
http://localhost:3000
```

Your Node.js/Express backend runs at:

```
http://localhost:5000
```

Different ports → **different origin** → browser blocks requests without CORS.

---

### **2. Using Third-Party APIs**

Your website calls:

```
https://api.openweathermap.org
```

Since this is a different domain, it is a **cross-origin request**.
The API server must set CORS headers to allow your domain to use it.

---

### **3. CDN or Asset Hosting**

You load an image from:

```
https://cdn.example.com/image.png
```

Since the CDN uses another subdomain → **cross-origin**.

Browsers often allow images, scripts, and CSS cross-origin, but restrict sensitive data access.

---

# Extreme or Rare Cross-Origin Cases

Here are unusual scenarios developers rarely encounter but should understand.

---

## 1. Cross-Origin Requests With Cookies (Credentialed Requests)

When a frontend sends cookies or authentication headers to a different origin:

```js
fetch("https://api.example.com/user", {
  credentials: "include"
});
```

The server must explicitly allow this:

```http
Access-Control-Allow-Origin: https://frontend.com
Access-Control-Allow-Credentials: true
```

 **You cannot use `*` with credentials.**

---

## 2. Private Networks (IoT / Local Devices)

A web page tries to access:

* routers
* smart home devices
* cameras
* local servers

Example:

```
http://192.168.0.1/api/settings
```

Browsers treat this as a *high-risk* cross-origin request.
Modern browsers (Chrome 98+) require:

```http
Access-Control-Allow-Private-Network: true
```

This is an extreme case because it involves security risks in private networks.

---

## 3. Cross-Origin iframes + Script Access

If you embed:

```html
<iframe src="https://another-origin.com"></iframe>
```

You **cannot** access its DOM:

```js
iframe.contentWindow.document //  Blocked
```

This is to prevent malicious pages from stealing data inside embedded apps.

---

## 4. Cross-Origin WebSockets

WebSockets are *not* restricted by SOP the same way as AJAX, but servers often enforce manual origin checks:

* Browsers send the `Origin` header
* WebSocket servers may choose to reject it

This is an uncommon use case but matters for real-time applications (chat, notifications).

---

Common Solutions to Cross-Origin Problems

### **1. Setting CORS headers on the backend**

(Express example)

```js
const cors = require("cors");
app.use(cors({ origin: "https://myfrontend.com" }));
```

---

### **2. Reverse Proxy**

Examples:

* Nginx
* Vite proxy
* Create React App proxy

Frontend sends requests to:

```
/api
```

Proxy forwards them to:

```
https://backend.com
```

Looks like same-origin from the browser’s perspective.

---

### **3. Same-Site Cookies**

Avoid exposing cookies cross-origin:

```http
Set-Cookie: session=abc; SameSite=Lax; Secure
```

---

### **4. Using server-side rendering**

SSR can fetch data **server-to-server** (not limited by CORS), then send HTML to the browser.

---

# Simple Example of a CORS Error

Browser error:

```
Access to fetch at 'https://api.example.com/data'
from origin 'http://localhost:3000'
has been blocked by CORS policy.
```

Solution on server:

```http
Access-Control-Allow-Origin: http://localhost:3000
```

---

# Example from project

```js
export default async function handler(req, res) {

  res.setHeader("Access-Control-Allow-Origin", "https://kamildziuk.github.io");
  res.setHeader("Access-Control-Allow-Methods", "GET");
  res.setHeader("Access-Control-Allow-Headers", "Content-Type");

  if (req.method === "OPTIONS") {
    return res.status(200).end();
  }

  try {
    const response = await fetch(
      "https://api.github.com/repos/KamilDziuk/blog/traffic/views",
      {
        headers: {
          Authorization: `Bearer ${process.env.GITHUB_TOKEN}`,
          Accept: "application/vnd.github+json",
        },
      }
    );

    const data = await response.json();

    res.setHeader(
      "Cache-Control",
      "s-maxage=3600, stale-while-revalidate"
    );

    return res.status(200).json({
      count: data.count,
      uniques: data.uniques,
    });

  } catch (error) {
    return res.status(500).json({ error: "Server error" });
  }
}
```

---

# Summary

Cross-origin rules exist to protect users from malicious websites by restricting access between different domains.

**Key points:**

* An *origin* consists of protocol + domain + port.
* Browsers enforce the **Same-Origin Policy**.
* **CORS** defines which cross-origin requests are allowed.
* Common use cases include local dev, CDNs, and third-party APIs.
* Extreme cases involve private networks, credentialed requests, iframes, and WebSockets.
* CORS can be managed through server headers, proxies, SSR, and proper cookie settings.

Understanding Cross-Origin behavior is essential for building secure and functional web applications.

---

## 📚 Sources  

[Developer Mozilla Cross-Origin Resource Sharing (CORS)](https://developer.mozilla.org/en-US/docs/Web/HTTP/Guides/CORS#:~:text=Cross%2DOrigin%20Resource%20Sharing%20(CORS)%20is%20an%20HTTP%2D,transfers%20between%20browsers%20and%20servers.)

[Web Dev Simplified - Learn CORS In 6 Minutes](https://www.youtube.com/watch?v=PNtFSVU-YTI)
