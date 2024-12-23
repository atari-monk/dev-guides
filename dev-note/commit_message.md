[Back](index.md)

# Git commit message guide

A good Git commit message format helps maintain clarity and consistency in your project's version history. Below is a commonly accepted format for Git commit messages:

### General Format:

```
<type>(<scope>): <short description>

<body (optional)>

<footer (optional)>
```

### 1. **Commit Type**:

The type defines the kind of change. Common commit types include:

-   **feat**: A new feature.
-   **fix**: A bug fix.
-   **docs**: Documentation only changes.
-   **style**: Code style changes (formatting, missing semicolons, etc.)
-   **refactor**: Refactoring code (changes that neither fix a bug nor add a feature).
-   **test**: Adding or correcting tests.
-   **chore**: Routine maintenance or tasks that don’t affect code (e.g., build, dependencies).

### 2. **Scope (optional)**:

The scope provides additional context about what area of the code the change relates to. For example:

-   `feat(auth): add login functionality`
-   `fix(api): resolve error handling`

If there is no clear scope, you can omit it.

### 3. **Short Description**:

A brief, concise summary of the change. The description should be written in the imperative mood (e.g., "Add" instead of "Added" or "Adding"). Keep it under 50 characters if possible.

### 4. **Body (optional)**:

Provides a detailed explanation of what and why the change was made. This can be several lines long, depending on the complexity of the change.

-   Explain why the change was made.
-   How was it fixed or implemented?
-   If applicable, mention any relevant issue numbers.

### 5. **Footer (optional)**:

Used to reference related issues or breaking changes.

-   **Breaking changes**: If your commit introduces breaking changes, indicate them in the footer using the phrase `BREAKING CHANGE:`.

Example with a breaking change:

```
fix(auth): handle session expiration error

- Fixes the issue where the session expiration was not properly handled.
- Also refactored the expiration handler.

BREAKING CHANGE: The API for session expiration has changed. Please update your session handling logic accordingly.
```

### Example Git Commit Messages:

1. **Simple Commit (fix)**:

```
fix(auth): correct session expiration handling
```

2. **Feature Commit (feat)**:

```
feat(search): add full-text search functionality
```

3. **Documentation Commit (docs)**:

```
docs(readme): update installation instructions
```

4. **Refactor Commit (refactor)**:

```
refactor(user): simplify login validation logic
```

### Summary:

-   Use **imperative** mood (e.g., "Add", "Fix", "Update").
-   Keep the **subject line short** (around 50 characters).
-   Provide **detailed information** in the body if needed.
-   Use the **footer** for issues or breaking changes.

This format helps improve the clarity and traceability of your code changes, making it easier for others (and yourself) to understand the history of the project.

[Back](index.md)
