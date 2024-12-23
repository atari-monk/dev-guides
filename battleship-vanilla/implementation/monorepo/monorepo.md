[Back](index.md)

# Setup Monorepo

A **monorepo** centralizes dependencies at the root level, avoiding redundant `node_modules` folders in individual packages or services.

### Instructions

1. **Setup repository**:

You can use guide [Start repository](../../../dev-note/start_repo.md).

2. **Create a root `package.json`**:

    ```bash
    npm init -y
    ```

3. **Enable npm workspaces** by adding this to `package.json`:

    ```json
    {
        "name": "repo-name-monorepo",
        "private": true,
        "workspaces": ["server"]
    }
    ```

4. **Install all dependencies from the root**:

    install packages

    ```bash
    npm i package package_2 package_3
    ```

    or state dependencies in package.json and run

    ```bash
    npm i
    ```

### Key Points

-   **Structure**: Centralized `node_modules` at the root.
-   **Dependencies**: Shared across all services (`server`, etc.).
-   **npm Workspaces**: Automates linking between packages.

[Back](index.md)
