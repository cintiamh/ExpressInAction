# Middleware

Framework-free Node has you writing a single large request handler function for your entire app.

Middleware allows you to break these request handlers into smaller bits.

Conceptually middleware is the biggest part of Express.

## Middleware and the middleware stack

In Node, the `request` and `response` objects are passed through just one function.

In Express, these objects are passed through an array of functions, called the middleware stack.

Every function in this stack takes three arguments:

* `request` - from Node
* `response` - from Node
* `next` - (function) when `next()` is called, Express will go on to the next function in the stack.

```javascript
function(request, response, next) { ... }
```

Eventually, one of these function in the stack must call `response.end()`, which will end the request.

(In Express you can also call `response.send()` or `response.sendFile()`, they call `response.end()` internally).

## Example App: A static file server

### Restart your server automatically

This will automatically restart after any changes.

```
$ npm i nodemon --save-dev
```

package.json
```javascript
scripts {
  "dev": "nodemon app.js",
  "start": "node app.js"
}
```

### Adding static file middleware to the middleware stack

```javascript
app.use(function(req, res, next) {
  var filePath = path.join(__dirname, 'static', req.url);
  fs.stat(filePath, function(err, fileInfo) {
    if (err) {
      next();
      return;
    }
    if (fileInfo.isFile()) {
      res.sendFile(filePath);
    } else {
      next();
    }
  });
});
```

With `express.static`:

```javascript
var express = require("express");
var morgan = require("morgan");
var path = require("path");

var app = express();

app.use(morgan("short"));

var staticPath = path.join(__dirname, "static");
app.use(express.static(staticPath));

app.use(function(req, res) {
  res.status(404);
  res.send("File not found!");
});

app.listen(3000, function() {
  console.log("App started on port 3000");
});
```

## Error handling middleware

There are two types of middleware:
* middleware functions that takes three arguments (request, response, next(optional))
* less used: error-handling middleware. When your app enters error-handling mode, the regular middleware are ignored and only runs error-handling middlewares.

To enter error-handling mode:
* call `next` with an argument.

```javascript
next(new Error('Something bad happened.'));
```

error-handling middleware is conventionally placed at the end of your middleware stack.

```javascript
app.use(function(req, res, next) {
  res.sendFile(filePath, function(err) {
    if (err) {
      next(new Error("Error sending file!"));
    }
  });
});
```

Error-handling middleware example:
```javascript
// Error-handling middleware functions are identified by having 4 arguments.
app.use(function(err, req, res, next) {
  console.error(err);
  // if you call next without error, it will exit the error-handling mode.
  next(err);
})
```

## Other useful middleware

* `express.static`
* body-parser: https://github.com/expressjs/body-parser
* cookie-parser: https://github.com/expressjs/cookie-session
* compression: https://github.com/expressjs/compression
* Full list here: http://expressjs.com/en/resources/middleware.html

Third-party:
* Helmet: https://github.com/helmetjs/helmet
* connect-assets: https://github.com/adunkman/connect-assets
