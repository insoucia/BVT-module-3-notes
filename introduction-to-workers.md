# Introduction to Workers

Workers are something we use to give an application the ability to run on "multiple threads",that is, to have an application work at multiple simultaneous actions while remaining responsive.

a concern of doing this, however, is that these separate threads can sometimes have access to the same data along with the ability to independently change it, this can cause very difficult to remedy bugs. In order to avoid this, your main code and worker code should not be allowed to directly access one another's variables, only "sharing" data in very particular cases. the main and worker code need to run completely separately, only interacting via messages sent between them. This means workers do not have access to the DOM.

There are 3 general types of workers used, as follows
* Dedicated Workers
* Shared Workers
* Service Workers

These notes will try to cover Dedicated workers, touching only lightly on the others.

### Using Web Workers

For our examples, we are going to run a page that calculates prime-numbers using a worker, allowing it to remain responsive to user actions as the calculation occurs.

#### Synchronous prime generator with worker
In this example we will have 4 files (an html, a css, and two js files).
the complete html css files are as follows:
```html
<!doctype html>
<html lang="en-US">
  <head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width" />
    <title>Prime numbers</title>
    <script src="main.js" defer></script>
    <link href="style.css" rel="stylesheet" />
  </head>

  <body>
    <label for="quota">Number of primes:</label>
    <input type="text" id="quota" name="quota" value="1000000" />

    <button id="generate">Generate primes</button>
    <button id="reload">Reload</button>

    <textarea id="user-input" rows="5" cols="62">
Try typing in here immediately after pressing "Generate primes"
    </textarea>

    <div id="output"></div>
  </body>
</html>

```
```css
textarea {
  display: block;
  margin: 1rem 0;
}

```

With these out of the way we can now work at our main and worker code, each kept separated into their own scripts. Here is ```"main.js"``` and what is happening in it:
```js
// Create a new worker, giving it the code in "generate.js"
const worker = new Worker("./generate.js");

// When the user clicks "Generate primes", send a message to the worker.
// The message command is "generate", and the message also contains "quota",
// which is the number of primes to generate.
document.querySelector("#generate").addEventListener("click", () => {
  const quota = document.querySelector("#quota").value;
  worker.postMessage({
    command: "generate",
    quota,
  });
});

// When the worker sends a message back to the main thread,
// update the output box with a message for the user, including the number of
// primes that were generated, taken from the message data.
worker.addEventListener("message", (message) => {
  document.querySelector(
    "#output",
  ).textContent = `Finished generating ${message.data} primes!`;
});

document.querySelector("#reload").addEventListener("click", () => {
  document.querySelector("#user-input").value =
    'Try typing in here immediately after pressing "Generate primes"';
  document.location.reload();
});
```
* We are first creating a worker using a ```worker()``` constructor that we pass a URL that points towards the worker script. This script executes as soon as the worker is created.
* Next we are adding a ```click``` event handler into the "Generate Primes" button. Instead of calling ```generatePrimes()``` however, we will send a message to the worker with ```worker.postMessage()``` passing a JSON object as its argument. The JSON contains these properties
    *```command```: indicates what we want the worker to do, presented as a string.
    *```quota```: the number of primes we wish to generate
* Next we give the worker a ```message``` event handler in order to have the worker communicate to us when it has completed and pass our data.
* Finally, we give the "Reload" button its ```click``` event handler as well, identical to the synchronous version.

Now comes the worker code which we copy into "generate.js":

```js
// Listen for messages from the main thread.
// If the message command is "generate", call `generatePrimes()`
addEventListener("message", (message) => {
  if (message.data.command === "generate") {
    generatePrimes(message.data.quota);
  }
});

// Generate primes (very inefficiently)
function generatePrimes(quota) {
  function isPrime(n) {
    for (let c = 2; c <= Math.sqrt(n); ++c) {
      if (n % c === 0) {
        return false;
      }
    }
    return true;
  }

  const primes = [];
  const maximum = 1000000;

  while (primes.length < quota) {
    const candidate = Math.floor(Math.random() * (maximum + 1));
    if (isPrime(candidate)) {
      primes.push(candidate);
    }
  }

  // When we have finished, send a message to the main thread,
  // including the number of primes we generated.
  postMessage(primes.length);
}
```

This will run immediately as the main script creates a worker.

### Other Types of Workers

What we have created above is what is known as a *dedicated worker*, meaning it is used by a single instance of a script.

additionally there are other types of workers,such as:

*[Shared workers](https://developer.mozilla.org/en-US/docs/Web/API/SharedWorker) that can be shared by several scripts running in several windows.
*[Service workers](https://developer.mozilla.org/en-US/docs/Web/API/Service_Worker_API) which act like proxy servers, making use of resource caching in order for web applications to work even when the user is offline.

## Conclusion

In these notes we've introduced the concept of web workers for offloading tasks into threads. Workers such as these can be effective at keeping main applications responsive. Be sure to remember that workers cannot access all the APIs the main program can, nor can they access the DOM.


## Credit
These notes are a personal re-stating of [MDN's Introducing Workers](https://developer.mozilla.org/en-US/docs/Learn/JavaScript/Asynchronous/Introducing_workers) for personal study purposes