extendFunction.js
=================

The easiest way to overwrite other functions with additional functionality

Example:
Let's modify alert to keep a history array of all the messages we alert:
```javascript
// paste extendFunction.js into your console and run these examples in your console :)

window.alertHistory = [];
// extend alert with additional functionality.
extendFunction('alert', function(args) {
  // args is an array of the arguments alert was called with
  // args[0] is the alert message.
  alertHistory.push(args[0]);
});
// Test it!
alert('a message');
console.log(alertHistory);
if (alertHistory[0] === 'a message') {
  console.warn('extendFunction worked!');
}
```

So extendFunction takes 2 parameters: `extendFunction(theFunction, additionalFunctionality)`

Now let's add __" from DevinRhode2"__ to every alert message
```javascript
extendFunction('alert', function(args, nativeAlert) {
  // the second argument here is the nativeAlert function
  // precisely, that's window.alert before it was modified
  nativeAlert(args[0] + ' from DevinRhode2')
  // because you called nativeAlert, extendFunction doesn't.
  // however, if you don't call it, then extendFunction will
  // If you don't want the original function called, then you should just overwrite the function:
  // window.alert = function alertOverride() { ... };
});
```
extendFunction also works for methods:
```javascript
extendFunction('console.log', function(args, nativeConsoleLog) {
  // omg console.log was called!
});
```

But if your functions are not global like `alert` and `console.log`, then you need to do this:
```javascript
localFunction = extendFunction(localFunction, function(args, originalLocalFunction) {
  // your magic here!
});
```
without extendFunction, you have to write this mess of code:
```
var oldLocalFunction = localFunction;
localFunction = function(paramA, paramB) {
 // your magic here!
 var args = Array.prototype.slice.call(arguments);
 return oldLocalFunction.apply(this, args);
};
```

Modify return values:
```javascript
extendFunction('strangeModule.strangeMethod', function(args, prevFunc) {
  var returnValue = prevFunc.apply(this, args);
  // docs on apply: https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/apply

  returnValue.extraInfo = 'idk';
  return returnValue;
});
```

Or promises:
```javascript
extendFunction('$.ajax', function(args, prevFunc) {
  var stackOnSend = new Error().stack;

  //prevFunc is the original $.ajax
  //call that and store the value to return
  var returnValue = prevFunc.apply(this, args);
  returnValue.fail(function(){
    console.error('request failed:', arguments, 'stackOnSend:', stackOnSend);
  });
  return returnValue;
});
```

I'm calling the original function asynchronously, but notice `extendFunction` is also calling it.

###### What's going on?

When extendFunction sees you haven't called the original function, it calls it for you - and, if you didn't return anything (i.e. `undefined` was returned) then extendFunction will return the value returned from the original function.

###### Tell extendFunction not to call the originalFunction at all:

```javascript
extendFunction(fn, function(args, originalFunction, dontCallOriginal){
  dontCallOriginal();
  $.getJSON('/posts')
  .done(function(json){
    originalFunction(json);
  });
});
```

MIT licensed


[![Bitdeli Badge](https://d2weczhvl823v0.cloudfront.net/devinrhode2/extendfunction.js/trend.png)](https://bitdeli.com/free "Bitdeli Badge")

