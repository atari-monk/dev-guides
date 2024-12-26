[Back](index.md)

# Socket.io

## Content

1. [Server](#server)
2. [Client](#client)

## Server

### Basic Server

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

### Cors

Cors is some kind of routing that needs to be set up to connect client and server.

```javascript
const cors = require("cors");

const io = socketIo(server, { cors: { origin: "*" } });

app.use(cors());
```

### Ping

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

[Content](#content)

## Client

Basic socket.io client to test connection with server using ping event.

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>Socket.IO Ping Client</title>
        <script src="https://cdn.socket.io/4.6.0/socket.io.min.js"></script>
        <style>
            body {
                font-family: Arial, sans-serif;
                margin: 20px;
            }
            #messages {
                border: 1px solid #ddd;
                padding: 10px;
                height: 200px;
                overflow-y: scroll;
            }
            .message {
                margin: 5px 0;
            }
        </style>
    </head>
    <body>
        <h1>Ping Messages</h1>
        <div id="messages">
            <!-- Messages will appear here -->
        </div>

        <script>
            const socket = io("http://localhost:3000");

            socket.on("ping", (message) => {
                const messagesDiv = document.getElementById("messages");
                const newMessage = document.createElement("div");
                newMessage.className = "message";
                newMessage.textContent = message;
                messagesDiv.appendChild(newMessage);

                // Scroll to the bottom
                messagesDiv.scrollTop = messagesDiv.scrollHeight;
            });
        </script>
    </body>
</html>
```

[Content](#content)  
[Back](index.md)
