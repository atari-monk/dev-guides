[Back](index.md)

# Server

### Step 1: Set Up the Server

1. **Add server workspace to root `package.json`**

    ```json
    {
        "name": "repo-name-monorepo",
        "private": true,
        "workspaces": ["server"]
    }
    ```

2. **Create the `server` directory**:

    ```bash
    mkdir server
    cd server
    ```

3. **Create a `package.json` in the `server` directory** (this can be a minimal file, as the root `package.json` will manage dependencies):

    ```bash
    npm init -y
    ```

    This `package.json` in `server/` will only contain the specific metadata for the service, like its name and scripts, and won't contain dependencies directly.

    Example `server/package.json`:

    ```json
    {
        "name": "server",
        "main": "index.js",
        "scripts": {
            "start": "node index.js"
        }
    }
    ```

4. **Create the server code** (`index.js`):

    Inside `server/index.js`, add a simple Node.js server code:

    ```javascript
    // server/index.js
    const express = require("express");
    const app = express();
    const port = 3000;

    app.get("/", (req, res) => {
        res.send("Hello from Node.js server!");
    });

    app.listen(port, () => {
        console.log(`Server is running at http://localhost:${port}`);
    });
    ```

### Step 2: Install Shared Dependencies at the Root Level

1. **Install the shared dependencies (e.g., `express`) from the root of the monorepo**:

    From the root of your monorepo (where the `package.json` file with `workspaces` is located), run:

    ```bash
    npm install express
    ```

    This will install `express` in the root `node_modules` and will be available to all services in the `workspaces` (including the `server` folder).

2. **Verify that `express` is available**: The `express` package will be installed in the root `node_modules`, and any service in the workspace (like `server`) will be able to use it without needing a local `node_modules` inside the `server/` folder.

### Step 3: Running the Server

1. **Run the server**:

    You can now run the server from the root directory using the `npm run start` command, which is defined in the `server/package.json`.

    ```bash
    npm run start --workspace server
    ```

    Or, navigate to the `server/` directory and run the following:

    ```bash
    cd server
    npm start
    ```

    Either method will start the server. You should see the message:

    ```
    Server is running at http://localhost:3000
    ```

[Back](index.md)
