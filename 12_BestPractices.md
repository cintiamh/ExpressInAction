# Best practices

## Simplicity

Check list:

* Look at a piece of your code and ask yourself how many other things you'd need to know in order to understand it.
  - Does it depend on a middleware earlier in the stack?
  - How many different database models does it depend on?
  - How many routes deep are you?
  - How many files have you looked at to get to this point?
* How confused are your fellow developers? How quickly could they add a feature to your app?

## File structure pattern

* `/bin` - folder that holds executable scripts relevant to your application.
* `/config` - folder that will hold any configuration for your app.
* `/public` - folder that is served by static file middleware (HTML, text files, images, videos, etc).
* `/routes` - folder that holds numerous JavaScript files, each one exporting an Express router.
* `/test` - folder that holds all of your test code.
* `/views` - folder that holds all of your views (EJS or Pug - jade).
* `app.js` - the main application code (the entry point)
* `package.json`

Official Express application generator (creates the structure above automatically):
```
$ npm install -g express-generator
$ express-generator my-new-app
```

## Locking down dependency versions

npm uses semantic versioning (semver).
Versions are broken up into three numbers:
* major - change that is considered breaking.
* minor - change that is not considered breaking.
* patch - bug fixes and performance enhancements.

To avoid any problems in production, you can lock versions down.

### The simple way: eschewing optimistic versioning

```javascript
"dependencies": {
  "express": "^5.0.0",
  "ejs": "~2.3.2"
}
```

* `ˆ` indicates optimistic versioning is allowed. You'll get all patch and minor updates.
* `~` indicates a less optimistic versioning. You'll get only patch updates.
* Just numbers indicates an exact version.

You can install a new package with exact version:
```
$ npm install --save-exact express
```

It installs the npm package with exact version (latest), but it doesn't do the same with its dependencies (if they are optimisticly versioned).

### The thorough way: npm's shrinkwrap command

The problem with the previous solution is that it doesn't lock down subdependency versions.

npm has a subcommand called `shrinkwrap` that solves this problem.

After you run `npm install` and everything is working fine, run:

```
$ npm shrinkwrap
```

If everything goes well, it will output:
```
wrote npm-shrinkwrap.json
```

The generated file specifies the whole dependency tree.

`npm install` first checks this file before checking package.json.

### Upgrading and adding dependencies

To update you'll need to run npm install with specific version:
```
$ npm install express@4.12.1
```

To install a new package:
```
$ npm install helmet
```

Once everything is fine, you need to run shrinkwrap again to lock in that dependency version:
```
$ npm shrinkwrap
```

shrinkwrap is not for everyone.

## Localized dependencies

npm allows you to install packages globally in your machine.

But you can find some problems when someone new comes into your project.

* A new developer doesn't have Grunt, for example, installed in their system at all.
* What if they have Grunt installed, but it's the wrong version?

### Invoking commands directly:

```
$ node_modules/.bin/mocha
```

### Executing commands from npm scripts

package.json:
```javascript
"scripts": {
  "test": "mocha"
}
```

Run on terminal
```
$ npm test
```
