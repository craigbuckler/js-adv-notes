# JavaScript closures
Closures are used throughout JavaScript development often without realising!

> A **scope chain** is created when you declare a function within another function. The parent function cannot access variables in the child, *but* the child function **can** access variables in the parent.

Closures are used extensively when handling events, timers, prototypal inheritance, etc. The [module pattern](../module/module.md) is one big closure!

See https://jsfiddle.net/craigbuckler/g2e7jmdL/


```javascript
// outer parent function
function parent() {

  var h = 'hello';

  child(); // call child function

  // inner child function
  function child() {

    var w = 'world';

    // h can be accessed
    console.log(h + ' ' + w);

  }

  // raises an error
  console.log(w);

}

// call parent
parent();
```


## Timer example
A timer example:

```javascript
// timer example
function startTimer(msg, delay) {

  console.log('startTimer start');

  setTimeout(function() {
    console.log('startTimer: ' + msg);
  }, delay || 1000)

  console.log('startTimer end');

}

startTimer('hello again', 2000);
```

In this example, the `startTimer` function begins and ends. Two seconds later, the `msg` sent is output even though `startTimer` has ended.

The `setTimeout` defines an inner anonymous function which has access to the parent's outer variables even when the parent has ended.


## Prototypal inheritance example
JavaScript uses prototypes rather than template based classes (although those are *emulated* in ES6):


```javascript
// prototypal inheritance example
function Animal(type, noise) {

  this.type = type;
  this.noise = noise;

}

// speak after a delay
Animal.prototype.speak = function(delay) {

  // default delay - could use function(delay = 1000) in ES6
  delay = delay || 1000;

  console.log('A ' + this.type + ' is about to speak...');

  var T = this; // closure variable

  setTimeout(function() {

    // we cannot use this.noise here!
    // the value of this becomes the window object
    console.log(T.noise + ' from closure');

  }, delay);

};

var
  animal1 = new Animal('dog', 'woof'),
  animal2 = new Animal('cat', 'miaow');

animal1.speak(3000);
animal2.speak(3500);
```

`Animal` is an object with the properties `type` and `noise`.

`Animal.prototype.speak` defines a new method for `Animal` objects which outputs the `noise` property after a short delay. However, we cannot use `this.noise` within the `setTimeout` because the value of `this` will change to the context of the calling code (typically the `window` object).

We therefore use a closure `var T = this;` to ensure the correct reference to the outer object is available.


## Closure alternatives
The ES5 [bind prototype](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/bind) creates a new function from an existing function and explicitly sets the value of `this`, e.g.

```javascript
setTimeout(function() {
  console.log(this.noise + ' from bind');
}.bind(this), delay);
```

ES6 fat arrow functions are bound to the current `this` by default:

```javascript
setTimeout(() => {
  console.log(this.noise + ' from ES6 fat arrow function');
}, delay);
```

The context of `this` is complex! Further reading...

* [The Simple Rules to ‘this’ in Javascript](https://codeburst.io/the-simple-rules-to-this-in-javascript-35d97f31bde3)
* [Gentle explanation of 'this' keyword in JavaScript](https://dmitripavlutin.com/gentle-explanation-of-this-in-javascript/)
