[Back](index.md)

# Server

## Basic Server

Basic server with express and socket.io in javascript.

```javascript
const express = require("express");
const http = require("http");
const socketIo = require("socket.io");

const app = express();
const server = http.createServer(app);
const io = socketIo(server);

const PORT = 3000;

server.listen(PORT, () => {
    console.log(`Server running on http://localhost:${PORT}`);
});
```

## Cors

Cors is some kind of routing that needs to be set up to connect client and server.

```javascript
const cors = require("cors");

const io = socketIo(server, { cors: { origin: "*" } });

app.use(cors());
```

[Back](index.md)

## Ping

This is code to emit ping event to client to test connection.

```javascript
io.on("connection", (socket) => {
    console.log("New client connected");

    // Send a ping every 5 seconds
    setInterval(() => {
        socket.emit("ping", `Ping at ${new Date().toLocaleTimeString()}`);
    }, 5000);

    socket.on("disconnect", () => {
        console.log("Client disconnected");
    });
});
```
