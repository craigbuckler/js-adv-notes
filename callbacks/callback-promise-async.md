# Callbacks to Promises to Async/Await

JavaScript is regularly claimed to be *asynchronous*. What does that mean? How does it affect development? How has the approach changed in recent years?

Consider the following code:

```
result1 = doSomething1();
result2 = doSomething2(result1);
```

Most languages process each line *synchronously*. The first line runs and returns a result. The second line runs once the first has finished *regardless of how long it takes*.


## Single-Thread Processing
JavaScript runs on a single processing thread. When executing in a browser tab, everything else stops. This is necessary because changes to the page DOM cannot occur on parallel threads; it would be dangerous to have one thread redirecting to a different URL while another attempts to append child nodes.

This is rarely evident to the user because processing occurs quickly in small chunks. For example, JavaScript detects a button click, runs a calculation, and updates the DOM. Once complete, the browser is free to process the next item on the queue.

*(Side note: other languages such as PHP also use a single thread but may be managed by by a multi-threaded server such as Apache. Two requests to the same PHP page at the same time can initiate two threads running isolated instances of the PHP runtime.)*


## Going Asynchronous with Callbacks
Single threads raise a problem. What happens when JavaScript calls a "slow" process such as an Ajax request in the browser or a database operation on the server? That operation could take several seconds - *even minutes*. A browser would become locked while it waited for a response. On the server, a Node.js application would not be able to process further user requests.

The solution is asynchronous processing. Rather than wait for completion, a process is told to call another function when the result is ready. This is known as a *callback* and it's passed as an argument to any asynchronous function. For example:

```javascript
doSomethingAsync(callback1);
console.log('finished');

// call when doSomethingAsync completes
function callback1(error) {
  if (!error) console.log('doSomethingAsync complete');
}
```

`doSomethingAsync()` accepts a callback function as a parameter (only a reference to that function is passed so there's little overhead). It doesn't matter how long `doSomethingAsync()` takes; all we know is that `callback1()` will be executed at some point in the future. The console will show:

```
finished
doSomethingAsync complete
```


### Callback Hell
Often, a callback is only ever called by one asynchronous function. It is therefore possible to use concise anonymous inline functions:

```javascript
doSomethingAsync(error => {
  if (!error) console.log('doSomethingAsync complete');
});
```

A series of two or more asynchronous calls can be completed in series by nesting callback functions, e.g.

```javascript
async1((err, res) => {
  if (!err) async2(res, (err, res) => {
    if (!err) async3(res, (err, res) => {
      console.log('async1, async2, async3 complete.');
    });
  });
});
```

Unfortunately, this introduces *callback hell*; a notorious concept which even [has its own webpage](http://callbackhell.com/)! The code is difficult to read and will become worse when error-handling logic is added.

Callback hell is relatively rare in client-side coding. It can go two or three levels deep if you're making an Ajax call, updating the DOM and waiting for an animation to complete but it normally remains manageable.

The situation is different on OS or server processes. An Node.js API call could receive file uploads, update multiple database tables, write to logs, and make further API calls before a response can be sent.


## Promises
[ES2015 (ES6) introduced Promises](https://www.sitepoint.com/overview-javascript-promises/). Callbacks are still used below the surface but Promises provide a clearer syntax which *chains* asynchronous commands so they run in series (more about that in the [next section](#asynchronouschaining)).

To enable Promise-based execution, asynchronous callback-based functions must be changed so they immediately return a Promise object. That object *promises* to run one of two functions (passed as arguments) at some point in the future:

* **`resolve`**: a callback function run when processing successfully completes, and
* **`reject`**: an optional callback function run when a failure occurs.

In the example below, a database API provides a `connect()` method which accepts a callback function. The outer `asyncDBconnect()` function immediately returns a new Promise and runs either `resolve()` or `reject()` once a connection is established or fails:

```javascript
const db = require('database');

// connect to database
function asyncDBconnect(param) {

  return new Promise((resolve, reject) => {

    db.connect(param, (err, connection) => {
      if (err) reject(err);
      else resolve(connection);
    });

  });

}
```

Node.js 8.0+ provides a [`util.promisify()` utility](https://nodejs.org/api/util.html#util_util_promisify_original) to convert a callback-based function into a Promised-based alternative. There are a couple of conditions:

1. the callback must be passed as the last parameter to an asynchronous function, and
2. the callback function must expect an error followed by a value parameter.

Example:

```javascript
// Node.js: promisify fs.readFile
const
  util = require('util'),
  fs = require('fs'),
  readFileAsync = util.promisify(fs.readFile);

readFileAsync('file.txt');
```

Various client-side libraries also provide promisify options but you can create one yourself in a few lines:

```javascript
// promisify a callback function passed as the last parameter
// the callback function must accept (err, data) parameters
function promisify(fn) {
  return function() {
  	return new Promise(
    	(resolve, reject) => fn(
      	...Array.from(arguments),
        (err, data) => err ? reject(err) : resolve(data)
      )
    );
  }
}

// example
function wait(time, callback) {
  setTimeout(() => { callback(null, 'done'); }, time);
}

const asyncWait = promisify(wait);

ayscWait(1000);
```


### Asynchronous Chaining
Anything which returns a Promise can start a series of asynchronous function calls defined in `.then()` methods. Each is passed the result from the previous `resolve`:

```javascript
asyncDBconnect('http://localhost:1234')
  .then(asyncGetSession)      // passed result of asyncDBconnect
  .then(asyncGetUser)         // passed result of asyncGetSession
  .then(asyncLogAccess)       // passed result of asyncGetUser
  .then(result => {           // non-asynchronous function
    console.log('complete');  //   (passed result of asyncLogAccess)
    return result;            //   (result passed to next .then())
  })
  .catch(err => {             // called on any reject
    console.log('error', err);
  });
```

Synchronous functions can also be executed in `.then()` blocks. The returned value is passed to the next `.then()` (if any).

The `.catch()`  method defines a function which is called when any previous `reject` is fired. At that point, no further `.then()` methods will be run. You can have multiple `.catch()` methods throughout the chain to capture different errors.

ES2018 introduces a `.finally()` method which runs any final logic regardless of the outcome, e.g. to clean-up, close a database connection etc. It is currently supported in Chrome and Firefox only, but Technical Committee 39 has released a [.finally() polyfill](https://github.com/tc39/proposal-promise-finally/blob/fd934c0b42d59bf8d9446e737ba14d50a9067216/polyfill.js).

```javascript
function doSomething() {
  doSomething1()
  .then(doSomething2)
  .then(doSomething3)
  .catch(err => {
    console.log(err);
  })
  .finally(() => {
    // tidy-up here!
  });
}
```

### Multiple Asynchronous Calls with Promise.all()
Promise `.then()` methods run asynchronous functions one after the other. If the order does not matter, e.g. initialising unrelated components, it is faster to launch all asynchronous functions at the same time and finish when the last (slowest) function runs `resolve`.

This can be achieved with `Promise.all()`. It accepts an array of functions and returns another Promise, e.g.

```javascript
Promise.all([ async1, async2, async3 ])
  .then(values => {           // array of resolved values
    console.log(values);      // (in same order as function array)
    return values;
  })
  .catch(err => {             // called on any reject
    console.log('error', err);
  });
```

`Promise.all()` terminates immediately if any one of the asynchronous functions calls `reject`.


### Multiple Asynchronous Calls with Promise.race()
`Promise.race()` is similar to `Promise.all()` except that it will resolve or reject as soon as the **first** Promise resolves or rejects. Only the fastest Promise-based asynchronous function will ever complete:

```javascript
Promise.race([ async1, async2, async3 ])
  .then(value => {            // single value
    console.log(value);
    return value;
  })
  .catch(err => {             // called on any reject
    console.log('error', err);
  });
```


### A Promising Future?
Promises reduce callback hell but introduce their own problems.

Tutorials often fail to mention that **the whole Promise chain is asynchronous**. Any function using a series of promises should either return its own Promise or run callback functions in the final `.then()`, `.catch()` or `.finally()` methods.

I also have a confession: *Promises confused me for a long time*. The syntax often seems more complicated than callbacks, there's a lot to get wrong and debugging can be problematic. However, it's essential to learn the basics.

Further Promise resources:

* [MDN Promise documentation](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
* [JavaScript Promises: an Introduction](https://developers.google.com/web/fundamentals/primers/promises)
* [JavaScript Promises ... In Wicked Detail](http://www.mattgreer.org/articles/promises-in-wicked-detail/)
* [Promises for asynchronous programming](http://exploringjs.com/es6/ch_promises.html)


## Async/Await
Promises can be daunting so [ES2017](https://www.sitepoint.com/LINK-to-ES2017-article) introduced `async` and `await`. While it may only be syntactical sugar, it makes Promises far sweeter and you can avoid `.then()` chains altogether. Consider the Promise-based example below:

```javascript
function connect() {

  return new Promise((resolve, reject) => {

    asyncDBconnect('http://localhost:1234')
      .then(asyncGetSession)
      .then(asyncGetUser)
      .then(asyncLogAccess)
      .then(result => resolve(result))
      .catch(err => reject(err))

  });
}

// run connect (self-executing function)
(() => {
  connect();
    .then(result => console.log(result))
    .catch(err => console.log(err))
})();

```

To rewrite this using `async`/`await`:

1. the outer function must be preceded by an `async` statement, and
2. calls to asynchronous Promise-based functions must be preceded by `await` to ensure processing completes before the next command executes.

```javascript
async function connect() {

  try {
    const
      connection = await asyncDBconnect('http://localhost:1234'),
      session = await asyncGetSession(connection),
      user = await asyncGetUser(session),
      log = await asyncLogAccess(user);

    return log;
  }
  catch (e) {
    console.log('error', err);
    return null;
  }

}

// run connect (self-executing async function)
(async () => { await connect(); })();
```

`await` effectively makes each call appear as though it is synchronous while not holding up JavaScript's single processing thread. In addition, `async` functions always return a Promise so they, in turn, can be called by other `async` functions.

`async`/`await` code may not be shorter but there are considerable benefits:

1. The syntax is cleaner. There are fewer brackets and less to get wrong.
2. Debugging is easier. Breakpoints can be set on any `await` statement.
3. Error handling is better. `try`/`catch` blocks can be used in the same way as synchronous code.
4. Support is good. It's implemented in all browsers (except IE and Opera Mini) and Node 7.6+.

That said, not all is perfect...


### Promises, Promises
`async`/`await` still relies on Promises which ultimately rely on callbacks. You will need to understand how Promises work and there's no direct equivalent of `Promise.all()` and `Promise.race()`. It's easy to forget about `Promise.all()` which is more efficient than using a series of unrelated `await` commands.


### Asynchronous Awaits in Synchronous Loops
At some point you'll try calling an asynchronous function *inside* a synchronous loop, e.g.

```javascript
async function process(array) {
  for (let i of array) {
    await doSomething(i);
  }
}
```

It won't work. Neither will:

```javascript
async function process(array) {
  array.forEach(async i => {
    await doSomething(i);
  });
}
```

The loops themselves remain synchronous and will always complete before their inner asynchronous operations.

ES2018 introduces asynchronous iterators which are just like regular iterators except the `next()` method returns a Promise. Therefore, the `await` keyword can be used with `for ... of` loops to run asynchronous operations in series, e.g.

```javascript
async function process(array) {
  for await (let i of array) {
    doSomething(i);
  }
}
```

However, until asynchronous iterators are implemented, it's possibly best to `map` array items to an `async` function and run them with `Promise.all()`, e.g.

```javascript
const
  todo = ['a', 'b', 'c'],
  alltodo = todo.map(async (v, i) => {
    console.log('iteration', i);
    await processSomething(v);
});

await Promise.all(alltodo);
```

This has the benefit of running tasks in parallel, but it is not possible to pass the result of one iteration to another and mapping large arrays could be computationally expensive.


### try/catch Ugliness
`async` functions will silently exit if you omit a `try`/`catch` around any `await` which fails. If you have a long set of asynchronous `await` commands, you may need multiple `try`/`catch` blocks.

One alternative is a higher-order function which catches errors so `try`/`catch` blocks become unnecessary (thanks to [@wesbos](https://twitter.com/wesbos/status/911309291545559041) for the suggestion):

```javascript
async function connect() {

  const
    connection = await asyncDBconnect('http://localhost:1234'),
    session = await asyncGetSession(connection),
    user = await asyncGetUser(session),
    log = await asyncLogAccess(user);

  return true;
}

// higher-order function to catch errors
function catchErrors(fn) {
  return function (...args) {
    return fn(...args).catch(err => {
      console.log('ERROR', err);
    });
  }
}

(async () => {
  await catchErrors(connect)();
})();
```

However, this option may not be practical in situations where an application must react to some errors in a different way to others.

Despite some pitfalls, `async`/`await` is an elegant addition to JavaScript. Further resources:

* MDN [async](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) and [await](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/await)
* [Async functions - making promises friendly](https://developers.google.com/web/fundamentals/primers/async-functions)
* [TC39 Async Functions specification](https://tc39.github.io/ecmascript-asyncawait/)
* [Simplifying Asynchronous Coding with Async Functions](https://www.sitepoint.com/simplifying-asynchronous-coding-async-functions/)



## JavaScript Journey
Asynchronous programming is a challenge which is impossible avoid in JavaScript. Callbacks are essential in most applications but its easy to become entangled in deeply-nested functions.

Promises abstract callbacks but there are many syntactical traps. Converting existing functions can be a chore and `.then()` chains still look messy.

Fortunately, `async`/`await` delivers clarity. Code looks synchronous but it cannot monopolize the single processing thread. It will change the way you write JavaScript and could even make you appreciate Promises (if you didn't before!)
