# JavaScript objects

Object Orientated (or Oriented) Programming (OOP) is shrouded in mystery with confusing terms such as encapsulation, inheritance, and polymorphism. Ignore all that - it's conceptually easier than you think and you've been using them without necessarily realising.

Newer JavaScript developers normally think OOP isn't supported. It is. In fact, almost everything in JavaScript is an object. You can do this in JavaScript:

```javascript
'hello'.length();
```

`'Hello'` is a Sting object so the `length()` method can be run.

Regularly used objects include `Math`, `window`, `document`, node and event objects.


## Simple objects
Simple single-use objects can be created quickly:

```javascript
var youngone = {
  name: 'Neil',
  occupation: 'student',
  style: 'hippy',
  hello: function(intro) {
    console.log(intro + ' ' + this.name + ' you ' + this.style);
  }
};

youngone.hello('go away'); // go away Neil you hippy
```

Some applications may never go much further but you will encounter situations where you need several instances of similar objects...


## OOP basics
Consider any program: a game, a spreadsheet, Facebook, etc.

That program let's you:

1. Set certain options, such as your name, favourite colour, inside leg measurement (i.e. *properties*)
2. Perform certain actions, such as post a message, upload a photo, fire a laser (i.e. *methods*)

As a user, you don't care how any of this is implemented. You just have a public interface which allows you to set inputs, perform actions, and read the output of those actions. The underlying code could change completely and everything would still work.

An object can be thought of as a small self-contained program which has a set of related responsibilities.

### Object example
Consider a game of Space Invaders. Each alien invader has various properties:

* score (for being shot)
* image (the alien design)
* alive (is the alien active or destroyed?)
* position (x,y)
* direction (+n or -n)
* canfire (can this invader fire? Those above another cannot.)

and various methods:

* move (does the next frame of animation)
* checkhit (has the invader been hit)
* fire (randomly fire weapon if possible)

These methods examine and change the properties. For example:

* `move` would change the `position` and the `direction` when it hits the screen edge.
* `checkhit` could change the `alive` state
* `fire` can only trigger if `canfire` is true.

Each invader is functionally identical. Rather than attempting to manage this data in bulk, we create a single `Invader` object template (known as a `class` in most languages). That is used to create as many invaders as we like at the start of each level. Later levels could have more invaders with different designs.

A (partially written) Invader object template in JavaScript:

```javascript
// object constructor
// this is called when we create an object of this type
function Invader(x, y, dir, score, img) {

  // size of invader image
  this.width = 20;
  this.height = 20;

  // gap between
  this.gap = 10;

  // set initial properties
  this.alive = true;
  this.posX = x * (this.width + this.gap);
  this.posY = y * (this.height + this.gap);
  this.dir = dir || 1;
  this.score = score || 10;
  this.canfire = false;

  // show invader on screen
  var sprite = document.createElement('img');
  sprite.src = img || 'invader.png';
  this.element = document.body.appendChild(sprite);

  // move into place
  this.move();

}


// move invader
Invader.prototype.move = function() {

  // invader is dead?
  if (!this.alive) return;

  // calculate new position
  this.posX += this.dir;

  // reached screen limits?
  if (this.posX <= 0 || posX >= 400) {
    this.dir = -this.dir; // change direction
    this.posY += 20;      // move down
  }

  // move position on screen
  this.element.style.left = posX + 'px';
  this.element.style.top = posY + 'px';

}


// check if invader is hit and returns score
// pass a bullet object which must have posX and posY values
Invader.prototype.checkHit = function(bullet) {

  if (bullet.posX >= this.posX && bullet.posX <= this.posX + this.width && bullet.posY >= this.posY && bullet.posY <= this.posY + this.height) {

    // die alien scum!
    this.alive = false;
    document.body.removeChild(this.element);

    return this.score;

  }

  // survived!
  return 0;

}
```

We can now create as many instances of the Invader object as we like...


```javascript
// create 6 x 10 aliens
var alien = [];
for (var i = 59; i >= 0; i--) {

  var
    row = Math.floor(i / 10),
    col = i % 10;

  // note the `new`
  alien[i] = new Invader(col, row, 1, (6 - row) * 10);

}

```

Our main game loop controls invader movement...

```javascript
var
  gameover = false,
  score = 0,
  remainingAliens = alien.length;

while (!gameover) {

  for (var i = alien.length; i >= 0; i--) {

    // move alien
    alien[i].move();

    // detect hit (bullet class not defined yet!)
    if (bullet) {
      var s = alien[i].checkHit(bullet);

      if (s) {

        // update score
        score += s;

        // all aliens defeated!
        remainingAliens--;
        if (!remainingAliens) gameover = true;

      }
    }

  }

}

```

This is not a completed game, but it shows the concept. We would also require objects for:

* the user's base which moves according to input
* the user's current in-flight bullet
* an array of alien in-flight bullets

and possibly other objects to retain high scores, store game settings (lives, speed, difficulty) etc.

In essence, objects are useful when you need *things* of the same type. They allow you to create modular programs with reusable functionality. You can change the internals of that object safely without affecting other parts of the application.


## JavaScript OOP
OOP in JavaScript is different to other languages in that the object template is very flexible. For example, if you create a new prototype method/function, all existing objects of that type can use it (not that I'd recommend it!)

Note there is a `class` command in ES6 which helps those coming from other languages. However, it's just a sprinkling of syntactical sugar - below the surface, it's using prototypes like you see above.


## Further reading

* [Object-oriented JavaScript for beginners](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Objects/Object-oriented_JS)

OOP goes far further than this and you can set private properties, use inheritance (an object definition uses another as a base), polymorphism (override methods such as `toString`) and more.
