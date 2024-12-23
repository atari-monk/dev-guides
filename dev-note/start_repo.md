[Back](index.md)

# Start repository

## Vanilla webdev

1. **Create the root directory**:

    ```bash
    cd C:\atari-monk\code
    Test-Path -Path "repo-name"
    New-Item -ItemType Directory -Path "repo-name"
    cd repo-name
    ```

2. **Initialize a Git repository (optional)**:

    ```bash
    git init
    ```

3. Add basic files

```bash
ni .prettierrc
ni .gitignore
ni README.md
```

4. Open in VSCode

```bash
code .
```

4. Add prettier config content (for example):

```json
{
    "tabWidth": 2,
    "semi": false,
    "singleQuote": true
}
```

4. Add `.gitignore` content (for example):

```plaintext
node_modules
```

5. Add `README.md` content (for example):

```plaintext
# repo_name

description
```

6. Publish on github as private/public with VSCode.

[Back](index.md)
