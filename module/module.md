# JavaScript Modules

Modules: a way to include functionality declared in one file within another. Typically, a developer creates an encapsulated library of code responsible for handling related tasks. That library can be referenced by applications or other modules.


## Multiple HTML `<script>` tags (client-side only)

HTML can load any number JavaScript files using multiple `<script>` tags:

```html
<script src="lib1.js"></script>
<script src="lib2.js"></script>
<script src="core.js"></script>
<script>
console.log('inline code');
</script>
```

Problems:

1. Performance - multiple HTTP requests and process halting (loading and parsing JS stops all other browser activities. Browsers are single-threaded.)
1. Dependency management is manual.
1. Functions can override others.


## Module Loaders
Systems such as [RequireJS](http://requirejs.org/) and [SystemJS](https://github.com/systemjs/systemjs) provide a library for loading and namespacing other JavaScript libraries at runtime. Modules are loaded using Ajax methods when required. The systems help, but could become complicated for larger code bases or sites adding standard `<script>` tags into the mix.


## Script concatenation
Concatenate all JavaScript files into a single, large file. This solves some performance and dependency management issues, but usually incurs a manual build step.


## Module Bundlers, Preprocessors and Transpilers
Build compile step.

1. Automated concatenation.
1. Further processing, e.g. lint, remove debugging commands, minify.
1. Alternative syntaxes such as TypeScript or CoffeeScript.


## Revealing module pattern
**The problem:** variables and functions defined outside of a function are assigned to the browser's global `window` object (or `global` in Node.js).

It makes it too easy for other code in libraries or frameworks to conflict with or override another.


```js
/*
a single value - gameScore - is set using an Immediately Invoked Function Expression (IIFE) which returns a list of public methods

All variables and functions defined in gameScore are private unless revealed in the return statement
*/
var gameScore = (function() {

  'use strict';

  // private configuration
  var
    score = 0,

    // score for hitting an invader at a level
    invaderScore = [
      10, 20, 40, 50, 100
    ];

  // ensure a level is valid
  function validLevel(level) {
    return Math.max(0, Math.min(level || 0, invaderScore.length - 1));
  }

  // invader is hit
  function hitInvader(level) {
    score += invaderScore[ validLevel(level) ];
  }

  // show score
  function show() {
    console.log(score);
  }

  // return list of public methods
  return {
    hitInvader: hitInvader,
    show: show
  };

}());


// main code
gameScore.hitInvader(0);
gameScore.hitInvader(1);
gameScore.hitInvader(15);

gameScore.show();

// fails - score is private
console.log(gameScore.score);
```

## CommonJS modules (Node.js)
Exports one or functions. It is still possible to use the revealing module pattern:

```js
// mathlib.js
module.exports = (function() {

  'use strict';

  function add(a, b) {
    return a + b;
  }

  function multiply(a, b) {
    return a * b;
  }

  return {
    add: add,
    multiply: multiply
  }

})();
```

ES6 alternative syntax:

```js
// mathlib.js
module.exports = (() => {

  'use strict';

  return {
    add: (a, b) => a + b,
    multiply: (a, b) => a * b
  }

})();
```

To use this module:

```js
const mathlib = require('mathlib.js');

console.log( mathlib.add(2,3) ); // 5
console.log( mathlib.multiply(2,3) ); // 6
```


## ES6 modules
Works in Node.js or client-side in Chrome 63+, Safari 11+, Edge 16+ and Firefox 60+.


```js
// mymodule.mjs
export function add(a, b) { return a + b; }
export function multiply(a, b) { return a * b; }
```


Client side:

```html
<script type="module" src="./mymodule.mjs"></script>
```

or inline client-side:

```html
<script type="module">

  import * as mathlib from './mymodule.mjs';

  console.log( mathlib.add(2,3) ); // 5
  console.log( mathlib.multiply(2,3) ); // 6

</script>
```

or in Node.js:

```js
import * as mathlib from './mymodule.mjs';

console.log( mathlib.add(2,3) ); // 5
console.log( mathlib.multiply(2,3) ); // 6
```

Alternative `import`s:

```js
// import one function
import { add } from './mymodule.mjs';
console.log( add(2,3) ); // 5

// import a few
import { add, multiply } from './mymodule.mjs';
console.log( add(2,3) ); // 5
console.log( multiply(2,3) ); // 5

// import and rename
import { add as addAll } from './mymodule.mjs';
console.log( addAll(2,3) ); // 5
```


Modules are parsed once, regardless of how many times they're referenced in the page or other modules.

Notes:

* `'use strict'` is enabled by default
* Client-side modules defer by default - they run once the page has loaded.
* Modules must be served with the MIME type `application/javascript`. If using in Node.js, modules use the `.mjs` extension - not `.js`.
* Regular `<script>` tags can fetch scripts on other domains but modules are fetched using cross-origin resource sharing (CORS).


## ES6 vs CommonJS modules
Remember:

* ES6 modules are pre-parsed in order to resolve further imports before code is executed.
* CommonJS modules load dependencies on demand while executing the code.

```js
// ES6 modules

// ---------------------------------
// one.js
console.log('running one.js');
import { hello } from './two.js';
console.log(hello);

// ---------------------------------
// two.js
console.log('running two.js');
export const hello = 'Hello from two.js';
```

ES6 output:

```
running two.js
running one.js
hello from two.js
```

```js
// CommonJS modules

// ---------------------------------
// one.js
console.log('running one.js');
const hello = require('./two.js');
console.log(hello);

// ---------------------------------
// two.js
console.log('running two.js');
module.exports = 'Hello from two.js';
```

CommonJS output:

```
running one.js
running two.js
hello from two.js
```

## Tips
Browser support is growing, but it's a little premature to switch to ES6 modules. For the moment, use a module bundler to create a script that works everywhere.

ES6 modules are supported in Node.js v10. Converting an existing project is unlikely to result in any benefit, and would render an application incompatible with earlier versions of Node.js.
