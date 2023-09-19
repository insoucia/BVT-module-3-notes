# Promises in JS

### What are promises?

The foundation of asynchronous programming in JavaScript is **Promises**. Promises exist as an object that an asynchronous function returns that are representative of an operations current state.
In this way, when the promise is returned, it does not mean the operation is complete, but the object provides methods to handle its success or failure.

### Using ```fetch()``` API

```fetch()```API is the modern replacement for ```XMLHttpRequest``` that opts for a promise-based approach.

here is the example from [MDN](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Promises#using_the_fetch_api) that can be run in the browsers JS console to demonstrate
```js
const fetchPromise = fetch(
  "https://mdn.github.io/learning-area/javascript/apis/fetching-data/can-store/products.json",
);

console.log(fetchPromise);

fetchPromise.then((response) => {
  console.log(`Received response: ${response.status}`);
});

console.log("Started request…");
```
in this example, here is what is happening:
1. ```fetch()``` is called and assigned to ```fetchPromise```
1. ```fetchPromise``` variable is then immediately logged into the console. The output here should appear as ```Promise {<state>: "pending"}``` which communicates that we have a ```Promise``` object with a pending state. this state tells us the fetch operation is ongoing.
1.  we are also passing a handler function into the promises ```then()``` function. if ```fetch()``` succeeds our handler will be called and a ```Response``` object containing the servers response passed in.
1. A message is also logged that we have begun the request.

in the end, our output should look something like:
```
Promise { <state>: "pending" }
Started request…
Received response: 200
```

unlike if we performed a function like this synchronously ```started request_``` is logged before any response is received and ```fetch()``` returns while the request is still ongoing. What this allows is for our program to remain responsive while this is happening.

### Promise Chains

with ```fetch()``` once you receive a ```response``` object another function needs to be called to get the data.  in our example we are wanting response data to come back in the form of a json file, so we should call the ```response.json()``` method. ```json()``` is asynchronous too so we end up with two asynchronous functions in succession.

```js
const fetchPromise = fetch(
  "https://mdn.github.io/learning-area/javascript/apis/fetching-data/can-store/products.json",
);

fetchPromise.then((response) => {
  const jsonPromise = response.json();
  jsonPromise.then((data) => {
    console.log(data[0].name);
  });
});
```
here, we have added a ```then()``` handler to the returned promise again but this time calling ```response.json()```. a new ```then()``` is passed into the promise that gets returned.

the first item listed in ```products.json``` is 'baked beans', this is what our example should log.

so, what makes this better than nesting callbacks which we noted were difficult to read and debug? this is just the same thing using ```then()```, however ```then()``` returns a promise itself so that if we rewrite our code like so:
```js
const fetchPromise = fetch(
  "https://mdn.github.io/learning-area/javascript/apis/fetching-data/can-store/products.json",
);

fetchPromise
  .then((response) => response.json())
  .then((data) => {
    console.log(data[0].name);
  });
```
we can return the promise that ```response.json()``` returns,calling our second ```then()``` on that value. this is **Promise Chaining** and allows us to avoid layers and layers of nesting while making multiple consecutive function calls.

### Error Catching 

There are many reason the ```fetch()```API could throw an error, so how ought we handle them? To help us with this ```Promise``` objects provide a ```catch()``` method. This method is called when the operation fails, directly opposite to the ```then()``` method.
If ```catch()``` is added  to the end of a promise chain, it will be called if any of them fail, allowing for a single location to handle any error.

### Terminology

Here are some terms that are good to know in relation to using ```promises```

*pending - the promises function has not yet succeeded nor failed, but the promise has been created
*fulfilled - the function has succeeded, this will result in its ```then()``` handler being called
*rejected -the function has failed, this will result in its ```catch()``` handler being called

### Async and Await

The keyword ```async``` allows for a simpler way of working with promises. adding the keyword to the beginning of a function turns that function into an asynchronous one.
```js
async function myFunction() {
  // This is an async function
}
```
Inside of such a function, the additional keyword ```await```  may be used when calling a function that returns a promise. This causes that code to wait until the promise is settled, returning the value on success or throwing the rejected value.

## Credit
These notes are a personal re-stating of [MDN's How to use promises](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Promises) for personal study purposes