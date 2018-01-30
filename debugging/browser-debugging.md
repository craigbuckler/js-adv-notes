# Browser debugging

All browsers provide JS debugging facilities.


## Simple debugging
Use `console.log(value [,value...])` to output values as the code runs.

Other options:

* `console.clear()` - clears the log
* `console.error()` - error message
* `console.info()` - information message
* `console.table()` - shows an object or array as a table
* `console.time()` - starts a timer
* `console.timeEnd()` - stops a timer
* `console.count()` - counts the number of times encountered
* `console.trace()` - outputs a stack trace (the names of all calling functions)
* `console.assert()` - tests whether a condition is true
* `console.group()` - creates a nested group within the console
* `console.groupEnd()` - exit the nested group
* `console.groupCollapsed()` - creates an inline group

See [MDN Console](https://developer.mozilla.org/en-US/docs/Web/API/Console).


## Debuggers and breakpoints
Access:

* in Chrome: DevTools > Sources > `js` folder > script
* in Firefox: DevTools > Debugger > `js` folder > script
* in Edge: DevTools > Debugger > site name folder > `js` folder > script

The `{}` icon will de-minify any code.

Breakpoints halt code execution so you can inspect it. To set a breakpoint:

1. Click any line number (triggers before that code is reached).
1. Insert a `debugger;` statement in your code.
1. Add a watch expression or other options provided by the browser.

Breakpoints normally remain between refreshes although you might need to adjust them if code changes.

When a breakpoint is encountered, you can:

1. Examine local and global variables (in window or console).
1. Examine and click the call stack (the function-call hierarchy).

Browsers then provide several buttons:

* `resume` - continues execution (until the next breakpoint).
* `step over` - run the next command, but do NOT follow it into other functions
* `step into` - run the next command and follow it to other functions
* `step out` - complete the current function and return back to the caller.
