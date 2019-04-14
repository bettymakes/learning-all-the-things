---
topic: JavaScript Concurrency model and Event Loop
tags: "JavaScript"
date: 2019-04-13
---

# ➰ JavaScript Concurrency model and Event Loop

## Intro
- Javascript runtime is single threaded which means that it can execute one piece of code at a time

## JavaScript
- This is how the execution is handled within JavaScript:
  - Whenever a function is invoked it is pushed into the call stack where it gets executed
  - When the function is done with it’s execution and is returning either implicitly or explicitly it will be popped off the stack
  - While Javascript can only execute one thing at a time, that’s not the case with the Browser
  - The Browser has it’s own set of API’s (e.g. `setTimeout`, `XMLHttpRequests` ) which are not specified in the Javascript runtime

## In the Browser
- `Heap`
  - Objects are allocated in a heap which is just a name to denote a large mostly unstructured region of memory

- Callback `Queue`
  - A data structure that stores all the callbacks and those are processed based on FIFO (First in First Out)

- Example
  ```javascript
  console.log("hi");

  setTimeout(function timer1() {
    console.log("timer one");
  }, 5000);

  console.log("wow");

  setTimeout(function timer2() {
    console.log("timer two");
  }, 1000);

  console.log("fake end");
  ```

  - `console.log('hi')`
    - JS Runtime encounters the first function invocation `console.log`, and it’s added to the call stack
    - `console.log('hi')` is executed
    - `console.log('hi')` is popped off the call stack

  - `setTimeout (timer1)`
    - JS Runtime reaches next function invocation, `setTimeout`, it’s added to the call stack
    - However, this `fn` is not specified in JS Runtime, it’s part of the Web API
    - It is popped off the call stack
    - It’s delegated to the Browser to handle its execution (in this case, it starts the counter on `timer1`)

  - `console.log('wow')`
    - This is the next function invocation, `console.log` is added to the call stack
    - `console.log('wow')` is executed
    - `console.log('wow')` is popped off the call stack

  - `setTimeout (timer2)`
    - The following function invocation `setTimeout` is added to the call stack
    - Again, because `setTimeout` is part of the Web API …
    - `setTimeout` is popped off the call stack
    - It’s delegated to the the Browser to handle again (in this case, it starts the counter for `timer2`)

  - `console.log('fake end')`
    - Reaching the last line of code, it’s a function invocation for `console.log` which is added to the call stack
    - `console.log('fake end')` is executed
    - `console.log('fake end')` is popped off the call stack

  - Browser `Callback Queue`
    - For the browser to push any message from the queue to the call stack, the call stack has to be empty first. That is why even though the delay provided in the setTimeout() was 1 second, the callback to exec() has to wait till the execution of all the frames in the call stack is complete.
    - The delay argument in setTimeout() does not guarantee the start of execution after timer finishes the delay. It serves as a minimum time for the delay part.
    - As each timer resolved, they were added to the `Callback Queue`
    - Even though `timer2` was executed later, it had a shorter timer so it was added to the queue first
    - `timer1` was added to the queue a short time after

  - Now that the execution within the “main” stack has completed …
    - `timer2` is added to the call stack, executed, popped off the stack
    - `timer1` is added to the call stack, executed, popped off the stack
  - Putting it all together, this is what would log to the console:

    ```javascript
    console.log("hi");

    setTimeout(function timer1() {
      console.log("last");
    }, 5000);

    console.log("wow");

    setTimeout(function timer2() {
      console.log("first");
    }, 1000);

    console.log("fake end");

    // hi
    // wow
    // fake end
    // timer two
    // timer one
    ```

  - [Play this example through in the Event Loop Playground](http://latentflip.com/loupe/?code=CmNvbnNvbGUubG9nKCdoaScpOwoKc2V0VGltZW91dChmdW5jdGlvbiB0aW1lcjEoKSB7CiAgIGNvbnNvbGUubG9nKCdsYXN0Jyk7Cn0sIDUwMDApOwoKY29uc29sZS5sb2coJ3dvdycpOwoKc2V0VGltZW91dChmdW5jdGlvbiB0aW1lcjIoKSB7CiAgIGNvbnNvbGUubG9nKCdmaXJzdCcpOwp9LCAxMDAwKTsKCmNvbnNvbGUubG9nKCdmYWtlIGVuZCcpOwpjb25zb2xlLmxvZygnZGVsYXkgZmFrZSBlbmQgbW9yZSAxJyk7CmNvbnNvbGUubG9nKCdkZWxheSBmYWtlIGVuZCBtb3JlIDInKTsKY29uc29sZS5sb2coJ2RlbGF5IGZha2UgZW5kIG1vcmUgMycpOwpjb25zb2xlLmxvZygnZGVsYXkgZmFrZSBlbmQgbW9yZSA0Jyk7CmNvbnNvbGUubG9nKCdkZWxheSBmYWtlIGVuZCBtb3JlIDUnKTsKY29uc29sZS5sb2coJ2RlbGF5IGZha2UgZW5kIG1vcmUgNicpOwpjb25zb2xlLmxvZygnZGVsYXkgZmFrZSBlbmQgbW9yZSA3Jyk7CmNvbnNvbGUubG9nKCdkZWxheSBmYWtlIGVuZCBtb3JlIDgnKTsKY29uc29sZS5sb2coJ2RlbGF5IGZha2UgZW5kIG1vcmUgOScpOw%3D%3D!!!PGJ1dHRvbj5DbGljayBtZSE8L2J1dHRvbj4%3D)

## Terminology:

- `Stack`
  - This represents the single thread provided for JavaScript code execution. Function calls form a stack of frames
- Queue (messages are queued up, asynchronous events are added to the queue)
- `Heap`
  - Objects are allocated in a heap which is just a name to denote a large mostly unstructured region of memory
- `Browser` or `Web API`
- They're built into your web browser, and are able to expose data from the browser and surrounding computer environment and do useful complex things with it. They are not part of the JavaScript language itself, rather they are built on top of the core JavaScript language, providing you with extra superpowers to use in your JavaScript code.

## More resources

- [Play with the Event Loop](http://latentflip.com/loupe)
- [MDN Concurrency model and Event Loop Docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop)
- [FreeCodeCamp - Concurrency model and Event Loop](https://guide.freecodecamp.org/javascript/concurrency-model-and-event-loop/)
- [Medium - Event Loop Explained](https://medium.com/front-end-weekly/javascript-event-loop-explained-4cd26af121d4)
