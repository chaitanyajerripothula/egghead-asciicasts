Chris Biscardi: 00:01 **Now that we have our site deploying, we'll create our first function.** This function will be the function that generates the OpenGraph image using [Playwright](https://github.com/microsoft/playwright). We'll create a functions directory and inside of that directory, create a directory that will house our function.

```bash
mkdir gen-opengraph-image
cd gen-opengraph-image
```

00:18 Inside of `gen-opengraph-image`, we need to initialize a new `package.json`. Our function needs to be in a file named the same as the directory. We'll create a `gen-opengraph-image.js`. We'll start in CommonJS syntax by exporting the name handler. Handler will be an `async` function that takes two arguments, the `event` to our function and the `context`.

```js
exports.handler = async function (event, context) {}
```

00:40 These variables together contain things like the `environment` or the `body` of a request that got sent to this function. Notably, this will also include the query `string`, which we'll use later to change how the image that we capture is rendered.

00:52 Because this is an `async` function, we can just return an `object` that has the `statusCode: 200` and the `body` of the response. In this case, the `body` will be `"gen function works"` as a string.

```js
exports.handler = async function (event, context) {
  return {
    statusCode: 200,
    body: 'gen function works',
  }
}
```

01:03 **We can commit these files and push them. This triggers a new build while we work.** We can now see that instead of new functions to upload, we have 1. If we go to the functions tab, and we click on our function, we can see the `URL` that we have to hit. We can either hit that `UR`L in the browser using the `URL` bar, or we can run `curl` in our terminal.

```
culr https://relaxed-payne-d1dfbe.netlify.com/.netlify/functions/gen-opengraph-image
```

01:23 You could also use any `REST` client for now. Soon, we'll be returning an image though, and **not all `REST` clients support returning images or displaying them.**
