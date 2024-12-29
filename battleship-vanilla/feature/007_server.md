[Back](index.md)

# Server

Server is encapsulated in library and class.

## Code

server_lib/src/server

```javascript
import express from "express";
import http from "http";
import { Server } from "socket.io";
import cors from "cors";

export class Server {
    constructor(port = 3000) {
        this.port = port;
        this.app = express();
        this.server = http.createServer(this.app);
        this.io = new Server(this.server, { cors: { origin: "*" } });

        this.handlers = [];

        this.initializeMiddleware();
        this.initializeSocketHandlers();
    }

    initializeMiddleware() {
        this.app.use(cors());
    }

    initializeSocketHandlers() {
        this.io.on("connection", (socket) => {
            console.log("New client connected");

            this.handlers.forEach((handler) => handler(socket));

            socket.on("disconnect", () => {
                console.log("Client disconnected");
            });
        });
    }

    registerHandler(handler) {
        if (typeof handler === "function") {
            this.handlers.push(handler);
        } else {
            console.error("Handler must be a function");
        }
    }

    start() {
        this.server.listen(this.port, () => {
            console.log(`Server running on http://localhost:${this.port}`);
        });
    }
}
```

## package.json

```json
{
    "name": "server_lib",
    "version": "1.0.0",
    "type": "module",
    "main": "src/index.js",
    "scripts": {},
    "keywords": [],
    "author": "",
    "description": ""
}
```

## Export/Import

Export

```javascript
export * from "./Server.js";
```

Import in server

```javascript
import { Server } from "server_lib";
```

[Back](index.md)
