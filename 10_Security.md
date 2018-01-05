# Security

## The Security Mindset

Thinking like an attacker will help you defend your applications against the endless onslaught of possible security flaws.

## Keeping your code as Bug-Free as possible

* Testing is terribly important.
* Code reviews can be quite helpful.
* Don't reinvent the wheel.
* Stick to good coding practices.

### Enforcing good JavaScript with JSHint

### Halting after errors happen in callbacks

```javascript
fs.readFile("myfile.txt", function(err, data) {
  if (err)  {
    console.error(err);
    // It's important to THROW an error here, or it will continue to run with data.
    throw err;
  }
  console.log(data);
});
```

### Perilous parsing of query strings

You'll always want to make sure that your users are giving you the data you expect, and if they aren't, you'll need to do something about it.

```
$ npm i arraywrap --save
```

```javascript
var arrayWrap = require('arraywrap');
// ...
app.get("/search", function(req, res) {
  var search = arrayWrap(req.query.q || ""); // always becomes an array
  var terms = search[0].split("+");
  // ...
});
```

## Protecting your users

### Using HTTPS

Use HTTPS instead of HTTP. It helps protect your users against all kinds of attacks.

#### Force users to HTTPS

If the request is over HTTPS, it continues on to the rest of your middleware and routes. If not, it redirects to the HTTPS version.

https://github.com/aredo/express-enforces-ssl

```javascript
var enforceSSL = require("express-enforces-ssl");
// ...
app.enable("trust proxy");
app.use(enforceSSL());
```

#### Keep users on HTTPS

New browsers support HTTPS Strict Transport Security (HSTS).
It's a simple HTTP header that tells browsers to stay on HTTPS for a period of time.

```
Strict-Transport-Security: max-age=31536000
```

To set this header, you can use Helmet (https://github.com/helmetjs/helmet).

```javascript
var helmet = require("helmet");
var ms = require("ms");
// ...
app.use(helmet.hsts({
  // ms converts human readable strings into seconds
  maxAge: ms("1 year"),
  includeSubdomains: true
}));
```

This header is only effective if your users are already on HTTPS, which is why you need `express-enforces-ssl`.

### Preventing cross-site scripting attacks

Cross-site scripting (XSS) attack to get control of ones browser.

You could replace a simple name link with:

```html
Bruce Lee<script>transferMoney(1000000, "bruce-lee's-account");</script>
```

There is one big way to prevent XSS: never blindly trust user input.

#### Escaping user input

You can sanitize or escape user input, so that when you put it into your HTML, you aren't doing anything unexpected.

You'll want to sanitize things as much as you can and always keep the context in mind.

EJS use `<%= myString %>`

#### Mitigating XSS with HTTP headers

There's a simple header called `X-XSS-Protection` that can protect against reflected XSS.

```javascript
app.use(helmet.xssFilter());
```

https://www.html5rocks.com/en/tutorials/security/content-security-policy/

### Cross-site request forgery (CSRF) prevention

#### Protecting against CSRF in Express

https://github.com/expressjs/csurf

* It adds a method to the request object called `req.csrfToken`. You'll send this token whenever you send a form, for example.
* If the request is anything other than a GET, it looks for a parameter called `_csrf` to validate the request, creating an error if it's invalid.

```
$ npm i csurf --save
```

Example:
https://github.com/EvanHahn/Express.js-in-Action-code/blob/master/Chapter_10/csrf-example/app.js

## Keep your npm dependencies safe

* Audit the code yourself
* Make sure you're on the latest versions
* Check against the Node Security Project

```
$ npm outdated --depth 0
```

Node Security Platform: https://nodesecurity.io/advisories

```
$ npm install -g nsp
$ nsp audit-package
```

## Handling Server Crashes

Forever: https://github.com/foreverjs/forever

If your app crashes, Forever will try to restart it.

```
$ npm i forever --save
```

And replace your start script from package.json:

```javascript
...
"scripts": {
  "start": "forever app.js"
}
...
```

## Various little tricks

### No Express here

By default, Express publicizes itself. You can easily disable it:

```javascript
app.disable("x-powered-by");
```

So hackers won't know our app is running with Express.

### Preventing clickjacking

Clickjacking takes advantage of browser frames.

I could send you a link to a simple button, but in reality, the button page is concealing the social network page.

There's an `<iframe>` of the social network site, and it's invisible. So when you click the button, you're actually clicking on "Click to make profile public".

Most browsers listen for a header called `X-Frame-Options`.

X-Frame-Options has three options.
* DENY keeps anyone from putting your site in a frame, period.
* SAMEORIGIN keeps anyone else from putting your site in a frame, but your own site is allowed.
* You can also let one other site through with the ALLOW-FROM option.

I’d recommend the SAMEORIGIN or DENY options.
You can use Helmet to set this:

```javascript
app.use(helmet.frameguard("sameorigin"));
// or ...
app.use(helmet.frameguard("deny"));
```

### Keeping Adobe products out of your site

Make sure you're serving this XML content at the root of your site (at /crossdomain.xml):

```XML
<?xml version="1.0"?>
<!DOCTYPE cross-domain-policy SYSTEM "http://www.adobe.com/xml/dtds/cross-domain-policy.dtd">
<cross-domain-policy>
  <site-control permitted-cross-domain-policies="none">
</cross-domain-policy>
```

This prevents any Flash users from loading content off of your site, unless they come from your domain.

### Don't let browsers infer the file type

This example extends further if file.txt looks like HTML and the browser interprets it as HTML. That HTML page can contain malicious JavaScript, which could do lots of bad things!

Luckily, you can fix this with a single HTTP header. You can set the X-Content-Type-Options header to its only option, nosniff. Helmet comes with noSniff middleware, and you can use it like this:

```JavaScript
app.use(helmet.noSniff());
```
