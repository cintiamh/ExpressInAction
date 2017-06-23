# The basics of Node.js

## Installing Node

* Download from official website: https://nodejs.org
* You can also use Homebrew.

You might also want to install Node Version Manager (NVM): https://github.com/creationix/nvm.

### Running Node script

```
$ node hello.js
```

## Using modules

At its core, CommonJS lets you include code from one file in another.

### Requiring built-in modules

Node has a number of built-in modules like filesystem access in a module called `fs` to utility functions in a built-in module called `util`.

https://nodejs.org/api/modules.html

```javascript
var url = require('url');
var parsedURL = url.parse('http://www.example.com/profile?name=barry');

console.log(parsedURL.protocol); // http:
console.log(parsedURL.host); // www.example.com
console.log(parsedURL.query); // name=barry
```

### Requiring third-party modules with package.json and npm

At the root of every Node project there's a file called `package.json`.
It is a simple JSON file that defines project metadata like the name, version, authors and dependencies.

`npm` is an official helper for Node.

When you list a dependency in `package.json`, anyone else can just run `npm install` and install all the dependencies locally.
(You don't want to copy around all the content from `node_modules` folder).

You can also automatically generate a `package.json`:
```
$ npm init
```

Publishing a simple package to npm:
http://evanhahn.com/make-an-npm-baby/

The most common external resources you'll deal with in Express are:

* Anything involving the filesystem (reading / writing files from the disk)
* Anything involving a network (receiving / sending requests / responses)

Asynchronous tasks are handled by callbacks!

Reading a file from the disk:
```javascript
var fs = require('fs');
var options = { encoding: 'utf-8' };
fs.readFile('myFile.txt', options, function(err, data) {
  if (err) {
    console.log('Error reading file!');
    return;
  }
  console.log(data.match(/x/gi).length + ' letter Xs');
});
```

What the heck is the event loop anyway?
https://www.youtube.com/watch?v=8aGhZQkoFbQ

## Building a web server with Node: The http module

`http.createServer()` - this function takes a callback that's called every time a request comes into your server, and it returns a server object.

```javascript
var http = require('http');
function requestHandler(request, response) {
  console.log('In comes a request to: ' + request.url);
  response.end('Hello, World!');
}
var server = http.createServer(requestHandler);
server.listen(3000);
```

Be aware that Node.js supports `https` as well.
