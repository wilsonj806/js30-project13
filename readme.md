# Notes on Project 13 - Slide In Scroll

## Table of Contents

- [New properties](#new-properties)
- [New Concepts](#new-concepts)
- [Logic Explanation](#logic-explanation)
- [References](#refrences)

## New properties
```javascript
Window.scrollY
Window.innerheight
HTMLElement.height
HTMLElement.offsetTop
arguments
Function.prototype.apply()
scope.setTimeout()
scope.clearTimeout()
```
## References

- [Function.prototype.apply()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)
- [Window.setTimeout()](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/setTimeout)
- [Window.clearTimeout()](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/clearTimeout)

## Logic explanation

The script has a bunch of things going on but everything, save the debounce function, is fairly simple.

### Global variables and event listeners

We only have one global variable this time and it's all of the `<img>` elements with the class `.slide-in`

```javascript
  const sliderImages = documentquerySelectorAll('img.slide-in');
```
In addition we only have one event listener but this one's interesting because it goes to the `debounce` function that has the `checkSlide` function as an argument

```javascript
Window.addEventListener('scroll', debounce(checkSlide));
```

### Debounce function

The debounce function serves as an automatic timeout function for event listeners that are expecting continuous input (or something like that).
  - e.g a scroll event in our case
  - It's not used in the video player project because we need the precision and the scope of the input is limited to that one element
    - Here it's literally being called every like .05ms without the debounce function

So here's the function below:

```javascript
  function debounce(func, wait = 20, immediate = true) {
    var timeout;
    return function() {
      var context = this, args = arguments;

      var later = function() {
        timeout = null;
        if (!immediate) func.apply(context, args);
      };

      var callNow = immediate && !timeout;
      clearTimeout(timeout);
      timeout = setTimeout(later, wait);
      if (callNow) func.apply(context, args);
    };
  }
```

First things first, the debounce function returns an anonymous function that does a bunch of stuff in addition to declaring a local variable: `timeout`

That "stuff" is as follows:
  - Declares two variables, `context` and `args`
    - `context` is set equal to *this* which in the context of this function is the `Window` object
    - `args` is the `argument` object which is the function that was passed into the debouance function as an argument
      - `args` here is the *Event* that's set as an additional argument in debauce
        - this is because of the event listener, so debounce should look like the following after an event:
          ```javascript
            debounce(Event, func = checkSlide(), wait = 20, immediate = true );
          ```
  - Declares a local function `later` that does the following:
    - sets the value of `timeout` to *null*
    - uses a conditional statement that looks for the inverse value of `immediate` (which is found as an initial argument in the debounce function)
      - if the inverse value is found to be true, then the following is done:
        - `Function.prototype.apply()` is called on the input function *func*
        - the *apply()* method then passes the *context* and *args* arguments into that function.
  - Then a boolean variable `callNow` is declared
  - Then the `clearTimeout()` method is used to cancel a timeout established by a previous *setTimeout()* call
    - note that `clearTimeout` has an input of *timeoutID* which in this case is the variable `timeout`
  - Then `timeout` has it's value set to `setTimeout(later, wait)`
    - this means that the function `later` is the function being called after *wait = 20ms*
  - We finally use a conditional statement to check the state of the timeout
    - if `callNow` returns *true* (i.e `timeout = null`) then we use `func.apply(context, args)` to call `checkSlide`