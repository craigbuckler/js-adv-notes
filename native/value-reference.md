# Function parameters

**Passing by value:** a *copy* of a value is passed to a function. It can be manipulated in any way without affecting the original.

```javascript

function inc(arg) {
  arg++;
  return arg;
}

let a = 1;
inc(a);
console.log(a); // a is 1

a = inc(a); // a is 2
```


**Passing by reference:** the actual value is passed to a function. If it's changed, the original value is changed.

```javascript
function inc(arg) {
  arg.counter++;
}

let a = {
  counter: 1
};

inc(a);
console.log(a.counter); // a.counter is 2
```


**IMPORTANT!** When passing arguments to JavaScript functions:

1. Native values such as strings, numbers, booleans, etc. are passed by VALUE.
1. Objects and arrays are passed by REFERENCE.
