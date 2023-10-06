# Implementing promised-based API's 

### Alarm API

These notes will be following the example of creating a promise-based API called ```alarm()```.  ```alarm()``` will take two arguments, one being the name of the user and the other being an integer representing the delay in milliseconds.  After the delay expires, a personalized "Wake up" message will be sent to the user.

### Wrapping setTimeout()
We will use the ```setTimeout()``` API to give ```alarm()``` it's function.  ```setTimeout()``` itself takes two arguments, that being the delay that we want as well as taking a callback function to run when the delay has elapsed, in our case ```alarm()```.
in the example that follows, we will be calling ```setTimeout()``` with a callback function and a 1000ms delay.
##### Html:
```html
<button id="set-alarm">Set alarm</button>
<div id="output"></div>
```
##### Javascript:
```js
const output = document.querySelector("#output");
const button = document.querySelector("#set-alarm");

function setAlarm() {
  setTimeout(() => {
    output.textContent = "Wake up!";
  }, 1000);
}

button.addEventListener("click", setAlarm);
```
The above code executes the ```setAlarm()``` function 1000ms after the ```#set-alarm``` button is clicked, displaying "Wake up!" within the```#output``` div.

###  Promise Constructor.
Next we need our ```alarm()``` function to return a ```promise``` that fulfills when the delay expires,passing the "Wake up" message into a ```then()``` handler.  If the delay value is provided is a negative value, the promise will be rejected instead.

To do these we need to create a ```promise() constructor``` that takes a single function, which we call the ```executor``` as an argument. When a new promise is created, we need to provide the code for what it will do inside our ```executor``` function.

Our ```executor``` will take two arguments itself, each of which is an additional function, ```resolve``` and ```reject```. In the implementation of an ```executor``` you call an underlying asynchronous function, which calls ```resolve``` on success, otherwise on any error ```reject``` is called in its place. both ```reject``` and ```resolve``` can be passed a single parameter of any type.

With this in mind,lets implement ```alarm()``` like so:
```js
function alarm(person, delay) {
  return new Promise((resolve, reject) => {
    if (delay < 0) {
      throw new Error("Alarm delay must not be negative");
    }
    setTimeout(() => {
      resolve(`Wake up, ${person}!`);
    }, delay);
  });
}
```
```alarm()``` here creates a new ```Promise``` inside the executor of which we do the following:
*check for delay being a negative, if it is, call ```reject``` to throw an error.
*call ```setTimeout()``` which passes a callback function and our ```delay```. When our timer expires, ```resolve``` is then called, printing our "Wake up" message.

### ```alarm()``` API Usage
This section ought to be familiar from our [Asynchronous Javascript](https://github.com/insoucia/BVT-module-3-notes/blob/main/introducing-asynchronous-programming.md) note. We call ```alarm()``` and on promise returning we call ```then()``` and ```catch()``` in order to set handlers for fulfillment or rejection of the promise.

```js
const name = document.querySelector("#name");
const delay = document.querySelector("#delay");
const button = document.querySelector("#set-alarm");
const output = document.querySelector("#output");

function alarm(person, delay) {
  return new Promise((resolve, reject) => {
    if (delay < 0) {
      throw new Error("Alarm delay must not be negative");
    }
    setTimeout(() => {
      resolve(`Wake up, ${person}!`);
    }, delay);
  });
}

button.addEventListener("click", () => {
  alarm(name.value, delay.value)
    .then((message) => (output.textContent = message))
    .catch((error) => (output.textContent = `Couldn't set alarm: ${error}`));
});
```
### Async and await with ```alarm()``` API

As ```alarm()``` returns a ```promise```, we can apply all the typical operations we would with any other ```promise```, such as promise chaining, using ```Promise.all()```, and working with async/await:
```js
const name = document.querySelector("#name");
const delay = document.querySelector("#delay");
const button = document.querySelector("#set-alarm");
const output = document.querySelector("#output");

function alarm(person, delay) {
  return new Promise((resolve, reject) => {
    if (delay < 0) {
      throw new Error("Alarm delay must not be negative");
    }
    setTimeout(() => {
      resolve(`Wake up, ${person}!`);
    }, delay);
  });
}

button.addEventListener("click", async () => {
  try {
    const message = await alarm(name.value, delay.value);
    output.textContent = message;
  } catch (error) {
    output.textContent = `Couldn't set alarm: ${error}`;
  }
});
```


These notes are a personal re-stating of [MDN's How to Implement a Promise-based API](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Implementing_a_promise-based_API) for personal study purposes
