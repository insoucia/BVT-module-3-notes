# Asynchronous Javascript

### What is Asynchronous programming?
Asynchronous programming describes an approach that allows for your program to begin long-running tasks while still allowing it to respond to other inputs and events, returning the result when completed. Normally, beginning a task requires waiting for it to complete before starting another, so asynchronicity is important for allowing users to interact with elements on the page without requiring downtime on every other part of the application.

#### Examples of browser functions that are asynchronous
 * Camera or Mic usage via ```getUserMedia()```. Important so the user can interact while streaming their media
 * File selection with ```showOpenFilePicker()```.
 * HTTP requests made with ```fetch()```
 * Timers set with ```setTimeout()```or ```setInterval()```
 * Requests made with Geolocation API's
 * etc.


### An example of Synchronous code
```js 
const name = "Sadie";
const greeting = `Hello, my name is ${name}!`;
console.log(greeting);
// "Hello, my name is Sadie!"
```

This code executes from top to bottom, step by step.  This means the browser must wait for each previous step to be completed before continuing to the next. in a small program like this one that presents no issue, but in a more complex system, needing each line to complete its task before any other presents problems. 

an example of such problems.  if we had a button that will do a calculation for use based upon a users inputting a value as well as a text-entry box for them to give another input, the user submitting the first value would start the synchronous function, disallowing the entry of the next until the function completes. If we wanted the user to be able to keep interacting and entering values, we would need to make the function asynchronous.


## Event Handlers

Event handlers are actually one form of asynchronous functionality.  you provide the handler with something that will be called in a non-immediate way, and when it is called it provides a result upon completion.


# Callbacks

Callbacks are functions that are passed into other functions with the intent they will be called at the time they are needed. These were once the main way to use asynchronous functions in JS.
Callback-based code however can be difficult to easily comprehend.  here is an example of a synchronous function and transitioning it to the use of callbacks:

#### synchronous
```js
function doStep1(init) {
  return init + 1;
}

function doStep2(init) {
  return init + 2;
}

function doStep3(init) {
  return init + 3;
}

function doOperation() {
  let result = 0;
  result = doStep1(result);
  result = doStep2(result);
  result = doStep3(result);
  console.log(`result: ${result}`);
}

doOperation();

```
#### asynchronous with callbacks
```js
function doStep1(init, callback) {
  const result = init + 1;
  callback(result);
}

function doStep2(init, callback) {
  const result = init + 2;
  callback(result);
}

function doStep3(init, callback) {
  const result = init + 3;
  callback(result);
}

function doOperation() {
  doStep1(0, (result1) => {
    doStep2(result1, (result2) => {
      doStep3(result2, (result3) => {
        console.log(`result: ${result3}`);
      });
    });
  });
}

doOperation();

```

nesting deeply into a function in the way makes the code difficult to read and debug. further, any errors will have to be handled at each level of nesting rather than only at the top level. because of this, callbacks are not as used in modern asynchronous API's, rather <u>promises</u> are used.

## Credit
These notes are a personal re-stating of [MDN's Introducing asynchronous JavaScript](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Introducing) for personal study purposes