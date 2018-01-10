# Custom events
Despite a long list of event types, you can also create your own custom events! It's rare you need to do this, but it can be very useful.

It could be used when something occurs in JavaScript, e.g. data has been successfully downloaded. An event could then be fired so any number of handlers could be triggered.


## Scroll/resize example
It's often necessary to know whether the page has been scrolled, perhaps so an image can be lazy-loaded. However, resizing the browser window could also trigger this action. Any lazy-loading system would need to monitor both events. We could write a custom event so only a single event needs to be monitored. In addition, we could throttle this event so it only occurs, say, at most every 300ms:

```javascript
// custom events are supported?
if (CustomEvent && window.addEventListener) {

  // throttled scroll/resize
  window.addEventListener('scroll', scroller, false);
  window.addEventListener('resize', scroller, false);
  scroller();

  var scTimer, scPos = 0;
  function scroller() {

    // timer fires the event no more than once every 300ms
    scTimer = scTimer || setTimeout(function() {

      // page scroll direction
      var
        wY = window.pageYOffset,
        dir = Math.sign(wY - scPos);

      scPos = wY;
      scTimer = null;

      // dispatch event on window
      window.dispatchEvent(

        new CustomEvent(
          'scrollresize',           // custom event name
          { detail: {               // further information for the handler
            pos: scPos,             // current Y position
            dir: dir                // direction: -1 for up, 1 for down, 0 for no scroll
          }},
          bubbles: false,           // event bubbles through DOM (not necessary here)
          cancelable: false         // event can be cancelled (not necessary here)
        )

      );

    }, 300);

  }

}


// custom event handler
window.addEventListener('scrollresize', function(e) {

  console.log('at scroll position ' + e.detail.pos);

  if (e.detail.dir > 0) {
    console.log('scrolling down');
  }
  else if (e.detail.dir < 0) {
    console.log('scrolling up');
  }
  else {
    console.log('not scrolling');
  }

}, false);


```

## Further reading

* [How to Create Custom Events in JavaScript](https://www.sitepoint.com/javascript-custom-events/)


## Exercise
Using your Twitter-like `<textarea>`, fire a custom event at the point the user exceeds or falls below 80 characters. A handler should detect this event and colour the character counter red when more than 80 characters have been entered.

Bonus points for firing the custom event only at the point the state changes!
