# Node.js introduction
[Node.js](https://nodejs.org/) is a runtime which uses Chrome's V8 JavaScript library to run standard JS code. It can run on your desktop, server, or on embedded devices. The scope is wider that a browser so it has [additional built-in libraries](https://nodejs.org/dist/latest/docs/api/) for handling files, network requests, OS settings, etc.

Node.js has become popular because it's:

1. JavaScript -- many developers know it already
1. extendable -- there are thousands of libraries available via npm
1. available everywhere,
1. fast. Really fast.


## Basics
Create a `.js` file, e.g.

```javascript
// myfirstapp.js

for (let i = 10; i > 0; i--) {
  console.log(i);
}

console.log('my first Node.js app!');
```

Then run it from the command line with `node myfirstapp.js`.

Or you could create a web server in a few lines of code:

```javascript
// server.js
const
  hostname = '127.0.0.1',
  port = 3000,
  http = require('http'),
  server = http.createServer((req, res) => {
    res.statusCode = 200;
    res.setHeader('Content-Type', 'text/plain');
    res.end('Hello World!\n');
  });

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});
```

Run this in your terminal then open `http://localhost:3000/` in a browser.


## CommonJS modules
Functions, values, and any other types can be written in other module files and exposed with `module.exports`, e.g.

```javascript
// sayhello.js
function sayHello(name = 'anonymous') {
  'use strict';
  console.log(`Hello ${name}!`);
}

module.exports sayHello;
```

This can then be used in other code or modules by referencing the file with `require`, e.g.

```javascript
const speak = require('sayhello.js');

speak('Rik');
```

There are numerous options and you can use patterns like revealing modules, e.g.

```javascript
// datelib.js
module.exports = (() => {

  'use strict';

  // make a real date or return null
  function make(d) {
    let date = (d instanceof Date ? d : new Date(d));
    if (String(date) === 'Invalid Date') return null;
    return date;
  }

  // get start of month
  function monthStart(d) {
    let date = make(d || new Date());
    if (date) {
      date.setUTCDate(1);
    }
    return date;
  }


  // get end of month
  function monthEnd(d) {
    let date = monthStart(d);
    if (date) {
      date.setUTCMonth(date.getUTCMonth() + 1);
      date.setUTCDate(0);
    }
    return date;
  }

  return {
    make: make,
    monthStart: monthStart,
    monthEnd: monthEnd
  }

})();
```

Then use elsewhere:

```javascript
const datelib = require('datelib'); // the .js is not necessary

let d = datelib.make();
console.log( datelib.monthStart() );
console.log( datelib.monthEnd() );
```

Note that Node.js 10+ will support ES6 modules. These operate in a similar way, but must be contained in `.mjs` files.


## npm - Node Package Manager
Thousands of third-party modules are available from [npmjs.com](https://www.npmjs.com/) and can be installed and managed with the `npm` utility supplied with Node.js.

First, you'll need to initialise your application. Create a folder then initialise it:

```bash
md myapp
cd myapp
npm init
```

You'll be asked a series of questions which creates a `package.json` file, e.g.

```javascript
{
  "name": "myapp",
  "version": "1.0.0",
  "description": "My test app",
  "author": "Craig Buckler",
  "main": "index.js",
  "scripts": {
    "test": "echo \"No tests specified\" && exit 1",
    "start": "node ./index.js",
  },
  "dependencies": {
  }
}
```

You can now install dependencies. For example, the [commander](https://www.npmjs.com/package/commander) module allows you to easily manage command-line interfaces by parsing --passed --arguments. Install it with:

```
npm install commander
```

Your `package.json` file "dependencies" will be updated, e.g.

```javascript
"dependencies": {
  "commander": "^2.15.1"
}
```

and the `commander` code will be installed to a new `node_modules` folder (which should be added to `.gitignore` if using Git).

You can now reference and use commander, e.g.

```javascript
// index.js
const
  package = require('package.json'),
  program = require('commander');

program
  .version(package.version) // obtained from package.json
  .description(package.description) // obtained from package.json
  .option('-n, --name <name>', 'your name (required)')
  .parse(process.argv) // pass arguments

if (program.name) {
  console.log(`Hello ${program.name}!`);
}
```

You can now run this application with `npm start` or `node ./index.js`. If you pass a `--name Rik` parameter, `"Hello Rik!"` is displayed.


## More Node.js
This covers the basics but there's a lot more to learn!

One gotcha: Node.js - like browser JavaScript - is asynchronous. Your application won't end until all callbacks (or Promises) have terminated.
