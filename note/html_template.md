[Back](index.md)

# Html Template

## Content

1. [Html](#html)
2. [Single File](#single-file)
3. [Links](#links)

## Html

[Content](#content)

Starting point for html file.

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title></title>
    </head>
    <body></body>
</html>
```

## Single File

[Content](#content)

Html with style and script.
For test, prototype.

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title></title>
        <script src="https://cdn.socket.io/4.6.0/socket.io.min.js"></script>
        <style>
            <!-- put css here -->
        </style>
    </head>
    <body>
        <h1>Some Text</h1>
        <div id="someId">
            <!-- Some ui element -->
        </div>

        <script type="module">
            <!-- put js here -->
        </script>
    </body>
</html>
```

## Links

[Content](#content)

Html with links.

```html
<!DOCTYPE html>
<html lang="en">
    <head>
        <meta charset="UTF-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title></title>
        <link rel="stylesheet" href="src/css/styles.css" />
        <script src="https://cdn.socket.io/4.6.0/socket.io.min.js"></script>
    </head>
    <body>
        <h1></h1>
        <div id="someId"></div>

        <script type="module" src="src/app.js"></script>
    </body>
</html>
```

[Back](index.md)
