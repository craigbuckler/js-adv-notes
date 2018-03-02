# Window handling

The global `window` object represents a browser window containing a DOM `document`. We normally use the `window` object to examine the browser environment or use an HTML5 API including:

* `.history` - history object, e.g. `window.history.back();`
* `.location` - current URL, e.g. `window.location.reload(true);`
* `.name` - the name of the current window (persists between pages)
* `.setTimeout` and `.setInterval`
* `alert`, `confirm` and `prompt`
* `fetch` - modern alternative to XMLHttpRequest used by Ajax


It can also be used to launch new windows (pop-ups) and handle embedded iframes.

* `.open()` and `.close()`
* `.resizeTo()`
* `.scroll()`
* `window.self` - a reference to the current window
* `window.parent` - the parent window (if using frames or iframes)
* `window.opener` - the window which opened the popup
* `.focus()` - focus a window

See [MDN Window](https://developer.mozilla.org/en-US/docs/Web/API/Window).


## Launching a pop-up
Browsers prevent pop-ups unless a user action such as a click is used to initiate it. We therefore need to create a click handler, e.g.

```javascript
document.getElementById('clickme').addEventListener('click', newWindow, false);
```

The handler:

```javascript
function newWindow(e) {
  let popup = window.open('newpage.html', 'popup')
}
```

The `window.open` method requires:

1. A page URL. This can be an empty string if the content is dynamically created.
1. The window name. This can be used as the target for links and forms, e.g. `<a href="page.html" target="popup">open in popup</a>`.

If the window opens successfully, it returns a reference to that window.


## Pop-up configuration
A third (ugly) string parameter can be passed to `window.open()` which controls the dimensions and appearance, e.g.

```javascript
let popup = window.open(
  'newpage.html',
  'popup',
  'width=400,height=600,left=100,top=200,location=0,menubar=0,toolbar=0,personalbar=0,status=0,scrollbars=1,resizable=1'
);
```

Certain features can be enabled or disabled. For example, adding `resizable=0` locks the window to the initial width and height. Note that most browsers do not permit the URL/location bar to be removed even though the option remains.

We can therefore adapt the `newWindow` function to size and position the popup:

```javascript
function newWindow(e) {

  let
    cfg = {
      width: 600,     // ideal width
      height: 600,    // ideal height
      minmargin: 100  // minimum margin from screen edge
    },

    sw = screen.availWidth || 1024,
    sh = screen.availHeight || 700,
    pw = Math.min(cfg.width, (sw - cfg.minmargin * 2)),
    ph = Math.min(cfg.height, (sh - cfg.minmargin * 2)),
    px = Math.floor((sw - pw) / 2),
    py = Math.floor((sh - ph) / 2),

    popup = window.open('newpage.html', 'popup',
      'width=' + pw +
      ',height=' + ph +
      ',left=' + px +
      ',top=' + py +
      ',location=0,menubar=0,toolbar=0,personalbar=0,status=0,scrollbars=1,resizable=1'
    );

  if (popup) {

    // focus the popup
    popup.focus();

  }
  else {
    alert('could not open pop-up?');
  }

}
```

Most of window configurations can be over-ridden once the pop-up has been created, e.g.

* `.resizeTo(newWidth, newHeight)`
* `.moveTo(newX, newY)`
* `.statusbar.visible = false`


## Dynamically writing to the window
Normally, it is easier to provide the URL to a page. However, you can directly create a page from JavaScript using `document.open()` (to clear the document), then `document.write()` then `document.close()`, e.g.

```javascript
if (popup) {
  popup.document.open();
  popup.document.write('<!doctype html><html lang="en"><head><meta charset="UTF-8"><title>popup</title></head><body><p>Hello! I\'m a pop-up!</p><script>alert(\'JS runs!\');<\/script></body></html>');
  popup.document.close();
  popup.focus();
}
```

Notes:

* The escaped `<\/script>` - this ensures it is not mistaken for an actual closing script tag.
* `document.write` should normally be avoided in main page scripts!


If the popup page is in the same domain, it is possible to examine and manipulate DOM nodes in the same way as the current page, e.g.

```javascript
let h1 = popup.document.querySelector('h1');
let div = popup.document.createElement('div');
popup.document.body.appendChild(div);
```

The popup could also manipulate DOM nodes in the window which opened it!

```javascript
let h2 = window.opener.document.querySelector('h2');
let div = window.opener.document.createElement('div');
window.opener.document.body.appendChild(div);
```

## Exercise
Most social media platforms allow you to use a URL to share a link, e.g.

```html
<a href="https://www.facebook.com/sharer/sharer.php?u=https%3A%2F%2Fwww.site.co.uk%2F">share on Facebook</a>

<a href="https://twitter.com/intent/tweet/?text=page%20share&amp;url=https%3A%2F%2Fwww.site.co.uk%2F">share on Twitter</a>
```

*(This is often preferable to the huge JS libraries most platforms provide to implement sharing facilities.)*

These would normally link you away from the current site. Create a system which intercepts social media links and opens them in a 600 x 600 popup. Hints:

* any number of social media links could be on the page
* you could add a `class` to social media links so they can be identified
* there may not be room for a 600 x 600 popup
* remember that, if a popup is successfully opened, the default click action must be prevented!
