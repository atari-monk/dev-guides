[Back](index.md)

# Dev Server

## Python

Script to serve web pages during dev.  
Ensure that your HTML file is located in the same directory as the Python script or provide the path to your HTML file.

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
PORT = 8100

# Create and start the server with the custom handler
with TCPServer(("", PORT), CustomHTTPRequestHandler) as httpd:
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
