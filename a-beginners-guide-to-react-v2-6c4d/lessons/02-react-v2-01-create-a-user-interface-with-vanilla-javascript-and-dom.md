Kent C. Dodds: [00:00] To start, we're going to need a `body`. Inside of here, we're going to have a `div` with an ID of `root`. That's where our application is going to be mounted. Then we'll have a script. Our type for our `script` is going to be `text/javascript`. Inside of here, we need to get access to this div. I'm going to make a variable called `rootElement`. That'll `document.getElementById('root')`.

```js
<body>
  <div id="root"></div>
  <script type="text/javascript">
    const rootElement = document.getElementById('root')
  </script>
</body>
```

[00:29] Then I'm going to take this rootElement and append a child. We need to append an element to this. Let's create that `element` with `document.createElement()`. We'll
make it a div. For this element, we want the text content to be "Hello World." Just for fun, we'll have the `element.className` equal "container". We'll save that and get a refresh here. Now we have "Hello, world."

```js
<body>
  <div id="root"></div>
  <script type="text/javascript">
    const rootElement = document.getElementById('root') 
    const element = document.createElement('div') 
    element.textContent = 'Hello World'
    element.className = 'container' 
    rootElement.appendChild(element)
  </script>
</body>
```

[00:56] If we look at the output, we have one thing from that Browser sync, but then here is our application at the root. That's the div we created with the class name of container and the text content of "Hello World." Then here's our JavaScript code that we wrote.

[01:11] In review, to create a user interface in JavaScript, you're going to need to have a place where you append your JavaScript-generated DOM elements. We're going to get access to that element from the document APIs. Then we'll create our own element. We'll add some properties onto that element. Then we'll append that element to our root element.