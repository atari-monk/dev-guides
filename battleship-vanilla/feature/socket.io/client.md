[Back](index.md)

# Client

Basic socket.io client to test connection with server using ping event.

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>Socket.IO Ping Client</title>
        <script src="https://cdn.socket.io/4.6.0/socket.io.min.js"></script>
        <style>
            body {
                font-family: Arial, sans-serif;
                margin: 20px;
            }
            #messages {
                border: 1px solid #ddd;
                padding: 10px;
                height: 200px;
                overflow-y: scroll;
            }
            .message {
                margin: 5px 0;
            }
        </style>
    </head>
    <body>
        <h1>Ping Messages</h1>
        <div id="messages">
            <!-- Messages will appear here -->
        </div>

        <script>
            const socket = io("http://localhost:3000");

            socket.on("ping", (message) => {
                const messagesDiv = document.getElementById("messages");
                const newMessage = document.createElement("div");
                newMessage.className = "message";
                newMessage.textContent = message;
                messagesDiv.appendChild(newMessage);

                // Scroll to the bottom
                messagesDiv.scrollTop = messagesDiv.scrollHeight;
            });
        </script>
    </body>
</html>
```

[Back](index.md)
