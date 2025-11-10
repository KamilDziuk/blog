# Real Time Chat With Node.js And WebSockets

Real-time communication has become a crucial feature in modern web applications — from chat apps and live notifications to multiplayer games and collaborative tools. In this post, we’ll explore how to implement WebSockets in **Node.js**, and we’ll take a look at a practical example using my  project:  
 [Live Chat on GitHub](https://github.com/KamilDziuk/live-chat)  
 [Demo Live Chat App](https://livechat-fue3.onrender.com/)

---

## What Are WebSockets?

**WebSockets** provide a full-duplex communication channel over a single TCP connection. Unlike HTTP — which follows a request–response pattern — WebSockets enable continuous, bi-directional communication between the client and the server.

This means both sides can send data at any time without having to establish a new connection for each message.

### Key Features

- Persistent connection between client and server  
- Low latency and real-time data exchange  
- Works well for chat apps, games, and live dashboards  
- Compatible with Node.js via libraries like `ws` or `socket.io`

---

## Why Node.js for WebSockets?

Node.js is built around an **event-driven, non-blocking I/O model**, which makes it perfect for handling multiple concurrent connections efficiently.  
Its ecosystem also offers mature WebSocket libraries that simplify setup and scaling.

---

## Example: Real-Time Chat Application

Let’s walk through a real-world example: a **live chat app** built with Node.js, Express, and Socket.IO.  
You can explore the full codebase here:  
[GitHub Repository – live-chat](https://github.com/KamilDziuk/live-chat)

### Project Overview

The app allows users to:
- Join a chat room instantly  
- Send and receive messages in real time  
- See when new users join or leave the room  

### Core Technologies

- **Node.js + Express** – backend server  
- **Socket.IO** – WebSocket abstraction for real-time messaging  
- **Render.com** – for hosting the live demo

---

## Implementation Highlights

Here’s a simplified look at how the backend works:

```js
// server.js
import express from "express";
import { createServer } from "http";
import { Server } from "socket.io";

const app = express();
const httpServer = createServer(app);
const io = new Server(httpServer);

io.on("connection", (socket) => {
  console.log("A user connected:", socket.id);

  socket.on("chatMessage", (msg) => {
    io.emit("chatMessage", msg);
  });

  socket.on("disconnect", () => {
    console.log("User disconnected:", socket.id);
  });
});

server.listen(3000, () => {
  console.log("Server running on http://localhost:3000");
});
