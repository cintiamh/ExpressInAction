# Building APIs

API - application programming interface.

In this chapter these APIs will take HTTP requests and respond with JSON data.

## A basic JSON API example

If you make an API that takes requests from computers and spits out response for computers, you can build UIs atop that API.

API HTTP request example:
```
/timezone?tz=America+Los_Angeles
```

JSON response example:
```javascript
{
  "time": "2015-06-09T16:20:00+01:00",
  "zone": "America/Los_Angeles"
}
```

## A simple express-powered JSON API

* take a request
* parse it
* respond with a JSON and an HTTP status code.

APIs don't have to use JSON, it can be XML or plain text.

Example: Simple API that generates a random number.

Create package.json file and install express:
```
$ npm init
$ npm install express --save
```

The code for `app.js`:
```javascript
var express = require('express');
var app = express();

app.get('/random/:min/:max', function(req, res) {
  var min = parseInt(req.params.min, 10);
  var max = parseInt(req.params.max, 10);

  if (isNaN(min) || isNaN(max)) {
    res.status(400);
    res.json({ error: "Bad request." });
    return;
  }

  var result = Math.round((Math.random() * (max - min)) + min);
  res.json({ result: result });
});

app.listen(3000, function() {
  console.log("App started on port 3000.");
});
```

You can start the server and go to http://localhost:3000/random/10/100.

## Create, Read, Update, Delete APIs (CRUD)

### HTTP verbs

#### GET

The most common one. It gets the resources. GET methods shouldn't change the state of your app.

#### POST

POST is used to create records on servers, not modify existing records. POST is also used for actions.

#### PUT

A better name would be UPDATE or CHANGE. If you try to PUT changes to a record that doesn't exist, the server can, but doesn't have to, create the record.

#### DELETE

It will delete something.

There is nothing that strictly enforces these constraints.

Handling different HTTP verbs:
```javascript
var express = require("express");

var app = express();

app.get("/", function(req, res) {
  res.send("you just sent a GET request, friend");
});

app.post("/", function(req, res) {
  res.send("a POST request? nice");
});

app.put("/", function(req, res) {
  res.send("i don't see a lot of PUT requests anymore");
});

app.delete("/", function(req, res) {
  res.send("oh my, a DELETE??");
});

app.listen(3000, function() {
  console.log("App is listening on port 3000");
});
```

You can test using `curl`:

```
$ curl http://localhost:3000
you just sent a GET request, friend
$ curl -X POST http://localhost:3000
a POST request? nice
$ curl -X PUT http://localhost:3000
i don't see a lot of PUT requests anymore
$ curl -X DELETE http://localhost:3000
oh my, a DELETE??
```

## API versioning

Add version information to your API.

```
/v1/timezone
```

```
/v2/timezone
```

You can use Express's routes to do that.

You can have two files: `api1.js` and `api2.js`. And reference both in `app.js`:

```javascript
var express = require('express');
var apiVersion1 = require('./api1.js');
var apiVersion2 = require('./api2.js');

var app = express();

app.use('/v1', apiVersion1);
app.use('/v2', apiVersion2);

app.listen(3000, function() {
  console.log('App started on port 3000.');
});
```

## Setting HTTP status codes

HTTP status ranges in a nutshell:

* 1XX: hold on
* 2XX: here you go
* 3XX: go away
* 4XX: you messed up
* 5XX: I messed up

https://en.wikipedia.org/wiki/List_of_HTTP_status_codes

### Setting HTTP status codes

* 200: the default status code
* 404 error: no resource found and no request handler for it.
* 500: another Express server error.

Setting the HTTP status code in Express:
```javascript
res.status(404);
```

Setting the HTTP status code and sending some JSON:
```javascript
res.status(404).json({ error: "Resource not found!" });

// This is equivalent to:
res.status(404);
res.json({ error: "Resource not found!" });
```

### The 100 range

* 100: Continue
* 101: Switching Protocols

### The 200 range

* 200: OK
* 201: Created (from POST or PUT)
* 202: Accepted (it was checked, but not created)
* 204: No Content (delete version of 201)

### The 300 range

* 301: Moved Permanently (the page has moved)
* 303: See Other
* 307: Temporary Redirect

### The 400 range

* 401 and 403: unauthorized and forbidden errors
* 404: Not Found

Other errors: https://en.wikipedia.org/wiki/List_of_HTTP_status_codes

### The 500 range

* 500: Internal Server Error
