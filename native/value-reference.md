# Primitive types

string, number, boolean, null, undefined, symbol (new in ES6)
All primitives are immutable (cannot change), but the variable assigned a primitive can be changed.


## Arrays
An ordered collection of data.
Arrays have a zero index and can hold any value.


```
var myArrayA = new Array();
myArray[0] = 1;

var myArrayB = [1, 'two', 3];

myArrayB[0]; // 1
```

Array items can be removed with `delete` but this sets their value to `undefined` - the array length is not changed.


## Objects
Objects in JS use a prototypal inheritance model - there is no need to define a `class` (template). Most often, objects are defined on the fly:

```
var objA = {
  name1: 'value',
  name2: 2
};
```

Further object tutorials are coming!


## Comparisons

`A === B` type and value must match, e.g. `1 === '1'` is false
`A == B` values must match, e.g. `1 == '1'` is true


## Pure/immutable functions
A pure function is one that, given the same parameters, will always return the same result:

1. It does not change the input parameters.
1. It is not affected by external factors.

This permits reliable testing and can make code easier to understand. That said, it's not always possible or practical!


## Functions are values in JavaScript
Example:

```
function op(fn, a, b) {
  return op(a,b);
}

var f = function add(a,b) { return a + b; };
op(f,1,2); // 3

```

*(Could also be done inline. ES6 syntax is even simpler.)*

Be wary of *hoisting*.


## Function parameters

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


## Exercises
1. Given the array [1,2,3,4,5], remove the item at index 2 and ensure the array length reduces to four items.
1. Write a pure function that accepts an array and a value. It removes all array items which match that value and returns a new array.
1. Research what is meant by *truthy* and *falsy* values. Write a function which always returns `true` when two parameters match (they will be single values - not arrays or objects).
