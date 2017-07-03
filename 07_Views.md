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

// app.locals will be available in the app context.
app.locals.appName = "Song Lyrics";

// Defines the default view engine (no extension files appends `.jade`).
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

#### View Caching

You can set `app.enabled('view cache')` or disable `app.disable('view cache')`.
If it's truthy, Express will cache the lookup of the view. By default, this is disabled in development mode and enabled in production.

Express looks at your file extension to determine which engine to use.

#### Rendering non-HTML views

You can render text, XML, JSON, etc.
Change is `res.type`:

```javascript
app.get('/', function(req, res) {
  res.type('text');
  res.render('myview', {
    currentUser: 'Gilligan'
  });
});
```

You can also use `res.json`.

### Making all view engines compatible with express

Use Consolidate.js: https://github.com/tj/consolidate.js

## Everything you need to know about EJS

### The syntax of EJS

```html
Hi <%= name %>!
You were born in <%= birthyear %>, so that means you're
 <%= (new Date()).getFullYear() - birthyear %> years old.
<% if (career) { -%>
  <%=: career | capitalize %> is a cool career!
<% } else { -%>
  Haven't started a career yet? That's cool.
<% } -%>
Oh, let's read your bio: <%- bio %> See you later!
```
