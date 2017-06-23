# Foundations of Express

Express is an abstraction layer on top of Node's built-in HTTP server.

## Middleware

Express has a middleware stack, multiple request handlers happening in sequence.

### Hello World with Express

```
$ npm init
$ npm i express --save
```

This will create `package.json` file and install and save express npm package.

Now create `app.js` file:
```javascript
var express = require('express');
var http = require('http');
var app = express();
app.use(function(req, res) {
  console.log('In comes a request to: ' + req.url);
  res.end('Hello, World!');
});
http.createServer(app).listen(3000);
```

`express()` returns a request handler function.
It's an Express-made request handler that starts going through all the middleware until the end.

### How middleware works at a high level

Without middleware, you'd find yourself having one master request function that handles everything!

```
Request => Request handler function => Response
```

With middleware, it passes through an array of functions you write called middleware stack.

Each middleware function can modify the request or the response, but it doesn't always have to.

### Middleware code that's passive

```javascript
var express = require('express');
var http = require('http');
var app = express();

// The logging middleware only logs but doesnt change request or response.
app.use(function(request, response, next) {
  console.log('In comes a ' + request.method + ' to ' + request.url);
  next();
});

app.use(function(req, res) {
  res.writeHead(200, { "Content-Type": "text/plain" });
  res.end('Hello, World!');
});

http.createServer(app).listen(3000);
```

### Middleware code that changes the request and response

```javascript
app.use(function(request, response, next) {
  console.log('In comes a ' + request.method + ' to ' + request.url);
  next();
});

// It only logs in when time is even.
app.use(function(request, response, next) {
  var minute = (new Date()).getMinutes();
  if (minute % 2 === 0) {
    next();
  } else {
    response.statusCode = 403;
    response.end('Not authorized.');
  }
});

app.use(function(req, res) {
  res.end('Secret info: the password is "swordfish"!');
});
```

### Third-party middleware libraries

#### Example: logging middleware:

```
$ npm i morgan --save
```

```javascript
var express = require('express');
var http = require('http');
var logger = require('morgan');
var app = express();

app.use(logger('short'));

app.use(function(req, res) {
  res.end('Secret info: the password is "swordfish"!');
});

http.createServer(app).listen(3000);
```

#### Example: Using static files

`express.static` ships with Express and helps you serve static files.

```javascript
var express = require('express');
var http = require('http');
var logger = require('morgan');
var path = require('path');
var app = express();
var publicPath = path.resolve(__dirname, "public");

app.use(logger('short'));
app.use(express.static(publicPath));
app.use(function(req, res) {
  res.end('Secret info: the password is "swordfish"!');
});
http.createServer(app).listen(3000);
```

#### More examples:

* connect-ratelimit: limits the number of requests per hour.
* Helmet: helps you add HTTP headers to make your app safer against some kind of attacks.
* cookie-parser.
* response-time: Sends the X-Response-Time header so you can debug the performance of your application.

You can look for "Express middleware" or "Connect middleware" (connect is supported).

## Routing

Like middleware, but the functions are called only when you visit a specific URL with a specific HTTP method.

```javascript
var express = require('express');
var path = require('path');
var http = require('http');

var app = express();

var publicPath = path.resolve(__dirname, "public");

app.use(express.static(publicPath));

app.get('/', function(request, response) {
  response.end('Welcome to my homepage!');
});

app.get('/about', function(request, response) {
  response.end('Welcome to my about page!');
});

app.get('/weather', function(request, response) {
  response.end('The current weather is NICE.');
});

app.use(function(request, response) {
  response.statusCode = 404;
  response.end("404!");
});

http.createServer(app).listen(3000);
```

`app.get()` are Express's magical routing system. (you can also use any other HTTP verbs.).

These routes can get smarter.

```javascript
app.get('/hello/:who', function(request, response) {
  response.end('Hello, ' + request.params.who + ".");
});
```

## Extensions to request and response objects

http://expressjs.com/en/api.html

Express adds some stuff into request and response objects.

### Using redirect

```javascript
response.redirect("/hello/world");
response.redirect("http://expressjs.com");
```

### sendFile example

Let's you send a whole file.

```javascript
response.sendFile("/path/to/cool_song.mp3");
```

### Example: Blacklisting an IP

`request` objects gets some property and methods. Like `request.ip`, `request.get`

```javascript
var express = require('express');
var app = express();

var EVIL_IP = "123.45.67.89";

app.use(function(request, response, next) {
  if (request.ip === EVIL_IP) {
    response.status(401).send('Not allowed');
  } else {
    next();
  }
});
```

## Views

Views allow you to dynamically render HTML.

This book will be using EJS.

https://github.com/tj/ejs

```
$ npm i ejs --save
```

```javascript
var express = require('express');
var path = require('path');

var app = express();

app.set("views", path.resolve(__dirname, "views"));
app.set("view engine", "ejs");
```

Create the file: `views/index.ejs`:
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <title>Hello, world!</title>
  </head>
<body>
  <%= message %>
</body>
</html>
```

And in your `app.js` code include:
```javascript
app.get('/', function(request, response) {
  response.render("index", {
    message: "Hey everyone! This is my webpage!"
  });
});
```
