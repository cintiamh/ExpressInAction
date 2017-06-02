# What is Express

* Node.js - run JavaScript on servers.
* Express - a framework to make Node.js easier to use.
* Express has middleware and routing.
* Express has request handler functions.

## What is this Node.js Business?

Node.js is a JavaScript platform. It lets you run JavaScript outside of a browser.

```
$ node myfile.js
```

* The JavaScript engine is fast.
* Works asynchronously.

## What is Express

Express is a small framework on top of Node.js's web server functionality.

* Simplify with middleware and routing.

Request Handler - the JavaScript function that processes browser requests in your application.

Node.js's HTTP server handles the connection between the client and your JavaScript function so that you don't have to handle tricky network protocols.

### What Express adds to Node.js

* Adds conveniences to Node's HTTP server, abstracting a lot of its complexity.
* Lets you refactor one monolithic request handler function into smaller request handlers that handles specific bits and pieces.

## Express minimal philosophy

Express is a very flexible framework. Express is minimalist and often used in combination with other frameworks and libraries.

## The core parts of Express

* Middleware
* Routing
* Subapplications
* Conveniences

### Middleware

The middleware functions are the smaller request handlers where each deal with a small chunk of code, replacing the ONE monolithic request handler function.

There are several middlewares implemented for Express. You just need to look one that suits your purposes.

### Routing

Like middleware, it breaks the one monolithic request handler function into smaller pieces.

These request handlers are executed conditionally, depending on what URL and HTTP method a client sends.

### Subapplications

These miniapplications are called routers.

Express allows you to define routers that can be used in larger applications.
A good example is to create a separate subapplication for the Admin Panel.

### Conveniences

Express has a lot of helper functionalities for files, requests, and HTML rendering, for example.

## The ecosystem surrounding Express

Because Express is extensible, there are many third-party modules that works well with Express.

### Express vs. other web application frameworks

* Hapi.js
* Meteor - full stack

### What Express is used for

* Build any web application
* MEAN stack (MongoDB, Express, Angular, Node.js)
* SPA (single page applications)

### Third-party modules for Node.js and Express

There is no such thing as an Express module - only a Node.js module.
A Node.js module can be compatible with Express and work well with its API.

References:
* http://expressjs.com/
* https://github.com/expressjs/express/tree/master/examples

## Hello World!

```javascript
// Requires Express and puts in a variable
var express = require('express');
// Creates a new Express application and assigns to the app variable
var app = express();

// Sends "Hello World!"
app.get('/', function(request, response) {
  response.send("Hello, World!");
});

// Starts the Express server on port 3000 and logs
app.listen(3000, function() {
  console.log('Express app started on port 3000.');
});
```
