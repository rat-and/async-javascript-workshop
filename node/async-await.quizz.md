# Question 1

Convert the promise version of the multi-file loader over to using async/await

```js
const util = require("util");
const fs = require("fs");
const readFile = util.promisify(fs.readFile);

const files = ["./files/demofile.txt", "./files/demofile.other.txt"];

(async () => {
  try {
    let promises = files.map((name) => readFile(name, { encoding: "utf8" }));
    // Promise.all(promises).then((data) => console.log(data));
    let values = await Promise.all(promises);
    console.log(values);
  } catch (err) {
    console.error(err);
  }
})();
```

# Question 2

Again convert the promise version of the multi-file loader over to using async/await but using a custom async iterator with the following syntax

node --harmony-async-iteration <file.js>

```js
const util = require("util");
const fs = require("fs");
const readFile = util.promisify(fs.readFile);

const fileIterator = (files) => ({
  [Symbol.asyncIterator]: () => ({
    x: 0,
    next() {
      if (this.x >= files.length) {
        return Promise.resolve({
          done: true,
          value: this.x,
        });
      }

      return readFile(files[this.x++], { encoding: "utf8" }).then((data) => ({
        done: false,
        value: data,
      }));
    },
  }),
});

(async () => {
  for await (let x of fileIterator([
    "./files/demofile.txt",
    "./files/demofile.other.txt",
  ])) {
    console.log(x);
  }
})();
```
