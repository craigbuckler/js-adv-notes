# ES6 classes

Developers from other languages such as C, C++, C#, Java and PHP are often confused by JavaScripts prototypal inheritance. They are used to a classical model where a `class` template is defined up-front and all objects are instances of that class.

ES6 introduces classes but they are syntactical sugar - below the surface, JavaScript still uses the same prototypal models. In addition, they are more limited, e.g. you cannot have private members (yet).

ES6 classes are supported in Node.js and in all modern browsers but not IE.


## `class` definition
A simple class which defines an animal:

```javascript
// it's not enforced, but is useful to name classes with an initial capital
// all classes 'use strict' by default
class Animal {

  // this function is run immediately when an object is created
  // it usually sets default properties
  constructor(name = 'anonymous', legs = 4, noise = 'silent') {

    // all these properties are public
    this.type = 'animal';
    this.name = name;
    this.legs = legs;
    this.noise = noise;

  }

  // set a value
  set typeOf(type) {
    this.type = type.toLowerCase();
    if (this.type === 'human') this.legs = 2;
  }

  // get a value (usually calculated)
  get typeOf() {
    return this.name + ' (' + this.type + ')';
  }

  // runs a method (does something)
  speak() {
    console.log(this.name, 'says', '"' + this.noise + '"');
  }

  // runs a method (does something)
  walk() {
    console.log(this.name, 'walks on', this.legs, 'legs');
  }

}
```

This introduces several concepts:

1. The `class` template.
1. A constructor. This is run when we create an object using the class.
1. Properties (such as `this.name`). All properties can be accessed from outside the class (they are public).
1. Setters. Similar to setting a single property but can do more!
1. Getters. Similar to getting a single property but can do more!
1. Methods. Functions which perform actions.


We can now create an object from this class:

```javascript
let rik = new Animal('Rik', 2, 'poetry');
rik.typeOf = 'human';
console.log( rik.typeOf );  // Rik (human)
rik.speak();                // Rik says "poetry"
rik.walk();                 // Rik walks on 2 legs
```

And another. All objects are of the same type.

```javascript
let neil = new Animal('Neil', 2, 'wow man, heavy');
neil.typeOf = 'human';
console.log( neil.typeOf ); // Neil (human)
neil.speak();               // Neil says "wow man, heavy"
neil.walk();                // Neil walks on 2 legs
```


## Child/sub classes
It's sometimes useful to create classes from another. (In some languages, there's a concept of `abstract` classes which must be used to create new child classes and cannot be invoked on directly).

Our `Animal` class is a little too generic. Every time we make a new human, we must set the `type` or that person is presumed to be an animal. We could therefore create a new `Human` class which uses `Animal` as a base:

```javascript
// child class
// makes a Human using Animal as a base
class Human extends Animal {

  constructor(name) {

    // calls the Animal constructor
    super(name, 2, 'nothing of interest');
    this.type = 'human';
    Human.counter++;

  }

  // override speak method
  speak() {

    // but call Animal.speak
    super.speak();
    console.log('to anyone listening');

  }

  // static methods can be called from the class directly
  static count() {
    return Human.counter;
  }

}

// static counter (property of the class)
Human.counter = 0;
```

New concepts:

1. The `extends` keyword.
1. The `super` keyword which references the parent class.
1. Overriding. We define a new `speak()` method (although it also calls `super.speak()`).
1. `static` methods. These can be called without creating an object first.
1. `static` properties. These are defined on the class itself (since everything is an object in JS).


We can now create new `Human` objects in a simpler way. Properties like `.legs` are presumed to be 2 unless we change it.

```javascript
console.log( Human.count() ); // 0

let rik = new Human('Rik');
rik.noise = 'poetry';
console.log( rik.typeOf );  // Rik (human)
rik.speak();                // Rik says "poetry" to anyone listening
rik.walk();                 // Rik walks on 2 legs

console.log( Human.count() ); // 1

let neil = new Human('Neil');
console.log( neil.typeOf ); // Neil (human)
neil.speak();               // Neil says "nothing of interest" to anyone listening
neil.walk();                // Neil walks on 2 legs

console.log( Human.count() ); // 2
```

Notice we can run the `static Human.count()` without defining a `new Human` object first. It keeps a running total of the number of humans we have created, however, there's no concept of static properties in JavaScript so it must update a global `countHuman` variable to retain the count.


## Further reading
ES6 classes are being updated in newer editions of JavaScript - watch out for some SitePoint articles soon!
