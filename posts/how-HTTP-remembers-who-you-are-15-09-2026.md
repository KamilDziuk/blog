# Session Management: How HTTP Remembers Who You Are
15-09-2026


**Tags:** `scaling` `web-development` `authentication` `http`

You log in once, and then every page you open already knows who you are. But HTTP itself is stateless - every request stands completely alone, and the server has no memory of what happened a moment ago. The illusion of "being logged in" is something you have to build on top of a protocol that forgets you instantly. This post breaks down the two ways real systems do it: server-side sessions and signed tokens.

### Why Understand This?

* HTTP has no built-in concept of a user - identity is something you bolt on yourself
* The client must carry an identifier on every single request, or the server sees a stranger
* Where you store session state decides how easily you can scale horizontally
* Immediate logout and token-based auth pull in opposite directions
* Almost every auth bug in production traces back to one of these two models being misapplied

### The Two Approaches

```
Server-side sessions -> client carries a key, server holds the data
  Login    -> server generates random ID "abc123"
  Store    -> shared store maps "abc123" to user 42
  Cookie   -> Set-Cookie: session=abc123
  Request  -> server looks up the ID on every request

Tokens (JWT)         -> client carries the data, server holds only a secret
  Login    -> server signs { "user_id": 42, "exp": 1735689600 }
  Store    -> nothing stored server-side
  Client   -> browser keeps the token and sends it every time
  Request  -> server verifies the signature, no lookup needed
```

### Technical Breakdown

* **The problem - HTTP is stateless:**
  Every request stands alone. The server has no memory of the previous one. But your app needs to know who is logged in, so the client has to carry some kind of ID with every request, and the server has to turn that ID back into "who is this person?" There are two main ways to do that, and both are used in real systems. The right choice depends on what you are building.

* **Approach 1 - Server-side sessions:**
  When you log in, the server generates a random ID, for example `abc123`. It saves a row in a session table that says "session `abc123` belongs to user 42." Then it sets a cookie in the response: `Set-Cookie: session=abc123`. From then on your browser automatically sends that cookie with every request. The server reads the cookie, looks up `abc123`, and figures out you are user 42.
  Where does that table live? Not in one web server's memory - that would tie every user to one specific machine. It lives in a shared store such as **Redis**, **Memcached**, or a database, so any web server can resolve any session ID. This is how traditional web apps worked for a long time, and it is still common.

* **Approach 2 - Tokens (JWT):**
  The other approach skips the session table entirely. When you log in, the server hands you a signed token - most commonly a **JWT** (JSON Web Token). The token itself contains your user info, such as `{ "user_id": 42, "exp": 1735689600 }`, plus a cryptographic signature generated with a secret key that only the server knows.
  Your browser stores the token (in a cookie or in browser storage) and sends it on every request. The server verifies the signature against its secret key, and if the signature is valid it trusts the contents. **No table lookup needed.**
  *The good part:* no shared session store. Every server verifies tokens on its own, so scaling horizontally is trivial, and tokens can be passed between services without each one looking the user up.
  *The bad part:* tokens are hard to invalidate. If a user logs out, or you want to ban them, their existing tokens keep working until they expire. The usual workaround is short expirations plus a refresh token system - or a revocation list, which puts you right back to needing a shared store. This is the dominant pattern in modern APIs, especially with mobile apps and third-party clients.

* **Which one to pick:**
  Use **server-side sessions** when you need to invalidate sessions immediately (an admin has to be able to force-log-out a user), when the per-session data is large and you do not want every request carrying it, or when you are building a traditional web app and the user is mostly on one device.
  Use **tokens** when you are building APIs consumed by mobile apps or third-party clients, when you want to scale across many independent services without coordinating session lookups, or when you run stateless serverless functions with no convenient place to keep a session.
  A lot of real systems mix both: short-lived tokens for API auth, with refresh tokens stored server-side so they can be revoked when needed.

### Summary

| Aspect | Server-side sessions | Tokens (JWT) |
|---|---|---|
| What the client carries | A key (session ID) | The data itself, signed |
| Server-side storage | Shared store required | None needed |
| Per-request cost | One store lookup | Signature verification |
| Immediate revocation | Easy | Hard |
| Horizontal scaling | Needs shared store | Trivial |
| Best fit | Traditional web apps | APIs, mobile, microservices |

Session management is the point where a stateless protocol meets an application that very much needs state. Every choice you make here - where the state lives, who carries it, how fast you can revoke it - ripples outward into how your system scales.

## Sources
[freesystemdesign.com](https://freesystemdesign.com/)

[MDN Web Docs - Using HTTP cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies)
