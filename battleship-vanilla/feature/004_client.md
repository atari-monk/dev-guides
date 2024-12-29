[Back](index.md)

# Client

As a refactor of feature socket.io, client feature is encapsulated/extracted.
This can be done after extracting js and css from html.  
Help [here](../../note/html_template.md).

1. [Client](#client)

## Client

Class encapsulates socket.io client.

```javascript
export class Client {
    constructor(url, options) {
        this.url = url;
        this.options = options;
        this.socket = null;
    }

    initialize() {
        this.socket = io(this.url, this.options);
    }

    getSocket() {
        if (!this.socket) {
            throw new Error(
                "Socket has not been initialized. Call initialize() first."
            );
        }
        return this.socket;
    }
}
```

Usage

```javascript
import { Client } from "/shared/src/index.js";

const client = new Client("http://localhost:3000", {
    cors: {
        origin: "*",
    },
});
client.initialize();
const socket = client.getSocket();
```

[Back](index.md)
