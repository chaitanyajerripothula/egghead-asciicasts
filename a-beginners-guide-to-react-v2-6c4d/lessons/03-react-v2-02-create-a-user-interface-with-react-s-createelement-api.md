Kent C. Dodds: [00:00] To use React to create this user interface, we're going to need to have React on the page. We can get React from npm, but there's a service called unpackage that we're going to use. If I go to [unpkg.com](https://unpkg.com/) that will show me how to use unpackage to get any file that is distributed on npm. It has these files for React. If I go to that file, then I'll see a minified version of React.

[00:25] Let's go ahead and add some `script` tags to our page here so that we get React on the page. With React and ReactDOM both on the page, I can now create React elements and then use ReactDOM to render those elements to the page.

```html
<script src="https:/unpkg.com/react@16.12.0/umd/react.development.js"></script>
<script src="https:/unpkg.com/react-dom@16.12.0/umd/react.development.js"></script>
```

[00:41] I'm going to comment all of this out and then with ReactDOM I'm going to render my element to the root element, but the element that I provide is not going to be a DOM element that I create with the document. Instead, it's going to be a `React.createElement()` that I create with React.


```js
<body>
  <div id="root"></div>
  <script src="https://unpkg.com/react@16.12.0/umd/react.development.js"></script>
  <script src="https://unpkg.com/react-dom@16.12.0/umd/react-dom.development.js"></script>
  <script type="text/javascript">
    const rootElement = document.getElementById('root')
    React.createElement()
    ReactDOM.render(element,rootElement)

    // const element = document.createElement('div') 
    // element.textContent = 'Hello World'
    // element.className = 'container' rootElement.appendChild(element)
  </script>
</body>
```

[01:01] The API is not exactly the same as the one that we get with `document`. We do get to specify the type of element as the first argument, but instead of getting the element and attaching properties to it, we specify those properties on creation as an object.

[01:16] Another difference is instead of text content, we specify `children`. Here we'll specify our children as "Hello World" and then the `className` will be a "container". This is going to give me my element, which I will render with ReactDOM to the root element. If we save this, we'll get a page refresh. You may not have even noticed that because it's rendering exactly the same thing that we had before.


```js
<body>
  <div id="root"></div>
  <script src="https://unpkg.com/react@16.12.0/umd/react.development.js"></script>
  <script src="https://unpkg.com/react-dom@16.12.0/umd/react-dom.development.js"></script>
  <script type="text/javascript">
    const rootElement = document.getElementById('root')
    const element = React.createElement('div', {
      children: 'Hello World',
      className: 'container'
    })
    ReactDOM.render(element, rootElement)
  </script>
</body>
```

[01:44] If we come down here to root, _(inside the dev tools elements tab)_, we'll see our div with a class container and the text content "Hello World". We'll also see those two script tags that we added to add React and ReactDOM onto the page then our own JavaScript that we wrote to create this user interface.

[02:00] Let's take a look at what this element is. If it's not a DOM node, then what is it? We'll `console.log(element)`, save that, and then we'll come over here to our console. We'll see that we have a `$$typeof` property. That's a symbol and that's something for React to know that this object is a legitimate React element.

[02:19] We know that the type is a div and there are a couple other properties that are used by React internally. Then this `props` object is what we passed as our second argument here with the `children` and the `className`.

[02:31] There's something interesting about this `children` prop that I want to show you here. You can write this same thing as a third argument to `React.createElement()` by simply providing the value for the children prop. Here we can provide "Hello World". Those are functionally equivalent.

```js
const element = React.createElement(
  'div', 
  {
    className: 'container',
  }, 
  'Hello World'
)
```

[02:51] If we look at the React element here, when you look at our props, and we still are getting children is "Hello World". Then if you provide another argument, that will be an additional child. We can say ", Goodbye World". Save that, we'll get a refresh, and we see "Hello World, Goodbye World", and we get our element's props to be children as an array.

```js
const element = React.createElement(
  'div', 
  {
    className: 'container',
  }, 
  'Hello World',
  ', Goodbye World'
)
```

[03:14] We can continue going on through children forever and ever. Or we could take these and use the children prop directly like this, specifying it as an array ourselves. You'll see that the result here is the same.

```js
const element = React.createElement('div', {
  children: ['Hello World', ', Goodbye World'],
  className: 'container' 
})
```

[03:30] This also gives us the flexibility of creating additional React elements to be children. In `children` I could say `React.createElement("span")`. We could say we want no props for this span, and we want the children to be "Hello" and "World".

[03:47] We have the type of span, the props are null, the children are an array of "Hello" and "World". 

```js
const element = React.createElement('div', {
  children: React.createElement('span', null, 'Hello', 'World'),
  className: 'container' 
})
```

If we expand our dev tools, we'll see props, children, and children is just another React element that itself has props. And that children is an array of two strings.

[04:05] In review, to create React elements and render them to the page, you need to include React and ReactDOM, React for creating the elements and ReactDOM for rendering those elements to the page.

[04:16] Then we still need to get access to some element that's on the page so we can render our elements to that DOM node. Then we create our React elements and we use ReactDOM.render to render our element to the root DOM node.