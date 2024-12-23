[Back](index.md)

# Dev Server

## Python

Script to serve web pages during dev.  
Ensure that your HTML file is located in the same directory as the Python script or provide the path to your HTML file.

```python
import os
from http.server import SimpleHTTPRequestHandler
from socketserver import TCPServer

# Define the directory to serve (the current directory in this case)
directory = os.path.dirname(os.path.abspath(__file__))

# Change the directory to the one containing the HTML file
os.chdir(directory)

# Define the port for the server
PORT = 8000

# Create and start the server
with TCPServer(("", PORT), SimpleHTTPRequestHandler) as httpd:
    print(f"Serving at http://localhost:{PORT}")
    httpd.serve_forever()
```

```bash
python serve.py
```

```bash
.\serve.py
```

[Back](index.md)
