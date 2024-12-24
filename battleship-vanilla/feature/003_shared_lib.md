[Back](index.md)

To introduce a shared library in a monorepo setup like this, where the library will be shared between the server and client projects, you can follow these steps:

### 1. **Create the Shared Library Folder**

Create a new folder for the shared library in your monorepo structure:

```
battleship-vanilla-monorepo/
├── server/
├── client/
├── shared/
├── package.json
```

### 2. **Initialize the Shared Library**

Navigate to the `shared` directory and initialize a new Node.js package:

```bash
cd shared
npm init -y
```

This will create a `package.json` file inside the `shared` folder.

```json
{
  "name": "shared",
  "version": "1.0.0",
  "type": "module",
  "main": "src/index.js",
  "scripts": {},
  "keywords": [],
  "author": "",
  "description": ""
}
```

### 3. **Implement Your Shared Code**

Add your shared logic or utilities inside the `shared` folder. For example:

```bash
// shared/src/utils.js
export function getRandomNumber(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}
```

```bash
// shared/src/index.js
export * from './utils.js'
```

### 4. **Reference the Shared Library in Your Monorepo**

Update the main `package.json` to include the `shared` library as part of your workspaces:

```json
{
    "workspaces": ["server", "client", "shared"],
}
```

### 5. **Link the Shared Library to Server and Client**

Both the `server` and `client` should declare a dependency on the `shared` library. For example:

#### Server `package.json`:

```json
{
	"main": "src/index.js",
	"type": "module",
    "dependencies": {
        "shared": "*"
    }
}
```

#### Client `package.json`:

```json
{
	"main": "src/index.js",
    "dependencies": {
        "shared": "*"
    }
}
```

### 6. **Install Dependencies**

Run the following command in the root of your monorepo to install dependencies and link the `shared` library:

```bash
npm install
```

### 7. **Import the Shared Library in Server**

You can now import the shared library in your server and client code.

#### Server Example:

Use import/export style imports on server.

```javascript
import express from 'express'
import http from 'http'
import { Server } from 'socket.io'
import cors from 'cors'
import { getRandomNumber } from 'shared'

console.log(getRandomNumber(1, 10))

const io = new Server(server, { cors: { origin: '*' } })
```

### 8. **Import the Shared Library in Client**

Since the client uses vanilla JS, you may need to ensure the shared files are compatible with browser environments. If you use ES Modules (`import/export`), browsers require the `.js` extension.

#### Example:

In the `client` code:

```javascript
<script type="module">
import { getRandomNumber } from "/shared/src/index.js";

console.log(getRandomNumber(1, 10))
```

### 9. **Fix dev server to inlcude hosting client with library**

Server was moved to script/serve.py

```python
import os
from http.server import SimpleHTTPRequestHandler
from socketserver import TCPServer
import mimetypes

class CustomHTTPRequestHandler(SimpleHTTPRequestHandler):
    def guess_type(self, path):
        # This will ensure that JavaScript files are served with the correct MIME type
        mime_type, _ = mimetypes.guess_type(path)
        if path.endswith(".js"):
            mime_type = "application/javascript"  # Correct MIME type for JS modules
        return mime_type

# Get the absolute path of the project root directory
project_root = os.path.dirname(os.path.abspath(__file__))  # This is the 'scripts' folder

# Set the root directory to the parent directory of the 'scripts' folder (i.e., project root)
os.chdir(os.path.dirname(project_root))

# Define the port for the server
PORT = 8000

# Create and start the server with the custom handler
with TCPServer(("", PORT), CustomHTTPRequestHandler) as httpd:
    print(f"Serving at http://localhost:{PORT}/client")
    httpd.serve_forever()
```

### 10. **Optional: Bundle the Shared Library for the Client**

If your shared library grows complex, consider bundling it with tools like Rollup or Webpack to ensure compatibility across environments.

---

With this setup, your `shared` library can be easily reused in both the server and client projects. The `npm workspaces` ensure that dependencies are linked automatically.

[Back](index.md)
