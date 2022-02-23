# Question 1

Create a custom async generator that loops over the files that are passed in.

```js
const util = require("util");
const fs = require("fs");
const readFile = util.promisify(fs.readFile);

function* fileLoader(files) {
  const promises = files.map((item) => readFile(item, "utf8"));
  for (const promise of promises) {
    yield promise;
  }
}

(async () => {
  for await (let contents of fileLoader([
    "./files/demofile.txt",
    "./files/demofile.other.txt",
  ])) {
    console.log(contents);
  }
})();
```
