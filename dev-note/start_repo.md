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

3. Add prettier config

add `.prettierrc` file (for example):

```json
{
    "tabWidth": 2,
    "semi": false,
    "singleQuote": true
}
```

4. Add `.gitignore` file (for example):

```plaintext
node_modules
```

5. Add `README.md` file (for example):

6. Publish on github as private/public with VSCode.
