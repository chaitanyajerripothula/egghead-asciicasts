Kent C. Dodds: 0:00 Using React to create elements in this way works out just fine, but it's not entirely ergonomic. It's not the way that most of the community creates React elements. **Most of the community is using JSX, which is an HTML-like syntax in our JavaScript**.

0:16 If I were to create this same React element using JSX, I would make it like this. We'll say `element = <div>`. Our children are "Hello World". Then we had a `className`, which is a prop. That'll go as an attribute on our div here, with `className="container"`.

```js
<body>
  <script type="text/javascript">
    const rootElement = document.getElementById('root')

    const element = <div className="container">Hello World</div>
    ReactDOM.render(element, rootElement)
  </script>
</body>
```

0:34 If we save that, we'll get a refresh. We'll get a white screen here. **That's because we have a syntax error in our JavaScript. That's because `element = <div>` is not JavaScript code. This is JSX**. The browser does not understand this natively. It needs to be compiled from this to something that the browser can understand. That's where Babel comes in.

0:54 **Babel is a JavaScript compiler supporting the next generation of JavaScript as well as non-standard features like JSX**. If we go to this ["Try It Out" page](https://babeljs.io/en/repl) and then go over here and copy our code and paste it in here, 

```jsx
const element = <div className="container">Hello World</div>
```

then we'll see that our code is being compiled to something that's very familiar to us.

1:13 React.createElement. The type is div. Here are the props, `className` is "container", then all of the children come hereafter. I would recommend that you spend some time playing around in this tool. Understanding how JSX is compiled will make you more effective at using JSX.

1:29 **In a typical application, you're going to be using a tool that will use Babel to compile JSX to JavaScript for you**. For our purposes, we're going to use Babel in the browser to get this compiling our JavaScript right here without having to install any other tools.

1:44 I'm going to add a `script` tag here to include Babel standalone. That will compile any script tag that has the type of `text/babel`. Then it will create a new script tag with the compiled code with the type as text/JavaScript so that the browser can evaluate it.

```js
<body>
  ...
  <script src="https://unpkg.com/@babel/standalone@7.8.3/babel.js"></script>

  <script type="text/babel">
    const rootElement = document.getElementById('root')
    const element = <div className="container">Hello World</div>
    ReactDOM.render(element, rootElement)
  </script>
</body>

```

2:00 With that, if I save this, we'll get Babel downloaded. It will compile our JavaScript and allow the browser to evaluate it so we can use JSX in the browser. **We'll also get this warning that we're using the in-browser Babel transformer, which is not something that you'd want to do in production, but it makes it easy for us to iterate quickly right here in this `index.html` file**.

2:20 If we take a look at the elements, we're going to see the code that we wrote in this `text/babel` script. Then if we go to the head, we'll see a new script tag in here which is the compiled version of our code.

2:31 In review, what we wanted to do here was make it so that we can author our UI without using the React createElement API and instead use JSX, which is an HTML-like syntax for authoring UI.

2:44 To do this, we need a compiler like Babel. We installed Babel standalone, which will compile our code on the fly in the browser. We also had to update our type to `text/babel`. Babel will look for all scripts that have that type, compile the code that's inside them, and insert a new script that has the compiled code so the browser can evaluate it.

3:05 In a typical application, you will not be using Babel standalone. Instead, you'll be using another tool which probably is using Babel to compile your code. That way, you get a much more friendly syntax for authoring your UI with React.