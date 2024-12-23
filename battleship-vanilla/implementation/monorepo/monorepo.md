[Back](index.md)

# Setup monorepo

In a true monorepo setup, dependencies are typically managed at the root level, and individual packages or services like the server should share the same `node_modules` from the root. The goal is to avoid having separate `node_modules` folders for each service.

Let me provide you with a corrected, truly monorepo-style setup where **dependencies are centralized at the root level**.

### Step-by-Step Guide for a Real Monorepo with Shared Dependencies

### Step 1: Initialize Your Monorepo

1. **Create the root directory for your monorepo**:

    ```bash
    mkdir my-monorepo
    cd my-monorepo
    ```

2. **Initialize a new Git repository** (optional but recommended):

    ```bash
    git init
    ```

3. **Initialize a new `package.json` file at the root**:

    This is where we’ll manage shared dependencies and configuration for all services.

    ```bash
    npm init -y
    ```

4. **Set up `workspaces` in the root `package.json`**:

    The `workspaces` field tells npm to treat the `server` and any other services as packages within the monorepo.

    Here's an example `package.json` for the root:

    ```json
    {
        "name": "my-monorepo",
        "private": true,
        "workspaces": ["server"]
    }
    ```

    The `private: true` field is required to prevent publishing the root package.

### Step 2: Run the Monorepo

To install all dependencies for all services and packages in the monorepo, run the following from the root directory:

```bash
npm install
```

This will install dependencies for both the `server` and `client` services, and they will share the same `node_modules` folder at the root.

### Summary

-   **Monorepo Structure**: Dependencies are installed at the root, and each service (like `server`) refers to those dependencies.
-   **Centralized Dependencies**: Instead of having `node_modules` in each service, they are shared and managed at the root.
-   **npm Workspaces**: These allow you to manage multiple services within one repo, and npm will handle linking them automatically.

This setup avoids redundancy, as all services share the same set of dependencies, and it's a true monorepo in that regard. Let me know if you have any further questions!

[Back](index.md)
