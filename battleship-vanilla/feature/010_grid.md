[Back](index.md)

# Grid

Grid for battleship game.  
Specifically stuff that enables placing fleet.  
By that i mean visualization/user interface, collecting data.

## Content

1. [Data model](#data-model)
2. [Grid Component](#grid-component)
3. [Bugs](#bugs)

## Data model

Grid data model for battleship game.

```javascript
export class GridModel {
    constructor(size = 10) {
        this.size = size;
        this.cells = Array(size)
            .fill(null)
            .map(() =>
                Array(size)
                    .fill(null)
                    .map(() => ({ status: "empty" }))
            );
    }

    isPlacementValid(startX, startY, length, direction) {
        for (let i = 0; i < length; i++) {
            const x = startX + (direction === "horizontal" ? i : 0);
            const y = startY + (direction === "vertical" ? i : 0);

            if (x >= this.size || y >= this.size) {
                return false;
            }

            if (this.cells[x][y].status !== "empty") {
                return false;
            }
        }
        return true;
    }

    placeShip(startX, startY, length, direction) {
        if (direction !== "horizontal" && direction !== "vertical") {
            throw new Error(
                "Invalid direction. Use 'horizontal' or 'vertical'."
            );
        }

        if (direction === "horizontal" && startX + length > this.size) {
            throw new Error(
                "Ship cannot be placed horizontally, out of bounds."
            );
        }
        if (direction === "vertical" && startY + length > this.size) {
            throw new Error("Ship cannot be placed vertically, out of bounds.");
        }

        for (let i = 0; i < length; i++) {
            const x = startX + (direction === "horizontal" ? i : 0);
            const y = startY + (direction === "vertical" ? i : 0);

            this.cells[x][y].status = "ship";
        }
    }

    attack(x, y) {
        if (x >= this.size || y >= this.size) {
            throw new Error("Attack out of bounds.");
        }

        const cell = this.cells[x][y];
        if (cell.status === "hit" || cell.status === "miss") {
            throw new Error("Cell already attacked.");
        }

        if (cell.status === "ship") {
            cell.status = "hit";
            return "hit";
        } else {
            cell.status = "miss";
            return "miss";
        }
    }

    getGridState() {
        return this.cells.map((row) => row.map((cell) => cell.status));
    }
}
```

Data service needs to be expanded to include this model.

[Extention](009_data_service.md#extention)

## Grid Component

Enables placing of the fleet.

```javascript
export class GridComponent {
    constructor(dataService, playerID, fleet) {
        this.dataService = dataService;
        this.playerID = playerID;
        this.fleet = fleet;
        this.currentShipIndex = 0;
        this.placementDirection = "horizontal";
        this.container = null;
    }

    init(container) {
        this.container = container;
        container.innerHTML = "";
        this.renderGrid();
        this.renderControls();
    }

    renderGrid() {
        const gridState = this.dataService.getPlayerGrid(this.playerID);

        this.container.innerHTML = "";

        const gridElement = document.createElement("div");
        gridElement.classList.add("battleship-grid");
        gridElement.style.display = "grid";
        gridElement.style.gridTemplateColumns = `repeat(${gridState.length}, 30px)`;
        gridElement.style.gap = "0px";

        gridState.forEach((row, x) => {
            row.forEach((cellStatus, y) => {
                const cell = document.createElement("div");
                cell.classList.add("grid-cell");
                cell.dataset.x = x;
                cell.dataset.y = y;
                cell.style.width = "30px";
                cell.style.height = "30px";
                cell.style.border = "1px solid #ccc";
                cell.style.backgroundColor = this.getCellColor(cellStatus);

                cell.addEventListener("mouseenter", () =>
                    this.previewPlacement(x, y)
                );
                cell.addEventListener("mouseleave", () =>
                    this.clearPreview(x, y)
                );
                cell.addEventListener("click", () => this.placeShip(x, y));

                gridElement.appendChild(cell);
            });
        });

        this.container.appendChild(gridElement);
    }

    renderControls() {
        const controlsElement = document.createElement("div");
        controlsElement.classList.add("battleship-controls");

        const directionButton = document.createElement("button");
        directionButton.textContent = `Direction: ${this.placementDirection}`;
        directionButton.addEventListener("click", () => {
            this.placementDirection =
                this.placementDirection === "horizontal"
                    ? "vertical"
                    : "horizontal";
            directionButton.textContent = `Direction: ${this.placementDirection}`;
        });

        const infoElement = document.createElement("p");
        infoElement.textContent = `Place ship of size: ${
            this.fleet[this.currentShipIndex]
        }`;

        controlsElement.appendChild(directionButton);
        controlsElement.appendChild(infoElement);

        this.container.appendChild(controlsElement);
    }

    getCellColor(cellStatus) {
        switch (cellStatus) {
            case "ship":
                return "blue";
            case "hit":
                return "red";
            case "miss":
                return "gray";
            default:
                return "white";
        }
    }

    previewPlacement(startX, startY) {
        const shipSize = this.fleet[this.currentShipIndex];
        const gridSize = this.dataService.getPlayerGrid(this.playerID).length;
        const valid = this.dataService.isPlacementValid(
            this.playerID,
            startX,
            startY,
            shipSize,
            this.placementDirection
        );

        for (let i = 0; i < shipSize; i++) {
            const x =
                startX + (this.placementDirection === "horizontal" ? i : 0);
            const y = startY + (this.placementDirection === "vertical" ? i : 0);

            if (x >= gridSize || y >= gridSize) {
                break;
            }

            const cell = this.getCellElement(x, y);
            if (cell) {
                const cellStatus = this.dataService.getPlayerGrid(
                    this.playerID
                )[x][y];
                if (cellStatus === "ship") {
                    cell.style.backgroundColor = "blue";
                } else {
                    cell.style.backgroundColor = valid ? "green" : "red";
                }
            }
        }
    }

    clearPreview(startX, startY) {
        const shipSize = this.fleet[this.currentShipIndex];
        const gridSize = this.dataService.getPlayerGrid(this.playerID).length;

        for (let i = 0; i < shipSize; i++) {
            const x =
                startX + (this.placementDirection === "horizontal" ? i : 0);
            const y = startY + (this.placementDirection === "vertical" ? i : 0);

            if (x >= gridSize || y >= gridSize) {
                break;
            }

            const cell = this.getCellElement(x, y);
            if (cell) {
                const cellStatus = this.dataService.getPlayerGrid(
                    this.playerID
                )[x][y];
                cell.style.backgroundColor = this.getCellColor(cellStatus);
            }
        }
    }

    placeShip(startX, startY) {
        const shipSize = this.fleet[this.currentShipIndex];
        const valid = this.dataService.isPlacementValid(
            this.playerID,
            startX,
            startY,
            shipSize,
            this.placementDirection
        );

        if (valid) {
            this.dataService.placeShip(
                this.playerID,
                startX,
                startY,
                shipSize,
                this.placementDirection
            );

            this.currentShipIndex++;
            if (this.currentShipIndex < this.fleet.length) {
                this.renderGrid();
                this.renderControls();
            } else {
                alert("All ships placed!");
            }
        } else {
            alert("Invalid placement!");
        }
    }

    getCellElement(x, y) {
        return this.container.querySelector(
            `.grid-cell[data-x="${x}"][data-y="${y}"]`
        );
    }
}
```

Export style form client_lib/src/index.js.

```javascript
export * from "./grid/GridComponent.js";
```

Syling.

```css
.battleship-grid {
    margin: 20px 0;
}

.grid-cell {
    display: inline-block;
}

.battleship-controls button {
    margin: 10px 0;
}
```

Container in index.html

```html
<div id="battleship-container"></div>
```

Usage in client app.

```javascript
const playerId = new IdService().getId();
//...
const dataService = new DataService();
dataService.addPlayer(playerId, socket.id);
const gridComponent = new GridComponent(dataService, playerId, [5, 4, 3, 3, 2]);
gridComponent.init(document.getElementById("battleship-container"));
```

## Bugs

Raw chatgpt generation had some bugs.  
There was a bug with not cleaning before rendering.  
Also ship was marking entire row of grid.  
After debuging it turned out that 0 to 4 element in array should be ship, but entire array from 0 to 9 was set.  
Chatgpt detected bug and fixed.

The issue you're encountering, where placing a ship at `(0, 0)` also fills the entire row `(0, 0)` to `(0, 9)`, likely stems from how you're initializing the grid cells.

### The Problem

In the constructor of your `GridModel` class, you're initializing the grid with the following code:

```js
this.cells = Array(size)
    .fill(null)
    .map(() => Array(size).fill({ status: "empty" }));
```

Here, you are using `.fill()` to populate the grid. However, when you use `.fill({ status: 'empty' })`, it doesn't create a new object for each cell. Instead, it fills all the cells in the row with references to the **same object**. As a result, when you modify one of the cells (like `this.cells[0][0]`), you're inadvertently modifying **all** the cells in that row (because they all point to the same object in memory).

### Solution

To fix this, you need to ensure that each cell is an independent object. You can do this by using a function to create a new object for each cell instead of using `.fill()` directly with an object.

### Updated Constructor

```js
constructor(size = 10) {
  this.size = size;
  // Initialize the grid with each cell having its own 'empty' status object
  this.cells = Array(size)
    .fill(null)
    .map(() => Array(size).fill(null).map(() => ({ status: 'empty' })));
}
```

### Explanation:

1. **`Array(size).fill(null)`** creates an array of `size` elements, each being `null` initially.
2. **`.map(() => Array(size).fill(null).map(() => ({ status: 'empty' })))`** ensures that for each row, you create a new array with individual objects `{ status: 'empty' }`. This ensures that each cell in the grid has its own independent object.

This way, when you update one cell (e.g., `this.cells[0][0].status = 'ship'`), it will not affect the other cells in the row or grid.

### What changed:

-   **Grid initialization**: We now use `.map()` to create a new object for each cell, ensuring that each cell is independent. This prevents the issue where modifying one cell inadvertently modifies all cells in the row.

Now, when you place a ship, it should only modify the intended cells, and the issue of affecting the entire row will be resolved.

### Test:

Now when you call `placeShip(0, 0, 5, 'horizontal')`, only the cells `(0, 0)` to `(0, 4)` will be updated, and no other cells will be affected.

##

[Content](#content)  
[Back](index.md)
