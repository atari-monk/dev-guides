[Back](index.md)

# **Data Service**

## Content

1. [Requirements](#requirements)
2. [Implementation](#implementation)
3. [Refactored](#refactored)
4. [Extention](#extention)

## Requirements

**Data Service Requirements**

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

## Implementation

Initial Implementation

Here's an initial implementation of the data model in JavaScript, with an encapsulated service layer that provides methods for managing the state. This is designed to be usable both on the server and the client.

```javascript
// DataModel.js

export class DataModel {
    constructor(playerID, socketID) {
        this.playerID = playerID;
        this.socketID = socketID;
    }
}
```

```javascript
// DataService.js

export class DataService {
    constructor() {
        this.players = new Map();
    }

    addPlayer(playerID, socketID) {
        if (this.players.has(playerID)) {
            throw new Error(`Player with ID ${playerID} already exists.`);
        }
        const player = new PlayerDataModel(playerID, socketID);
        this.players.set(playerID, player);
    }

    getPlayer(playerID) {
        return this.players.get(playerID) || null;
    }

    updateSocketID(playerID, newSocketID) {
        const player = this.players.get(playerID);
        if (!player) {
            throw new Error(`Player with ID ${playerID} not found.`);
        }
        player.socketID = newSocketID;
    }

    removePlayer(playerID) {
        if (!this.players.has(playerID)) {
            throw new Error(`Player with ID ${playerID} not found.`);
        }
        this.players.delete(playerID);
    }

    listPlayers() {
        return Array.from(this.players.values());
    }
}
```

```javascript
// Example usage (works on both server and client):
const dataService = new DataService();
dataService.addPlayer("player1", "socket123");
console.log(dataService.getPlayer("player1"));
dataService.updateSocketID("player1", "socket456");
dataService.removePlayer("player1");
```

### **Key Features**:

1. **Encapsulation**:

    - The `PlayerService` class manages all operations related to players.
    - The `PlayerDataModel` class is the structure for individual player instances.

2. **State Management**:

    - Players are stored in a `Map` for fast access using `playerID` as the key.

3. **Shared Usage**:
    - Designed to be modular and easily imported on both the client and server using ES6 imports (`import { PlayerService } from './PlayerDataModel';`).

## Refactored

Refactored Implementation with DI

#### **DataService Interface**

We’ll define a generic contract for the data service:

```javascript
// IDataService.js
export class IDataService {
    addPlayer(playerID, socketID) {
        throw new Error("Method 'addPlayer()' must be implemented.");
    }

    getPlayer(playerID) {
        throw new Error("Method 'getPlayer()' must be implemented.");
    }

    getPlayerIdBySocket(socketID) {
        throw new Error("Method 'getPlayerIdBySocket()' must be implemented.");
    }

    updateSocketID(playerID, newSocketID) {
        throw new Error("Method 'updateSocketID()' must be implemented.");
    }

    removePlayer(playerID) {
        throw new Error("Method 'removePlayer()' must be implemented.");
    }

    listPlayers() {
        throw new Error("Method 'listPlayers()' must be implemented.");
    }
}
```

---

#### **Concrete DataService Implementation**

```javascript
import { IDataService } from "./IDataService";

export class DataService extends IDataService {
    constructor() {
        super();
        //...
```

[See integration with connect feature](008_connect.md#integration)

## Extention

Extention for grid feature.

### Extended data model.

```javascript
import { GridModel } from "./GridModel.js";

export class DataModel {
    constructor(playerID, socketID, gridSize = 10) {
        this.playerID = playerID;
        this.socketID = socketID;
        this.grid = new GridModel(gridSize);
    }
}
```

### Extended data service.

```javascript
import { IDataService } from "./IDataService.js";
import { DataModel } from "./DataModel.js";

export class DataService extends IDataService {
    constructor() {
        super();
        this.players = new Map();
    }

    addPlayer(playerID, socketID) {
        if (this.players.has(playerID)) {
            throw new Error(`Player with ID ${playerID} already exists.`);
        }
        const player = new DataModel(playerID, socketID);
        this.players.set(playerID, player);
    }

    getPlayer(playerID) {
        return this.players.get(playerID) || null;
    }

    getPlayerIdBySocket(socketID) {
        const players = this.listPlayers();
        const player = players.find((p) => p.socketID === socketID);
        return player ? player.playerID : null;
    }

    updateSocketID(playerID, newSocketID) {
        const player = this.players.get(playerID);
        if (!player) {
            throw new Error(`Player with ID ${playerID} not found.`);
        }
        player.socketID = newSocketID;
    }

    removePlayer(playerID) {
        if (!this.players.has(playerID)) {
            throw new Error(`Player with ID ${playerID} not found.`);
        }
        this.players.delete(playerID);
    }

    listPlayers() {
        return Array.from(this.players.values());
    }

    getPlayerGrid(playerID) {
        const player = this.players.get(playerID);
        if (!player) {
            throw new Error(`Player with ID ${playerID} not found.`);
        }
        return player.grid.getGridState();
    }

    isPlacementValid(playerID, startX, startY, length, direction) {
        const player = this.players.get(playerID);
        if (!player) {
            throw new Error(`Player with ID ${playerID} not found.`);
        }

        return player.grid.isPlacementValid(startX, startY, length, direction);
    }

    placeShip(playerID, startX, startY, length, direction) {
        const player = this.players.get(playerID);
        if (!player) {
            throw new Error(`Player with ID ${playerID} not found.`);
        }
        player.grid.placeShip(startX, startY, length, direction);
    }

    attackPlayer(playerID, x, y) {
        const player = this.players.get(playerID);
        if (!player) {
            throw new Error(`Player with ID ${playerID} not found.`);
        }
        return player.grid.attack(x, y);
    }
}
```

##

[Content](#content)  
[Back](index.md)
