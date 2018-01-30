# JavaScript dates

Date handling in JavaScript is a little nasty. Developers often resort to date libraries but they can be overkill.

Create a new date object:

```javascript
var d = new Date();
```

This sets `d` to the current date/time. A value can be passed to the `Date()` constructor:

1. A string representing the date/time, e.g. '2018-01-30'.
1. A series of numbers representing the date/time, e.g. 2018,0,30 (months are zero-based)
1. A number representing the number of milliseconds since midnight on 1 January, 1970.

There are a couple of ways to check you have a real date - either with `new Date()`:

```javascript
var arg = 'a date string or number';
var d = new Date(arg);

if (String(d) !== 'Invalid Date') console.log(d);
```

or `Date.parse`:

```javascript
var arg = 'a date string or number';
var d = Date.parse(arg);

if (!isNaN(d)) console.log(d);
```

Note that `Date.parse` can be inconsistent across browsers.


## Date properties
Main GET properties:

* `.getFullYear()` - e.g. 2018
* `.getMonth()` - where 0 is January, 1 is February...
* `.getDate()` - day of month
* `.getDay()` - day of week where 0 is Sunday, 1 is Monday...
* `.getHours()`
* `.getMinutes()`
* `.getSeconds()`
* `.getMilliseconds()`
* `.getTimezoneOffset()` - number of minutes difference to UTC

Main SET properties:

* `.setFullYear()`
* `.setMonth()` - where 0 is January, 1 is February...
* `.setDate()` - day of month
* `.setDay()` - day of week where 0 is Sunday, 1 is Monday...
* `.setHours()`
* `.setMinutes()`
* `.setSeconds()`
* `.setMilliseconds()`
* `.setTimezoneOffset()` - number of minutes difference to UTC

Most of the above have UTC (Coordinated Universal Time) equivalents, e.g. `.getUTCFullYear()` and `.setUTCFullYear()`.

Note that negative or impossible numbers are resolved correctly, e.g. using `.setDate(29)` on 1 February will resolve to 1 March on any non-leap year.

All `set` methods change the original date. Example: find midnight today...

```javascript
var d = new Date(); // now
d.setMilliseconds(0);
d.setSeconds(0);
d.setMinutes(0);
d.setHours(0);

console.log(d); // midnight of today
```


## Tips
If using Node.js on the server, it's often best to use the UTC functions which matches GMT outside of daylight saving time. UTC never changes.

On the client, you can use the user's local time (not UTC) although there's no guarantee it'll be set correctly. Never depend on it!


## Further reading

* [MDN Date](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date)


## Exercises
Create functions to:

1. Pretty-print a date in the format "DD-MM-YYYY, HH:MM".
1. Given a date and a positive or negative number, adds that number to the day, i.e. -1 would return yesterday, 1 would return tomorrow.
1. Given a date, return a new date matching midnight on the first day of month, e.g. 25-06-2018 would return 01-06-2018.
1. Write a similar function which returns the last day of the month.
1. Given a date, return a new date matching midnight on the first Monday of that week, e.g. 28-01-18 would return 22-01-18.
