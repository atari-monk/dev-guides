[Back](index.md)

# Monorepo

This steps allow to setup monorepo with client and server and test it to confirm it is working.

## Content

1. [Monorepo](#monorepo-1)
2. [Server](#server)
3. [Cors](#cors)
4. [Client](#client)

## Monorepo

A **monorepo** centralizes dependencies at the root level, avoiding redundant `node_modules` folders in individual packages or services.

### Instructions

1. **Setup repository**:

    You can use guide [Start repository](../../../note/start_repo.md).

2. **Create a root `package.json`**:

    ```bash
    npm init -y
    ```

3. **Enable npm workspaces** by adding this to `package.json`:

    ```json
    {
        "name": "repo-name-monorepo",
        "private": true,
        "workspaces": []
    }
    ```

4. **Install all dependencies from the root**:

    install packages

    ```bash
    npm i package package_2 package_3
    ```

    or state dependencies in package.json and run

    ```bash
    npm i
    ```

### Key Points

-   **Structure**: Centralized `node_modules` at the root.
-   **Dependencies**: Shared across all services (`server`, etc.).
-   **npm Workspaces**: Automates linking between packages.

[Content](#content)

## Server

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
    mkdir src
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
            "start": "node src/index.js"
        }
    }
    ```

4. **Create the server code** (`index.js`):

    ```bash
    cd src
    ni index.js
    ```

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

[Content](#content)

## Cors

Cors is a kind of routing filter that needs to be defined to connect client and server.

### Express

To set cors on express js server:

```bash
npm i cors
```

```javascript
const cors = require("cors");

app.use(cors());
```

[Content](#content)

## Client

### Step 1: Adding More Services (Optional)

If you want to add more services (e.g., a client or API), you can simply create additional directories in the root and add them to the `workspaces` configuration.

For example:

1. **Create a new `client/` directory** for a frontend service or another package.

    ```bash
    mkdir client
    cd client
    npm init -y
    ```

2. **Modify the root `package.json` to include `client` in the workspaces**:

    Update the `workspaces` array in the root `package.json`:

    ```json
    {
        "name": "my-monorepo",
        "private": true,
        "workspaces": ["server", "client"]
    }
    ```

3. **Install client dependencies** in the root (e.g., React, Vue, etc.):

    ```bash
    npm install react react-dom
    ```

4. **Here’s a basic structure for a vanilla web project:**

    ```plaintext
    /my-project
    ├── index.html       (Main entry HTML file)
    ├── /assets          (Folder for images, fonts, etc.)
    ├── /css             (Folder for stylesheets)
    ├── /js              (Folder for JavaScript files)
    └── /lib             (Optional folder for libraries)
    ```

5. **Add code to the `client` service** (you can set up a React app, for example).

    ```html
    <!DOCTYPE html>
    <html lang="en">
        <head>
            <meta charset="UTF-8" />
            <meta
                name="viewport"
                content="width=device-width, initial-scale=1.0"
            />
            <title>Server Message</title>
            <script>
                async function fetchServerMessage() {
                    try {
                        const response = await fetch("http://localhost:3000");
                        const message = await response.text();
                        document.getElementById("serverMessage").innerText =
                            message;
                    } catch (error) {
                        document.getElementById("serverMessage").innerText =
                            "Failed to connect to server.";
                        console.error("Error fetching server message:", error);
                    }
                }

                window.onload = fetchServerMessage;
            </script>
        </head>
        <body>
            <h1>Server Message</h1>
            <p id="serverMessage">Loading...</p>
        </body>
    </html>
    ```

[Content](#content)  
[Back](index.md)
