# Routing

Express's routing allow you to map different requests to different request handlers.

## What is Routing?

Routing maps verbs and URIs to specific code.

### Example

```javascript
var express = require('express');
var app = express();

// Routes GET requests to /olivia to the request handler.
app.get('/olivia', function(request, response) {
  response.send('Welcome to Olivias Homepage!');
});

// If you load something other than /olivia, servers a 404 error.
app.use(function(request, response) {
  response.status(404).send('Page not found!');
});

app.listen(3000);
```

## The features of routing

You can grab a parameter from the route:

```javascript
app.get('/users/:userid', function(request, response) {
  var userId = parseInt(request.params.userId, 10);
  // ...
});
```

### Using regular expressions to match routes

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Regular_Expressions

```javascript
// Defines the route URLs and captures digits using a regular expression.
app.get(/^\/users\/(\d+)$/, function(request, response) {
  var userId = parseInt(request.params[0], 10);
  // ...
});
```

### Grabbing query arguments
