[Back](index.md)

# Libraries

Browser runtime is different codebase than node.js.  
For that reason it is necessary to generate libs client lib, server lib.  
Server stuff in shared library would mean error in browser runtime.

1. Generate folders

```bash
mkdir client_lib
mkdir server_lib
```

2. Generate package.json in them.

```bash
npm init -y
```

3. Add libs to monorepo workspace.

```json
"workspaces": [
    "shared",
    "server_lib",
    "server",
    "client_lib",
    "client"
  ],
```

4. Check main path

```json
{
    "name": "client_lib",
    "version": "1.0.0",
    "main": "src/index.js",
    "scripts": {},
    "keywords": [],
    "author": "",
    "description": ""
}
```

5. Add code and export it in index.js

```javascript
export * from "./SocketClient.js";
export * from "./ClientPing.js";
```

6. Add lib to client, server package.json.

```json
"dependencies": {
    "shared": "*",
    "client_lib": "*"
  }
```

7. Import in client, server.

```javascript
import { SocketClient, ClientPing } from "/client_lib/src/index.js";
```

[Back](index.md)
