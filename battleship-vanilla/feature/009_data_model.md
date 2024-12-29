[Back](index.md)

# **Data Model**

## **Data Model Requirements**

### **Feature Description**

1. **Purpose**:

    - Create a data model to manage players and their associated sockets.
    - Ensure it is usable both on the server and client.

2. **Properties**:

    - **playerID**: A string representing the unique identifier of a player.
    - **socketID**: A string representing the unique identifier of a player's socket.

3. **Language**:

    - Implement the model using **JavaScript**.

4. **Encapsulation**:

    - Provide a **service** layer to encapsulate the data model.
    - The service should include **methods** to control and manipulate the model's state effectively.

5. **Shared Usage**:
    - Ensure the data model is compatible for use on both the **server** and **client** sides.

## Initial Implementation

Here's an initial implementation of the data model in JavaScript, with an encapsulated service layer that provides methods for managing the state. This is designed to be usable both on the server and the client.

```javascript
// PlayerDataModel.js

class PlayerDataModel {
    constructor(playerID, socketID) {
        this.playerID = playerID;
        this.socketID = socketID;
    }
}

class PlayerService {
    constructor() {
        this.players = new Map(); // Store players using playerID as the key
    }

    // Add a new player
    addPlayer(playerID, socketID) {
        if (this.players.has(playerID)) {
            throw new Error(`Player with ID ${playerID} already exists.`);
        }
        const player = new PlayerDataModel(playerID, socketID);
        this.players.set(playerID, player);
    }

    // Get a player by playerID
    getPlayer(playerID) {
        return this.players.get(playerID) || null;
    }

    // Update a player's socketID
    updateSocketID(playerID, newSocketID) {
        const player = this.players.get(playerID);
        if (!player) {
            throw new Error(`Player with ID ${playerID} not found.`);
        }
        player.socketID = newSocketID;
    }

    // Remove a player by playerID
    removePlayer(playerID) {
        if (!this.players.has(playerID)) {
            throw new Error(`Player with ID ${playerID} not found.`);
        }
        this.players.delete(playerID);
    }

    // List all players
    listPlayers() {
        return Array.from(this.players.values());
    }
}

// Example usage (works on both server and client):
// const playerService = new PlayerService();
// playerService.addPlayer("player1", "socket123");
// console.log(playerService.getPlayer("player1"));
// playerService.updateSocketID("player1", "socket456");
// playerService.removePlayer("player1");

export { PlayerDataModel, PlayerService };
```

### **Key Features**:

1. **Encapsulation**:

    - The `PlayerService` class manages all operations related to players.
    - The `PlayerDataModel` class is the structure for individual player instances.

2. **State Management**:

    - Players are stored in a `Map` for fast access using `playerID` as the key.

3. **Shared Usage**:
    - Designed to be modular and easily imported on both the client and server using ES6 imports (`import { PlayerService } from './PlayerDataModel';`).

## ccc

[Back](index.md)
