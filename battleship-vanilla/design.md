[Content](index.md)
[Main](../index.md)

# AI generated quick start.

To create a simple version of a Battleship server-client using Socket.IO, I'll guide you through the basic structure you need. You'll need two main events: one for placing fleets (`fleetPlaced`) and one for attacking (`attack`). Here's a high-level approach to how you can implement this in the simplest way possible:

### Basic Structure:

1. **Server**: Handle game state (e.g., waiting for fleet placement, attacking, game over).
2. **Client**: Send events to place fleets, attack, and handle server responses.
3. **Game States**: Handle game state like waiting for fleet placement, attacking, and checking if the game is over.

### Step-by-Step Implementation:

#### 1. **Set up your server** (Node.js with `socket.io`):

```javascript
// server.js
const express = require("express");
const http = require("http");
const socketIo = require("socket.io");

const app = express();
const server = http.createServer(app);
const io = socketIo(server);

const PORT = 3000;

let players = [];
let gameState = {
    player1: { fleet: [], hits: [] },
    player2: { fleet: [], hits: [] },
    turn: "player1",
    gameOver: false,
};

function checkGameOver() {
    const { player1, player2 } = gameState;
    if (player1.fleet.length === 0 || player2.fleet.length === 0) {
        gameState.gameOver = true;
        io.emit("gameOver", "Game Over!");
    }
}

io.on("connection", (socket) => {
    console.log("A player connected");

    if (players.length < 2) {
        players.push(socket);
        socket.emit("waitingForFleet", "Place your fleet!");
    } else {
        socket.emit("gameFull", "Game is full. Try again later.");
    }

    // Event when fleet is placed
    socket.on("fleetPlaced", (fleetData) => {
        const player = players[0] === socket ? "player1" : "player2";
        gameState[player].fleet = fleetData;

        // If both players have placed fleets, start the game
        if (gameState.player1.fleet.length && gameState.player2.fleet.length) {
            io.emit(
                "startGame",
                "Both players have placed fleets. Let the battle begin!"
            );
        }
    });

    // Event for attacking a cell
    socket.on("attack", (cell) => {
        if (gameState.gameOver) return;

        const attackingPlayer = players[0] === socket ? "player1" : "player2";
        const defendingPlayer =
            attackingPlayer === "player1" ? "player2" : "player1";

        const target = gameState[defendingPlayer].fleet.find(
            (ship) => ship.cell === cell
        );

        if (target) {
            gameState[defendingPlayer].fleet = gameState[
                defendingPlayer
            ].fleet.filter((ship) => ship.cell !== cell);
            socket.emit("hit", `Hit on ${cell}!`);
            checkGameOver();
        } else {
            socket.emit("miss", `Miss on ${cell}!`);
        }

        // Switch turn
        gameState.turn = attackingPlayer === "player1" ? "player2" : "player1";
        io.emit("turn", gameState.turn);
    });

    // Event when a player disconnects
    socket.on("disconnect", () => {
        console.log("A player disconnected");
        players = players.filter((player) => player !== socket);
    });
});

server.listen(PORT, () => {
    console.log(`Server running on http://localhost:${PORT}`);
});
```

#### 2. **Set up the client** (HTML/JavaScript with `socket.io-client`):

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>Simple Battleship</title>
        <script src="https://cdn.socket.io/4.4.0/socket.io.min.js"></script>
        <style>
            /* Style for grid, buttons, etc. */
        </style>
    </head>
    <body>
        <h1>Simple Battleship Game</h1>
        <div id="message"></div>
        <div id="grid"></div>
        <button id="startGameBtn" style="display:none;">Start Game</button>

        <script>
            const socket = io("http://localhost:3000");

            let fleet = [];
            let currentTurn = "";

            function createGrid() {
                const grid = document.getElementById("grid");
                grid.innerHTML = ""; // Clear the grid
                for (let i = 0; i < 10; i++) {
                    const row = document.createElement("div");
                    row.classList.add("row");
                    for (let j = 0; j < 10; j++) {
                        const cell = document.createElement("button");
                        cell.innerText = `${i},${j}`;
                        cell.onclick = () => attackCell(i, j);
                        row.appendChild(cell);
                    }
                    grid.appendChild(row);
                }
            }

            function attackCell(row, col) {
                if (currentTurn !== "player1") {
                    socket.emit("attack", { row, col });
                }
            }

            socket.on("waitingForFleet", (message) => {
                document.getElementById("message").innerText = message;
                createGrid();
            });

            socket.on("startGame", (message) => {
                document.getElementById("message").innerText = message;
            });

            socket.on("gameFull", (message) => {
                document.getElementById("message").innerText = message;
            });

            socket.on("hit", (message) => {
                alert(message);
            });

            socket.on("miss", (message) => {
                alert(message);
            });

            socket.on("turn", (turn) => {
                currentTurn = turn;
                document.getElementById(
                    "message"
                ).innerText = `It's ${turn}'s turn!`;
            });

            socket.on("gameOver", (message) => {
                alert(message);
                setTimeout(() => {
                    location.reload();
                }, 3000);
            });

            function placeFleet() {
                // This would be some kind of user input for placing the fleet
                // Example: `fleet = [{cell: '1,1'}, {cell: '1,2'}]`
                socket.emit("fleetPlaced", fleet);
            }
        </script>
    </body>
</html>
```

### Explanation of Events:

1. **fleetPlaced**: This event is triggered when the user has placed their fleet. The fleet data is sent to the server, and the game waits until both players have placed their fleets.
2. **attack**: This event is triggered when a player attacks a cell on the grid. The server checks whether it's a hit or miss and responds accordingly.

3. **gameOver**: When one player sinks all the ships of the other player, the server emits a `gameOver` event, and the game restarts after 3 seconds.

4. **turn**: Each time a player attacks, the server emits the turn event to indicate whose turn it is to play.

### Improvements:

-   **Fleet Placement**: The client code is designed to let players place fleets. You can enhance this with more complex grid logic and visual representation of ships.
-   **Game State Management**: The game should ensure that players can only attack in their turn and handle edge cases (e.g., invalid moves).
-   **UI**: This is a very simple UI. You can improve it by visually showing ships, hits, misses, and displaying whose turn it is.

This setup gives you the simplest structure for a two-player Battleship game over Socket.IO. You can extend it with more complex features, but this provides the basic game loop and communication flow.

[Content](index.md)
[Main](../index.md)
