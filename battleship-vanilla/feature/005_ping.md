[Back](index.md)

# Ping

As a refactor of feature socket.io, ping feature is encapsulated/extracted.

1. [PingService](#pingService)

## PingService

Class creates div with ping text from server.

```javascript
export class PingService {
    constructor(socket) {
        this.socket = socket;
        this.pingDiv = this.createPingDiv();
        this.init();
    }

    createPingDiv() {
        let pingDiv = document.getElementById("ping");
        if (!pingDiv) {
            pingDiv = document.createElement("div");
            pingDiv.id = "ping";
            document.body.appendChild(pingDiv);
        }
        return pingDiv;
    }

    init() {
        this.socket.on("ping", (message) => this.handlePing(message));
    }

    handlePing(message) {
        this.pingDiv.textContent = message;
    }
}
```

This style puts it in left bottom of page.

```css
#ping {
    position: fixed;
    bottom: 10px;
    left: 10px;
    background-color: rgba(0, 0, 0, 0.8);
    color: white;
    padding: 10px;
    border-radius: 5px;
    font-family: Arial, sans-serif;
    font-size: 14px;
    z-index: 1000;
}
```

##

[Back](index.md)
