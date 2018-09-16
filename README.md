# Advanced JavaScript notes

General concepts:

**Learn HTML and CSS first.**

1. JavaScript is the most fragile front-end technology.
1. Only use JavaScript when there is no other alternative.
1. Use JavaScript to progressively enhance functionality.

Form fields and validation should be HTML.

Hover effects, basic animations, scroll-snapping, etc. are better handled in CSS.

ES6 will be shown, but warnings will be given with ES5 alternatives where necessary. Code converters such as Babel will not be used.

MDN is your friend!


## `'use strict';`
Use it at the top of a function - it switches JS into strict mode and provides improved debugging.


## Code consoles
Web development consoles with collaborative features.

For performance, it is often best to switch off live updates.

### [jsfiddle.net](https://jsfiddle.net/)
Can add Firebug Lite as an external resource (top left):

```
https://getfirebug.com/firebug-lite.js#startOpened=true
```

Notes:

* No code validation.


### [liveweave.com](https://liveweave.com/)
Can add Firebug Lite to HTML `<head>`:

```html
<script src="https://getfirebug.com/firebug-lite.js#startOpened=true"></script>
```

Notes:

* Firebug Lite only works in Firefox, Edge and IE (not Chrome-based browsers).
* Login does not appear to work?
* JavaScript validation does not include ES6 syntax.
* If initial "Hello Weaver" appears on a saved page, try refreshing.

### [Plunker](http://plnkr.co/)
Can add Firebug Lite to HTML as above.


### [ideone](https://ideone.com/)
Online compiler and debugging tool. No collaboration, but can be used for Node.js.


## Topics covered

* ✅ value and reference
* ✅ module pattern
* ✅ DOM selection
* ✅ DOM manipulation
* ✅ closures
* ✅ events
* ✅ recursion (see recursion)
* ✅ browser debugging
* ✅ form handling basics and validation API
* ✅ date and time
* ✅ timers and JS animation
* ✅ windows and pop-ups
* ✅ Object Oriented Programming
* ✅ ES6 classes
* ☐ Node.js
* ☐ callbacks to promises to async/await
* ☐ web workers
* ☐ PWAs (service workers)


## Topics to consider

* types
* arrays
* strings
* variables and scope
* loops
* functions and code blocks
* bitwise operators
* regular expressions
* persistence and storage
* callbacks, Promises, async/await
* Ajax
* DOM mutation
* Node.js
* HTML5 APIs: audio, video, geolocation, files, web workers
* Progressive Web Apps
* canvas and SVG


Create simple project based on requirements.
