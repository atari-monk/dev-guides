[Back](index.md)

# Reconnect

## Content

1. [Reconnect](#reconnect-1)
2. [Refactor](#refactor)

## Reconnect

In a typical Socket.IO setup for a server-client architecture, handling reconnections involves ensuring that the client can reconnect seamlessly after a disconnection, while maintaining some form of unique identification for the player. One of the simplest and standard ways to achieve this is by using a **unique player ID** stored in the browser's **localStorage**.

### Steps for Establishing Reconnection with a Unique ID:

1. **Generate a Unique ID** (if it doesn't exist already):

    - On the client side, you can generate a unique ID for each player and store it in the browser’s `localStorage`. This ID will persist even after page reloads, ensuring that the client is always recognized even if they disconnect and reconnect.

2. **Connect to Socket.IO Server**:

    - When the client connects, the unique ID from `localStorage` is sent to the server. This helps the server identify the player and re-establish their session, if needed.

3. **Handle Reconnection**:
    - Socket.IO automatically handles reconnections, but you can enhance this behavior by handling events on both the client and server.

### Code Example:

#### Client-Side (JavaScript):

```javascript
// Generate a unique ID if it doesn't exist in localStorage
let playerId = localStorage.getItem("playerId");
if (!playerId) {
    playerId = generateUniqueId();
    localStorage.setItem("playerId", playerId);
}

// Function to generate a simple unique ID (e.g., a random string)
function generateUniqueId() {
    return "player-" + Math.random().toString(36).substr(2, 9);
}

// Connect to the Socket.IO server
const socket = io();

// Send player ID to the server when the client connects
socket.on("connect", () => {
    socket.emit("registerPlayer", playerId);
});

// Handle reconnections
socket.on("reconnect", () => {
    console.log("Reconnected with player ID: " + playerId);
});

socket.on("disconnect", () => {
    console.log("Disconnected. Trying to reconnect...");
});
```

#### Server-Side (Node.js with Socket.IO):

```javascript
const io = require("socket.io")(server);

// Store player sessions (in-memory example)
let playerSessions = {};

io.on("connection", (socket) => {
    console.log("New connection: " + socket.id);

    // Listen for the player registration event
    socket.on("registerPlayer", (playerId) => {
        console.log("Player connected with ID: " + playerId);

        // Optionally, store the player session based on player ID
        playerSessions[playerId] = socket.id;

        // Optionally, send the player a welcome message or reconnection confirmation
        socket.emit("welcome", `Welcome back, player ${playerId}`);
    });

    // Handle disconnections
    socket.on("disconnect", () => {
        console.log("Player disconnected: " + socket.id);
    });
});
```

### Explanation:

-   **Unique Player ID**: The unique ID is generated and stored in `localStorage` if it doesn’t exist. This ID is used to re-establish the player's identity after a reconnection.
-   **Server Handling**: The server listens for the `registerPlayer` event, which contains the unique ID. When a player reconnects, they send their ID back to the server, and the server can use this to identify the player and manage their session.
-   **Reconnection**: Socket.IO handles automatic reconnection. When the client disconnects and reconnects, it continues to use the same unique ID stored in `localStorage` for identification.

### Additional Notes:

-   You can use a more advanced mechanism (such as JWT tokens) for authentication if your game requires it.
-   The `reconnect` event provided by Socket.IO automatically attempts to reconnect the client to the server, and you can customize this process based on your application’s requirements (e.g., adding custom backoff strategies).

This method ensures that the player is always recognized, even if they disconnect and reconnect, by relying on the browser's `localStorage` to persist a unique player ID.

[Content](#content)

## Refactor

### Client PlayerIdService

Here's the encapsulated version of your code in a class:

```javascript
class PlayerIdService {
    constructor() {
        this.playerIdKey = "playerId";
        this.playerId = this.getOrCreatePlayerId();
    }

    getOrCreatePlayerId() {
        let playerId = localStorage.getItem(this.playerIdKey);
        if (!playerId) {
            playerId = this.generateUniqueId();
            localStorage.setItem(this.playerIdKey, playerId);
        }
        return playerId;
    }

    generateUniqueId() {
        return "player-" + Math.random().toString(36).slice(2, 9);
    }

    getPlayerId() {
        return this.playerId;
    }
}

const playerIdService = new PlayerIdService();
console.log(playerIdService.getPlayerId());
```

This class encapsulates the functionality into a reusable tool that manages the retrieval or creation of a unique player ID.

### Client SocketService

Here's how you can encapsulate the reconnect logic into a class:

```javascript
export const SocketEvents = {
    Client: {
        PING: "ping",
        CONNECT: "connect",
        RECONNECT: "reconnect",
        DISCONNECT: "disconnect",
        REGISTER_PLAYER: "registerPlayer",
    },
    Server: {
        CONNECTION: "connection",
    },
};
```

```javascript
import { SocketEvents } from "/shared/src/index.js";

export class SocketService {
    constructor(socket, playerId) {
        this.socket = socket;
        this.playerId = playerId;

        this.setupListeners();
    }

    setupListeners() {
        this.socket.on(SocketEvents.Client.CONNECT, () => this.handleConnect());
        this.socket.on(SocketEvents.Client.RECONNECT, () =>
            this.handleReconnect()
        );
        this.socket.on(SocketEvents.Client.DISCONNECT, () =>
            this.handleDisconnect()
        );
    }

    handleConnect() {
        console.log(`Connected with player ID: ${this.playerId}`);
        this.registerPlayer();
    }

    registerPlayer() {
        this.socket.emit(SocketEvents.Client.REGISTER_PLAYER, this.playerId);
    }

    handleReconnect() {
        console.log(`Reconnected with player ID: ${this.playerId}`);
        this.registerPlayer();
    }

    handleDisconnect() {
        console.log("Disconnected. Trying to reconnect...");
    }
}

// Usage
const socket = io();
const playerId = "examplePlayerId";
const socketService = new SocketService(socket, playerId);
```

#### Explanation:

1. **Encapsulation:** The `SocketService` class encapsulates all socket-related logic.
2. **Reusability:** You can reuse the class for different sockets and player IDs.
3. **Maintainability:** The class isolates the connection logic, making the code easier to manage and extend.
4. **Method Extraction:** The `registerPlayer` method is extracted for clarity and reusability.

### Server PlayerSessionService

Here's how you can encapsulate your code into a class to manage player sessions and socket connections more cleanly on a server.

```javascript
import { SocketEvents } from "shared";

export class PlayerSessionService {
    constructor(io) {
        this.io = io;
        this.playerSessions = {};
        this.initializeSocketEvents();
    }

    initializeSocketEvents() {
        this.io.on(SocketEvents.Server.CONNECTION, (socket) => {
            socket.on(SocketEvents.Client.REGISTER_PLAYER, (playerId) => {
                this.registerPlayer(socket, playerId);
            });

            socket.on(SocketEvents.Client.DISCONNECT, () => {
                this.handleDisconnect(socket);
            });
        });
    }

    registerPlayer(socket, playerId) {
        console.log(
            `Connected with playerID: ${playerId}, socketID: ${socket.id}`
        );

        this.playerSessions[playerId] = socket.id;
    }

    handleDisconnect(socket) {
        const playerId = Object.keys(this.playerSessions).find(
            (id) => this.playerSessions[id] === socket.id
        );

        if (playerId) {
            delete this.playerSessions[playerId];
            console.log(
                `Disconnected with playerID: ${playerId}, socketID ${socket.id}`
            );
        }
    }
}

// Usage:
const io = require("socket.io")(server);
const playerSessionService = new PlayerSessionService(io);
```

#### Explanation:

1. **Encapsulation:** The code is now encapsulated in the `PlayerSessionService` class. This makes it modular and reusable.
2. **Methods:** The logic for handling connections, player registration, and disconnections is split into dedicated methods: `registerPlayer` and `handleDisconnect`.
3. **State Management:** The `playerSessions` object is now a class property, allowing you to keep track of player sessions.
4. **Usage:** The class is initialized with a `socket.io` instance (`io`), making it flexible to integrate into any server setup.

To use the class:

-   Pass your `io` instance to `PlayerSessionService` when initializing it.
-   The class handles all socket events, simplifying your server's main logic.

#### Logging

Keeping logging concise, consistent and state oriented.

Logging on server:  
Server running on http://localhost:3000  
Connected with playerID: player-v9f8li036, socketID: kQxbedd4v7m81WH1AAAB  
Disconnected with playerID: player-v9f8li036, socketID kQxbedd4v7m81WH1AAAB  
Connected with playerID: player-v9f8li036, socketID: Px-Ne0iAPpV4qq4LAAAD

Server running on http://localhost:3000  
Connected with playerID: player-v9f8li036, socketID: 1tRQrYtSkc2SkoNmAAAB

Logging on client:  
Connected with player ID: player-v9f8li036  
Disconnected. Trying to reconnect...

Connected with player ID: player-v9f8li036

[Content](#content)  
[Back](index.md)
