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
