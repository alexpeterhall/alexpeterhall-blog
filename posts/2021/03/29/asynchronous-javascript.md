---
title: JavaScript Promises
description: Notes from JavaScript The Definitive Guide
date: 2021-03-29
readingTime: 6 minutes
tags: ["javascript", "promises", "async"]
layout: layouts/post.njk
---

I've been reading a lot about Promises the past few weeks while going through [JavaScript: The Definitive Guide](https://www.amazon.com/JavaScript-Definitive-Most-Used-Programming-Language/dp/1491952024). The [7th Edition](https://davidflanagan.com/2020/05/03/changes-in-the-seventh-edition.html) was released in June 2020 and adds documentation for everything from ES6 up to ES2020. It's hard for a programming book to be more current. I've also read through some online resources and I'll link to a couple of the better ones below.

This post captures my notes from "Chapter 13: Asynchronous JavaScript" from [The Definitive Guide](https://www.amazon.com/JavaScript-Definitive-Most-Used-Programming-Language/dp/1491952024). It is not meant to be an all-encompassing guide or tutorial.

## Callbacks

Before we can talk about Promises we have to first mention callbacks. A callback refers to a function that you provide as an argument to another function to be executed at a later time. That other function will invoke or "call back" your function when some condition has been met. This is common in asynchronous programming because there are many cases where we have to wait for a result to return (i.e. a network request) before our function can do something with the result.

Promises are basically a different way of working with callback functions. They solve a couple of the problems with asynchronous programming with callbacks. The big two are difficult error handling and convoluted code (AKA "[callback hell](http://callbackhell.com)" or the "pyramid of doom").

## Promise Terminology

- **Promise:** A Promise is an object that represents the return value of some operation that will complete in the future.
- **Fulfilled:** A Promise is fulfilled when the asynchronous operation it represents successfully completes and returns its value. The return value of the Promise is the return value of the successful operation.
- **Rejected:** A Promise is rejected when the asynchronous operation it represents has failed and returns an error. The return value of the Promise is the error object the failed operation threw.
- **Pending:** A Promise is pending when it has not yet been fulfilled or rejected.
- **Settled:** A Promise is settled when it has either fulfilled or rejected (it can never be both). Once a Promise is settled it can never change from fulfilled to rejected or vise versa. A settled Promise will have a value associated to it that will not change.
- **Resolved:** A Promise is resolved when it has become associated with or "locked onto" another Promise. When the return value of a callback to a Promise is itself a Promise, the first Promise is now tied to the result of that second Promise. Promise #1 has resolved, but not yet fulfilled or rejected. Promise #1 can only settle when Promise #2 settles. If Promise #2 fulfills then Promise #1 will also fulfill with the same value. Likewise for rejecting.

To explain "resolved" another way. If a callback to a Promise successfully runs its code and returns a "normal" non-Promise value, the Promise is said to "resolve" to that returned value, the Promise is now "fulfilled", and everybody is happy. However, if the callback to a Promise returns yet another Promise object then the first Promise will "resolve" to (or "lock on" to) that new Promise value and it stays in this "resolved" state where it is not yet fulfilled or rejected. It has to wait for Promise #2 to settle and its outcome becomes completely dependent on what happens with that second Promise.

Resolved is a tricky state that David Flanagan does an excellent job of explaining in the book. I haven't found any online explanations that cover this "resolved" concept quite like in The Definitive Guide. I think it can be confusing because the term "resolved" is often used synonymously with "fulfilled". The Promise methods themselves are called `resolve()` and `reject()`, not `fulfill()` and `reject()`. This is often true enough because a Promise will resolve to the fulfillment value itself, not another Promise. But not all of the time.

## Chaining Promises

### then()

One of the major benefits of Promises is it gives us a better way of executing asynchronous operations in sequence. Promise objects define an instance method called `then()` which is kind of like a callback registration method. Instead of nesting callbacks inside of each other, we can pass our callback in the `then()` method and append it directly to the function invocation that returned the initial Promise. This is why Promise objects are sometimes referred to as "thenable". Because each invocation of `then()` returns a new Promise we can continue to chain `then()` calls together to form a "promise chain" or a sequence of asynchronous operations that will run in order.

<pre><code>PromiseObject(doSomething)
  .then(doCallback1)
  .then(doCallback2)</code></pre>

Part of what makes the promise chain useful is subsequent operations utilize the return value of the previous operation. But, this means if you forget to return a value from your callback function you will pass `undefined` to the next function in line and potentially have a bug that is hard to spot. This problem can pop up even more when using arrow functions if you're not careful about the implicit return syntax. You may think your arrow function is implicitly returning when it's not and returning `undefined`.

The `then()` method takes two arguments. The first argument is a callback function that will be invoked with the returned value of the previous successful (fulfilled) async operation. The second argument is a callback function that will be invoked with the returned value of the previous failed (rejected) async operation. Also known as an error handler.

<pre><code>PromiseObject(doSomething)
  .then(doCallback1, handleDoSomethingFailure)
  .then(doCallback2, handleDoCallback1Failure)</code></pre>

The problem with this is if `doCallback2()` fails there is no error handling for it. You could use `then()` as only an error handler by passing `null` as the first argument and your error handler function as the second argument.

<pre><code>PromiseObject(doSomething)
  .then(doCallback1, handleDoSomethingFailure)
  .then(doCallback2, handleDoCallback1Failure)
  .then(null, handleDoCallback2Failure)</code></pre>

However, it is uncommon to see `then()` used like this to handle errors because `catch()` is more idiomatic and easier to read.

### catch()

As mentioned above, `catch()` is the same as calling `then()` with a null first argument and error handling function as the second argument.

<pre><code>PromiseObject(doSomething)
  .then(doCallback1)
  .then(doCallback2)
  .catch(handleFailures)</code></pre>

While commonly used at the end of a Promise chain to handle errors it doesn't have to be. `catch` can be inserted into the middle of a Promise chain to handle a recoverable error and allow subsequent steps in the Promise chain to proceed. If no errors are thrown further up the chain that `catch()` function call will be skipped. If that `catch()` itself throws an error though it will propagate down the Promise chain.

<pre><code>PromiseObject(doSomething)
  .then(doCallback1)
  .then(doCallback2)
  .catch(handleFailures)
  .then(doCallback3)
  .then(doCallback4)
  .catch(handleMoreFailures)</code></pre>

### finally()

`finally()` was introduced in ES2018 and is used for running some final cleanup code at the end of a Promise chain. It will be invoked when the Promise you call it on settles regardless of if that Promise fulfilled or rejected. `finally()` is passed no arguments and it returns a new Promise object. It will generally settle with the same value that the previous Promise in the chain settled with and that value is usually ignored.

## Creating a Promise

Chaining `then()` calls onto an existing library method (such as `fetch()`) that already returns a Promise is easy. But how do we create our Promise from scratch?

<!-- prettier-ignore -->
<pre><code>let myNewPromise = new Promise((resolve, reject) => {
  if (thingsGoWell) {
    resolve('Hurray!');
  }
  if (thingsGoOffTheRails) {
    reject(new Error('The train has derailed.'))
  }
})
myNewPromise.then(celebrate());</code></pre>

## Additional Methods

Not going deep here. These are my high-level notes on some additional Promise methods. Check out the [MDN Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) for in-depth explanations of each.

**Promise.resolve()** - Returns a Promise object that is resolved with a given value.
**Promise.reject()** - Returns a Promise object that is rejected with a given reason.
**Promise.all()** - This allows you to run multiple asynchronous operations in parallel. Takes an array of Promise objects as an input and returns a Promise object. It will immediately reject if any of the Promises inputs reject, even if there are pending operations. If all of the input Promises fulfill then `Promise.all()` will fulfill with an array containing the fulfillment values of all of the input Promises. Non-Promise input values are treated as already fulfilled Promises.
**Promise.allSettled()** - New in ES2020. Like `Promise.all()`, this is a way to run multiple asynchronous operations concurrently and takes an array of input Promise objects. However, it will never reject the returned Promise. `Promise.allSettled()` will wait until all of the input Promises have settled and it returns an array of objects representing the return values. Each object will have a `status` property with a value of "fulfilled" or "rejected". Fulfilled promises will have a `value` property containing the fulfillment value. Rejected promises will have a `reason` property containing the error. Non-Promise input values are treated as already fulfilled Promises.
**Promise.race()** - This is like `Promise.all()` except it will immediately return the value of the first input Promise to fulfill or reject.

## Sources

- Flanagan, David. "Chapter 13: Asynchronous JavaScript". JavaScript: The Definitive Guide Seventh Edition, O'Rielly, 2020, pp. 341-377
- [JavaScript Promises - An introduction on Web.dev](https://web.dev/promises/)
- [JavaScript.info - Promises, async/await](https://javascript.info/async)
- [MDN Web Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises)
