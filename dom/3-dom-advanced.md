# Advanced DOM manipulation
Sometimes it will be necessary to add, modify or remove elements within the DOM.

## DOM manipulation methods
The main methods include (there are many more!):

* [`.appendChild()`](https://developer.mozilla.org/en-US/docs/Web/API/Node/appendChild)
* [`.insertBefore()`](https://developer.mozilla.org/en-US/docs/Web/API/Node/insertBefore)
* [`.cloneNode()`](https://developer.mozilla.org/en-US/docs/Web/API/Node/cloneNode)
* [`.removeChild()`](https://developer.mozilla.org/en-US/docs/Web/API/Node/removeChild)
* [`.replaceChild()`](https://developer.mozilla.org/en-US/docs/Web/API/Node/replaceChild)
* [`.innerHTML`](https://developer.mozilla.org/en-US/docs/Web/API/Element/innerHTML)
* [`.outerHTML`](https://developer.mozilla.org/en-US/docs/Web/API/Element/outerHTML)

Use `document.createElement([name])` to create a new element node.


## Document fragments
Adding individual nodes to the DOM one at a time can be expensive. It is usually better to construct a document fragment in memory then append it to the DOM when ready, e.g.

```html
// new fragment
let frag = document.createDocumentFragment();

// append a DIV to the fragment (div now references that node)
let div = frag.appendChild( document.createElement('div') );

// set the DIV text
div.textContent = 'my new element';

// add the fragment to the body
document.body.appendChild(frag);

```


## HTML5 `<template>` tag
Segments of your HTML page can be marked as a [template](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/template) which allow it to be used multiple times within a page. See:

https://jsfiddle.net/craigbuckler/od0r1u7p/

This only works in recent browsers and not IE. However, a polyfill and style is used to apply backward compatibility.


## Exercises
Using this HTML (or similar):

```html
<!doctype html>
<html>
  <head></head>
  <body>
    <article>
      <p class="intro"><a href="#">a link</a></p>
      <p>another paragraph</p>
      <ul id="list">
        <li>item 1</li>
        <li>item 2</li>
        <li>item 3</li>
      </ul>
    </article>
  </body>
</html>
```

1. Write a function to remove all child nodes of an element. Use it to clear the `<ul>` list.
1. Move the the first paragraph to the end of the article.
1. Insert a new `<h2>New title</h2>` title before the list.
1. Insert a new paragraph at the end with the current date.

**Task:** Create a modal pop-up dialog system which can have a custom message and an `OK` button to dismiss. The pop-up should appear when a user clicks the link. Note that we've not covered events yet, so a partial solution may only be achievable at this stage. Bonus points for creating two systems: one with a `<template>` and one adding nodes using a document fragment.
