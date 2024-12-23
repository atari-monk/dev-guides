[Back](index.md)

# Git commit message convention

## Format:

```
<type>(<scope>): <short description>

<body (optional)>

<footer (optional)>
```

-   **Type**: The kind of change.

```plaintext
feat: New feature
fix: Bug fix
docs: Documentation changes
style: Code formatting (no functional changes)
refactor: Code restructuring (no functional changes)
test: Adding or updating tests
chore: Miscellaneous tasks
perf: Performance improvements
build: Changes to the build system or dependencies
ci: Continuous integration configuration
revert: Reverting a previous commit
hotfix: Urgent fix for a bug or issue
```

-   **Scope**: The area of the code affected (optional) (e.g., auth, payment-handler, homepage).
-   **Short Description**: A brief summary of the change.
-   **Body**: An optional detailed explanation.
-   **Footer**: Optional extra information (e.g., references to issues).

## Example

```plaintext
init: initial project setup

Created the initial project structure with basic configuration files. Includes README, .gitignore, and package.json for dependency management.

No functional code yet.
```

```plaintext
feat(auth): add JWT token authentication

Added JWT token-based authentication to the backend. Users can now log in using their credentials, and a JWT token will be returned for further requests. This improves security and allows for scalable user sessions.

Closes #45
```

```
fix(cart): resolve incorrect item quantity calculation

Fixed an issue where the quantity of items in the shopping cart was being calculated incorrectly when the user added multiple items. This ensures the cart displays the correct number of items for checkout.

Fixes #102
```

[Back](index.md)
