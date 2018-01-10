# Event object
The event handler is passed a single value which is an [event object](https://developer.mozilla.org/en-US/docs/Web/API/Event). This has many properties and methods but the most useful are:

* `.target` - the element which fired the event (very useful)
* `.type` - the event type, e.g. `click`, `blur`, `mouseover` etc. (a single handler can be used for many types)
* `.currentTarget` - a reference to the element the event was attached to

Methods:

* `.preventDefault()` - cancel the event, e.g. stop a form submitting or a character being entered
* `.stopPropagation()` - stops the event propagating up the DOM
* `.stopImmediatePropagation()` - cancel all other listeners

[Mouse event properties](https://developer.mozilla.org/en-US/docs/Web/API/MouseEvent):

* `.clientX` or `.x` - X co-ordinate in DOM element
* `.clientY` or `.y` - Y co-ordinate in DOM element
* `.pageX` - X co-ordinate in page
* `.pageY` - Y co-ordinate in page
* `.button`

[Keyboard event properties](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent):
Be wary about cross-browser handling.

* `.charCode`
* `.code`
* `.key`


## Exercises
1. Create a table with many cells. Change a cell to a random colour when it is clicked BUT do not attach more than one event!
1. Create an element (any) which reports the current cursor co-ordinates as you move around its area.
1. Create a `<textarea>` which shows the number of characters entered (similar to Twitter). Do not permit the user to enter more than 100 characters.
1. Create a form with name, email and telephone fields. Only permit submission if the name and either or both the email and telephone fields are completed. Bonus points for showing error messages!
