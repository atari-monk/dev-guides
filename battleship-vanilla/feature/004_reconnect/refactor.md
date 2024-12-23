[Back](index.md)

# Refactor

## Client PlayerIdService

Here's the encapsulated version of your code in a class:

```javascript
class PlayerIdService {
    constructor() {
        this.playerIdKey = "playerId";
        this.playerId = this.getOrCreatePlayerId();
    }

    // Retrieves the player ID or generates a new one if it doesn't exist
    getOrCreatePlayerId() {
        let playerId = localStorage.getItem(this.playerIdKey);
        if (!playerId) {
            playerId = this.generateUniqueId();
            localStorage.setItem(this.playerIdKey, playerId);
        }
        return playerId;
    }

    // Generates a simple unique ID
    generateUniqueId() {
        return "player-" + Math.random().toString(36).substr(2, 9);
    }

    // Gets the player ID
    getPlayerId() {
        return this.playerId;
    }
}

// Usage example
const playerIdService = new PlayerIdService();
console.log(playerIdService.getPlayerId());
```

This class encapsulates the functionality into a reusable tool that manages the retrieval or creation of a unique player ID.

## Client SocketService

Here's how you can encapsulate the reconnect logic into a class:

```javascript
class SocketService {
    constructor(socket, playerId) {
        this.socket = socket;
        this.playerId = playerId;

        this.setupListeners();
    }

    setupListeners() {
        // Handle initial connection and registration
        this.socket.on("connect", () => {
            this.registerPlayer();
        });

        // Handle reconnections
        this.socket.on("reconnect", () => {
            console.log(`Reconnected with player ID: ${this.playerId}`);
            this.registerPlayer();
        });

        // Handle disconnections
        this.socket.on("disconnect", () => {
            console.log("Disconnected. Trying to reconnect...");
        });
    }

    registerPlayer() {
        this.socket.emit("registerPlayer", this.playerId);
    }
}

// Usage
// Assuming `io` is the Socket.IO client library and `playerId` is defined
const socket = io(); // Initialize the Socket.IO client
const playerId = "examplePlayerId";
const socketService = new SocketService(socket, playerId);
```

### Explanation:

1. **Encapsulation:** The `SocketService` class encapsulates all socket-related logic.
2. **Reusability:** You can reuse the class for different sockets and player IDs.
3. **Maintainability:** The class isolates the connection logic, making the code easier to manage and extend.
4. **Method Extraction:** The `registerPlayer` method is extracted for clarity and reusability.

## Server PlayerSessionService

Here's how you can encapsulate your code into a class to manage player sessions and socket connections more cleanly on a server.

```javascript
class PlayerSessionService {
    constructor(io) {
        this.io = io; // Reference to the socket.io instance
        this.playerSessions = {}; // Store player sessions
        this.initializeSocketEvents();
    }

    initializeSocketEvents() {
        this.io.on("connection", (socket) => {
            console.log("New connection: " + socket.id);

            // Listen for the player registration event
            socket.on("registerPlayer", (playerId) => {
                this.registerPlayer(socket, playerId);
            });

            // Handle disconnections
            socket.on("disconnect", () => {
                this.handleDisconnect(socket);
            });
        });
    }

    registerPlayer(socket, playerId) {
        console.log("Player connected with ID: " + playerId);

        // Store the player session based on player ID
        this.playerSessions[playerId] = socket.id;

        // Send the player a welcome message or reconnection confirmation
        socket.emit("welcome", `Welcome back, player ${playerId}`);
    }

    handleDisconnect(socket) {
        console.log("Player disconnected: " + socket.id);

        // Optionally remove the player from sessions
        const playerId = Object.keys(this.playerSessions).find(
            (id) => this.playerSessions[id] === socket.id
        );

        if (playerId) {
            delete this.playerSessions[playerId];
            console.log(`Player session removed for ID: ${playerId}`);
        }
    }
}

// Example usage:
// const io = require('socket.io')(server); // Pass your server instance here
// const playerSessionService = new PlayerSessionService(io);
```

### Explanation:

1. **Encapsulation:** The code is now encapsulated in the `PlayerSessionService` class. This makes it modular and reusable.
2. **Methods:** The logic for handling connections, player registration, and disconnections is split into dedicated methods: `registerPlayer` and `handleDisconnect`.
3. **State Management:** The `playerSessions` object is now a class property, allowing you to keep track of player sessions.
4. **Usage:** The class is initialized with a `socket.io` instance (`io`), making it flexible to integrate into any server setup.

To use the class:

-   Pass your `io` instance to `PlayerSessionService` when initializing it.
-   The class handles all socket events, simplifying your server's main logic.

[Back](index.md)
