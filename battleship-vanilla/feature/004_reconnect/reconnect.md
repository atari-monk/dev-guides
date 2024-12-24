[Back](index.md)

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

[Back](index.md)
