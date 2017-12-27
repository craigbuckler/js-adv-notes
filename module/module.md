# JavaScript Modules

See: https://jsfiddle.net/craigbuckler/te9zkeu8/

**The problem**: variables and functions defined outside of a function are assigned to the browser's global `window` object (or `global` in Node.js).

It makes it too easy for other code in libraries or frameworks to conflict with or override another.

Node.js supports CommonJS:

```javascript
let myModule = require('/modules/my-module.js');
myModule.runFunction();

```

Solved in ES6 using the `import` statement:

https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import


```javascript
import * as myModule from '/modules/my-module.js';
myModule.runFunction();

```

Neither option is natively supported in Chrome 60, Firefox 53, Edge 15, Safari 9.x, IE11 or below.


## Revealing module pattern

https://jsfiddle.net/craigbuckler/hmmLy5ep/


```javascript
/*
single value, lib, set using an Immediately Invoked Function Expression (IIFE)
which returns a list of public methods

All variables and functions defined in lib are private unless revealed in the return statement
*/
var lib = (function() {

  // defaults
  var defaultColor = '#f00';

  // color tags
  function colorTags(tagset, color) {

    color = color || defaultColour;

    for (var i = tagset.length - 1; i >= 0; i--) {
      tagset[i].style.color = color;
    }

  }

  // get array of colors
  function getColors(tagset) {

    var color = [];
    for (var i = tagset.length - 1; i >= 0; i--) {
      color.push( tagset[i].style.color );
    }

    return color;

  }

  // return list of public methods
  return {
    colorTags: colorTags,
    getColors: getColors
  };

}());


// main code
var tag = document.getElementsByTagName('p');

console.log( lib.getColors(tag) );
lib.colorTags(tag, '#f00');
console.log( lib.getColors(tag) );
```
