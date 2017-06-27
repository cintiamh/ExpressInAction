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

`/search?q=javascript-themed%20burrito`

```javascript
app.get('/search', function(request, response) {
  // request.query.q == "javascript-themed burrito"
  // ...
});
```

If you visit `?arg=something`, then `req.query.arg` will be a string.
If you visit `?arg=something&stuff`, then `req.query.arg` will be an array.

## Using routers to split up your app

When you think: "This `app.js` file is getting big," it's time to think about breaking down your app with routers.

Routers in action: the main app
```javascript
var express = require('express');
var path = require('path');
var apiRouter = require('./routers/api_router');

var app = express();

var staticPath = path.resolve(__dirname, 'static');
app.use(express.static(staticPath));
app.use('/api', apiRouter);
app.listen(3000);
```

routes/api_router.js
```javascript
var express = require('express');

var ALLOWED_IPS = [
  '127.0.0.1',
  '123.456.7.89'
];

var api = express.Router();

api.use(function(req, res, next) {
  var userIsAllowed = ALLOWED_IPS.indexOf(req.ip) !== -1;
  if (!userIsAllowed) {
    res.status(401).send('Not authorized!');
  } else  {
    next();
  }
});

api.get('/users', function(req, res) {});
api.post('/user', function(req, res) {});
api.get('/messages', function(req, res) {});
api.post('/message', function(req, res) {});

module.exports = api;
```

Router with many subrouters.

## Serving static files

HTML, CSS, images, etc.

### Static files with middleware
