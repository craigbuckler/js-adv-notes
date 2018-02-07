# Events
Events can be attached to any DOM element, the `document` and `window` objects. The most common [event types](https://developer.mozilla.org/en-US/docs/Web/Events):

* `click`
* `change` - a form field has changed
* `submit` - form submitted
* `focus` - an element receives focus (does not bubble)
* `blur` - an element has lost focus (does not bubble)
* `resize` - be careful in oldIEs!
* `scroll` - be careful in oldIEs!
* `load` - typically used for the `window`, images and Ajax-like requests
* `beforeunload`
* `unload`

Mouse events:

* `mouseenter`
* `mouseover`
* `mousemove`
* `mouseleave`
* `mouseout`
* `mousedown` - mouse button gone down
* `mouseup` - mouse button released
* `dblckick` - mouse button double-click
* `select`

Touch events (multi-touch is supported):

* `touchstart`
* `touchenter`
* `touchleave`
* `touchmove`
* `touchcancel`
* `touchend`

Pointer events (no support in Safari yet):

* `pointermove`
* `pointerover`
* `pointerenter`
* `pointerdown`
* `pointerup`
* `pointerout`
* `pointercancel`
* `pointerleave`

Keyboard events:

* `keydown` - key has gone down
* `keypress` - fired continuously
* `keyup` - key released

Drag and drop events (awful):

* `dragstart`
* `drag`
* `dragenter`
* `dragleave`
* `dragover`
* `dragend`
* `drop`

CSS animation and transition events:

* `animationstart`, `animationend`, `animationiteration`
* `transitionstart`, `transitioncancel`, `transitionend`, `transitionrun`

Media events:

* `canplay`, `play`, `playing`, `ended`, `pause`, `complete`

and many, many more.

You may need to attach more than one event type to do the same thing. For example, a gallery carousel may move to the next/previous image if you click a button, swipe, hit a cursor key, etc.

Example: https://jsfiddle.net/craigbuckler/2hqLqgok/


## Inline HTML event handler
**Don't use this!...**

```html
<p onclick="console.log('click1')">click 1 (inline)</p>
```

Clunky to code, intermingles HTML and JavaScript and impossible to add further events.


## DOM0 event handler
**Don't use this!...**

```javascript
document.getElementById('click2').onclick = function(e) {
  console.log('click2');
};
```

Very old browser support but is impossible to add further events.


## DOM2 event handler
**Use this!**

```javascript
document.getElementById('click3').addEventListener('click', handler3, false);

// event handler
function handler3(e) {
  console.log('click3');
}

```

Any number of events can be attached to the same element.

This doesn't work in IE8 and below but who cares! OldIE fallback using `attachEvent`...

```javascript
if (element.addEventListener) {
  element.addEventListener('click', doSomething, false);
} else if (element.attachEvent)  {
  element.attachEvent('onclick', doSomething);
}
```


## Capture and bubbling
When an event is fired on an element, there are two phases of handling:

1. **capture** - the browser fires the event on the `<html>` element and moves down the DOM tree to the element.
1. **bubbling** - the browser fires the event on the element then moves up the tree via all parents to the `<html>` element.

By default, browsers only trigger events on the bubbling phase (which is more useful because an event can be changed before it reaches parent elements).

However, you can use the capture phase by setting the third `.addEventListener` to `true`. **Don't**. It's not worth it!


## Further reading

* [An Introduction to DOM Events](https://coding.smashingmagazine.com/2013/11/an-introduction-to-dom-events/)
* [MDN events](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Building_blocks/Events)
* [addEventListener](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener)


## Removing events
Events can be removed with `element.removeEventListener(type, listener)`.

This becomes impossible if you used an anonymous function! It's rarely required - you can simply set a variable to enable/disable handling functionality.


## Exercises
1. Create a table with many cells. Output a message to the console when any element is clicked BUT do not attach more than one event handler! (Tip: perhaps attach a handler to `<table>` element itself?)
1. Create an element (any) which changes text as you move a mouse on and off its area.
1. Create a `<textarea>` which shows the number of characters entered (similar to Twitter). Remember to consider the module pattern and/or try to create self-contained objects which could add similar functionality to any `<textarea>` on the page...
