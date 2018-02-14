# Timers
Timers are useful for various activities including:

1. Delaying an action.
1. Running an action at pre-defined intervals, e.g. animations.
1. Stopping execution temporarily so other code can run.

**Note!** You cannot rely on timers firing with any accuracy. Setting something to trigger in 1000ms may occur, but the browser could be doing anything at that time and browser execution is single-threaded. It will run as soon as possible after the timeout.


## setTimeout and clearTimeout
Runs an action once:

```javascript
setTimeout(doSomething, 1000);

function doSomething() {
  console.log('I ran after 1 second (1000ms)');
}
```

Timeouts can also be cancelled with `clearTimeout` if you know their *value*:

```javascript
let timer = setTimeout(doSomething, 1000);

setTimeout(function() {
  clearTimeout(timer);
}, 100);

function doSomething() {
  console.log('I will never run');
}
```

It's sometimes necessary to use a timeout in response to an immediate error encountered in Node.js, e.g.

```javascript
asyncFunction(function(err) {
  console.log('Done.\nErrors: ' + err);
}, 0);

function asyncFunction(callback, value) {

  if (value > 0) {
    // do something asynchronous and run callback
  }
  else {

    // throw error later
    setTimeout(function() { callback('invalid parameter',); }, 1);

  }

}

```

If we ran `callback()` immediately on the error, it's no longer an asynchronous process and we could encounter problems.


## setInterval and clearInterval
Runs an action at predefined intervals:

```javascript
setInterval(doSomething, 1000);

let n = 0;
function doSomething() {
  n++;
  console.log('run ' + n);
}
```

Similarly, intervals can be cancelled with `clearInterval`:

```javascript
let timer = setInterval(doSomething, 1000);

let n = 0;
function doSomething() {
  n++;
  console.log('run ' + n);

  // stop!
  if (n >= 10) {
    clearInterval(timer);
  }
}
```


## requestAnimationFrame
Typically used for JS-powered animations, `requestAnimationFrame` calls a function when the browser is free shortly before the next repaint. That function is passed a timestamp.

```javascript
// move an element from 0px to 100px in one second
let
  moveTime = 1000,
  startTime = null,
  box = document.getElementById('box');

requestAnimationFrame(move);

function move(timestamp) {

  startTime = startTime || timestamp;

  let
    progress = Math.min(timestamp - startTime, moveTime),
    t = progress / moveTime;

  // t is a timing value between 0 (start of animation) and 1 (end)
  // it's possible to create easing functions, e.g.
  // t = (t < 0.5 ? 8*t*t*t*t : 1-8*(--t)*t*t*t);

  box.style.left = t * 100 + 'px';
  if (t < 1) requestAnimationFrame(move);
}

```

`cancelAnimationFrame()` is also available. It's rarely required since you can simply not run `requestAnimationFrame` at the end of the animation. *(That said, I've recently seen it used to cancel a game loop.)*


## Exercises
Create a real-time clock which continues to show the current hour, minute and second.
