[Back](index.md)

# Client

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

4. **Add code to the `client` service** (you can set up a React app, for example).

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
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

[Back](index.md)
