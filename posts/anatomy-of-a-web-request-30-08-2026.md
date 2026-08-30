# Anatomy of a Web Request: What Really Happens When You Hit Enter
30-08-2026


**Tags:** `web-development` `networking` `http`

You type `twitter.com`, hit Enter, and roughly a quarter of a second later the page is on your screen. But your browser started with almost nothing: just a name, no address, no connection, no data. Between pressing Enter and seeing pixels, the browser has to run through four distinct protocol layers - DNS, TCP, TLS, and HTTP - before it even touches the actual page content. This post breaks down each layer technically, step by step.

### Why Understand This?

* Computers don't route by name - every request has to resolve to a numeric IP address first
* A "connection" isn't free - TCP requires a handshake before a single byte of page data moves
* HTTPS doesn't hide *that* you're talking to a site, only *what* you're saying
* One page load triggers dozens or hundreds of independent request cycles, not just one
* Caches, CDNs, and load balancers are optimizations layered on top of this exact chain

### The Five Steps

```
1. DNS Lookup      -> resolve twitter.com to an IP address (e.g. 104.244.42.193)
2. TCP Handshake   -> open a reliable connection via a 3-way handshake
3. TLS Handshake   -> authenticate the server and negotiate an encryption key
4. HTTP Request    -> send GET /, receive status code + page content
5. Rendering       -> fetch and process every linked resource (CSS, JS, images, fonts)
```

### Technical Breakdown

* **Step 1 - DNS Lookup:**
  Computers only communicate via numeric IP addresses (e.g. `104.244.42.193`), never by name. When you type `twitter.com`, the browser queries a DNS resolver - run either by your ISP or a public service such as Cloudflare's `1.1.1.1` or Google's `8.8.8.8`. If the resolver has no cached answer, it performs a recursive lookup across three tiers of authority:
  1. It asks a **root nameserver**: "Who handles `.com`?" → pointed to the `.com` nameserver.
  2. It asks the **`.com` nameserver**: "Who handles `twitter.com`?" → pointed to Twitter's own nameserver.
  3. It asks **Twitter's nameserver** directly for the IP address.
  The resolver returns the IP to the browser and caches it locally for minutes to hours, so this entire lookup is skipped on the next visit. Total round-trip time: **20-100ms**.

* **Step 2 - TCP Handshake:**
  Having an IP address isn't enough - the browser needs a reliable channel that guarantees ordered, lossless delivery. This is established via TCP's **three-way handshake**:
  1. Browser → Server: *"Hi, I want to talk."* (SYN)
  2. Server → Browser: *"Got it, I'm ready."* (SYN-ACK)
  3. Browser → Server: *"Great, let's begin."* (ACK)
  This adds **30-100ms**, scaling with physical distance to the server. **HTTP/3** was specifically designed to eliminate this handshake overhead by running over QUIC instead of TCP.

* **Step 3 - TLS Handshake:**
  If the URL uses `https://`, the connection must be encrypted before any data is exchanged. The server presents a digital certificate signed by a trusted Certificate Authority; the browser validates that signature to confirm the server is who it claims to be. Once trust is established, both sides negotiate a shared secret key, and from that point every byte exchanged is encrypted with it. This is what HTTPS actually guarantees: an observer can see *that* you're connecting to Twitter, but not *what* is being sent. This step costs **1-2 additional round trips**; **TLS 1.3** reduces this by parallelizing parts of the negotiation.

* **Step 4 - HTTP Request/Response:**
  Only now does the browser send the actual request - a compact text message specifying the method (`GET`), the path (`/`), the host (`twitter.com`), and metadata such as the browser's user agent and authentication cookies. On the server side, a typical flow looks like:
  1. Read the cookie to identify the user.
  2. Query the database for relevant data (e.g. "what tweets should this person see?").
  3. Assemble a response from that data.
  This can take **10ms** (served from cache) up to **several seconds** (touching multiple backend services). The response includes a status code (`200 OK`, `404 Not Found`, etc.), headers, and the page body.

* **Step 5 - Rendering:**
  The browser now holds raw text, not a rendered page. It parses that text and discovers additional resources it needs - stylesheets, scripts, images, fonts, video - and each one triggers its **own independent run through steps 1-4** (DNS, TCP, TLS, HTTP). Browsers mitigate this by reusing open connections and by serving many of these resources from geographically nearby servers (CDNs), but a single page load can still involve **dozens or hundreds of separate requests**.

### Summary

| Layer | Responsibility |
|---|---|
| DNS | Name → IP address |
| TCP | Opens a reliable connection |
| TLS | Encrypts the connection |
| HTTP | The actual question and answer |
| Browser | Converts the response into a rendered page |

Every subsequent system-design concept - caching, CDNs, load balancers, databases - exists to make one specific step in this chain faster or more reliable. Understanding this chain is the foundation for reasoning about all of them.

## Sources
[freesystemdesign.com](https://freesystemdesign.com/)

[MDN Web Docs - An overview of HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview)
