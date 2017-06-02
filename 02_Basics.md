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
