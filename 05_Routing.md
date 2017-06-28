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

#### Routing with multiple static file directories

```javascript
var publicPath = path.resolve(__dirname, "public");
var userUploadsPath = path.resolve(__dirname, "user_uploads");

app.use('/public', express.static(publicPath));
app.use('/uploads', express.static(userUploadsPath));
```

### Routing to static files

Getting a user profile photo:
```javascript
app.get("/users/:userid/profile_photo", function(req, res) {
  res.sendFile(getProfilePhotoPath(req.params.userid));
})
```

## Using Express with https

* Transport Layer Security - TLS - uses public key cryptography.
* Secure Sockets Layer - SSL

1. Generate your public and private keys using OpenSSL. (it comes pre-installed on MacOS)
2. Type the commands:

```
$ openssl genrsa -out privatekey.pem 1024
$ openssl req -new -key privatekey.pem -out request.pem
```

3. Request a certificate from a Certificate Authority (VeriSign, Google, or https://letsencrypt.org/).
4. Once you have a certificate, you can use Node's built-in HTTPS module with Express.

```javascript
var express = require('express');
var https = require('https');
var fs = require('fs');

var app = express();
// ... do stuff
var httpsOptions = {
  key: fs.readFileSync("path/to/private/key.pem"),
  cert: fs.readFileSync("path/to/certificate.pem")
};
https.createServer(httpsOptions, app).listen(3000);
```
