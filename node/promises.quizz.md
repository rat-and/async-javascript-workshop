# Question 1 - (10min)

Create a promise version of the async readFile function

```js
const fs = require("fs");

function readFile(filename, encoding) {
  return new Promise((resolve, reject) => {
    fs.readFile(filename, encoding, (err, data) => {
      if (err) {
        reject("Error occured");
      } else {
        resolve(data);
      }
    });
  });
}
readFile("./files/demofile.txt", "utf-8")
  .then((value) => {
    console.log(value);
  })
  .catch((error) => {
    console.error(error);
  });
```

# Question 2

Load a file from disk using readFile and then compress it using the async zlib node library, use a promise chain to process this work.

```js
const fs = require("fs");
const zlib = require("zlib");

function zlibPromise(data) {
  return new Promise((resolve, reject) => {
    zlib.gzip(data, (error, result) => {
      if (error) {
        reject(error);
      } else {
        resolve(result);
      }
    });
  });
}

function readFile(filename, encoding) {
  return new Promise((resolve, reject) => {
    fs.readFile(filename, encoding, (err, data) => {
      if (err) reject(err);
      resolve(data);
    });
  });
}

// Quiz #2
// readFile("./files/demofile.txt", "utf-8")
//   .then((data) =>
//     zlibPromise(data)
//       .then((zippedData) => console.log(zippedData))
//       .catch((err) => console.error(err))
//   )
//   .catch((err) => console.error(err));

// Quiz #3 & #4
readFile("./files/demofile.txt", "utf-8")
  .then((data) => {
    return zlibPromise(data);
  })
  .then((data) => console.log(data))
  .catch((err) => console.error(err));
```

# Question 3

Convert the previous code so that it now chains the promise as well.

# Question 4

Convert the previous code so that it now handles errors using the catch handler

# Question 5

Create some code that tries to read from disk a file and times out if it takes longer than 1 seconds, use `Promise.race`

```js
function readFileFake(sleep) {
  return new Promise((resolve, _) => setTimeout(resolve, sleep, "read"));
}
function timeout(sleep) {
  return new Promise((_, reject) => setTimeout(reject, sleep, "timeout"));
}

Promise.race([readFileFake(5000), timeout(3000)])
  .then((data) => console.log(data))
  .catch((err) => console.error(err));
```

# Question 6

Create a process flow which publishes a file from a server, then realises the user needs to login, then makes a login request, the whole chain should error out if it takes longer than 1 seconds. Use `catch` to handle errors and timeouts.

```js
function authenticate() {
  console.log("Authenticating");
  return new Promise((resolve) => setTimeout(resolve, 2000, { status: 200, msg: "Published" }));
}

function publish() {
  console.log("Publishing");
  return new Promise((resolve) => setTimeout(resolve, 2000, { status: 403, msg: "Unauthorized" }));
}

function timeout(sleep) {
  return new Promise((resolve, reject) => setTimeout(reject, sleep, "timeout"));
}

const savePublish = () => {
  return publish().then((result) => {
    if (result.status == 403) {
      return authenticate();
    } else {
      return Promise.resolve("Published");
    }
  });
};

Promise.race([savePublish(), timeout(3000)])
  .then((data) => console.log(data.msg))
  .catch((err) => console.error(err));
```
