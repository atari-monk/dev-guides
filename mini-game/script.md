[Back](index.md)

# Script

## Create Game Structure

Add new project template to a repo.

```python
#create_game_structure.py

import os
import sys

def create_game_structure(game_name):
    # Define the base directory where projects will be created
    base_proj_dir = "../"

    # Ensure the base directory exists
    try:
        os.makedirs(base_proj_dir, exist_ok=True)
        print(f"Ensured base directory exists: {base_proj_dir}")
    except Exception as e:
        print(f"Error creating base directory {base_proj_dir}: {e}")
        return

    # Define the project directory
    base_dir = os.path.join(base_proj_dir, game_name)

    # Define the structure
    structure = [
        base_dir,
        os.path.join(base_dir, "assets"),
    ]

    # Create directories
    for folder in structure:
        try:
            os.makedirs(folder, exist_ok=True)
            print(f"Created directory: {folder}")
        except Exception as e:
            print(f"Error creating directory {folder}: {e}")

    # Create default files
    files = {
        os.path.join(base_dir, "index.html"): "<!DOCTYPE html>\n<html>\n<head>\n    <title>Mini Game</title>\n    <link rel=\"stylesheet\" href=\"styles.css\">\n</head>\n<body>\n    <h1>Welcome to the Mini Game</h1>\n    <script src=\"script.js\"></script>\n</body>\n</html>",

        os.path.join(base_dir, "styles.css"): "/* Add your styles here */\nbody {\n    font-family: Arial, sans-serif;\n    margin: 0;\n    padding: 0;\n    text-align: center;\n}",

        os.path.join(base_dir, "script.js"): "// Add your game logic here\nconsole.log('Game initialized');",
    }

    for file_path, content in files.items():
        try:
            with open(file_path, 'w') as file:
                file.write(content)
            print(f"Created file: {file_path}")
        except Exception as e:
            print(f"Error creating file {file_path}: {e}")

    # Update the main index file
    update_main_index(base_proj_dir, game_name)

def update_main_index(base_proj_dir, game_name):
    index_file = os.path.join(base_proj_dir, "index.html")
    project_link = f"<li><a href=\"{game_name}/index.html\">{game_name}</a></li>\n"

    if not os.path.exists(index_file):
        # Create the index file if it doesn't exist
        try:
            with open(index_file, 'w') as file:
                file.write("<!DOCTYPE html>\n<html>\n<head>\n    <title>Projects Index</title>\n</head>\n<body>\n    <h1>Projects</h1>\n    <ul>\n")
                file.write(project_link)
                file.write("    </ul>\n</body>\n</html>")
            print(f"Created main index file: {index_file}")
        except Exception as e:
            print(f"Error creating main index file {index_file}: {e}")
    else:
        # Append the new project link if the index file exists
        try:
            with open(index_file, 'r+') as file:
                content = file.read()
                insertion_point = content.rfind("</ul>")
                if insertion_point != -1:
                    updated_content = content[:insertion_point] + project_link + content[insertion_point:]
                    file.seek(0)
                    file.write(updated_content)
                    file.truncate()
            print(f"Updated main index file with project link: {index_file}")
        except Exception as e:
            print(f"Error updating main index file {index_file}: {e}")

if __name__ == "__main__":
    if len(sys.argv) != 2:
        print("Usage: python create_game_structure.py <game_name>")
    else:
        game_name = sys.argv[1]
        create_game_structure(game_name)
```

## Server

[Dev Server](../note/dev_server.md)

##

[Back](index.md)
