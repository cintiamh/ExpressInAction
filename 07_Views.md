# Views and Templates: Pug and EJS

Pug was originally called Jade, but was changed for legal reasons.

## Express view features

View engine: module that does the actual rendering of views.

As long as the view engine exposes an API that Express expects, you're good to go.

### A simple view rendering

```
$ npm i ejs --save
```

```javascript
var express = require('express');
var path = require('path');

var app = express();

app.set('view engine', 'ejs');
app.set('views', path.resolve(__dirname, 'views'));

app.get('/', function(req, res) { res.render('index'); });

app.listen(3000);
```

### A complicated view rendering

```javascript
var express = require("express");
var path = require("path");
var ejs = require("ejs");

var app = express();

app.locals.appName = "Song Lyrics";

app.set("view engine", "jade");
app.set("views", path.resolve(__dirname, "views"));
app.engine("html", ejs.renderFile);

app.use(function(req, res, next) {
  res.locals.userAgent = req.headers["user-agent"];
  next();
});

app.get("/about", function(req, res) {
  res.render("about", {
    currentUser: "india-arie123"
  });
});

app.get("/contact", function(req, res) {
  res.render("contact.ejs");
});

app.use(function(req, res) {
  res.status(404);
  res.render("404.html", {
    urlAttempted: req.url
  });
});

app.listen(3000);
```
