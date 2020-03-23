Kent C. Dodds: [00:00] Let's say that instead of rendering a div with the class name `container` and then "Hello, World," we wanted to render two spans side by side, one that says "Hello" and the other that says "World." Let's first do this with `React.createElement`, and then we'll see how we could do this with JSX.

[00:15] I'm going to make my element `React.createElement` span. We don't need any props. Then we'll say, "Hello." We'll need another element. We'll call this our `worldElement`. Let's call this one our `helloElement`. This one will say, "World."

[00:36] Then we need to render those side by side. I don't know how we do that. Let's say `helloElement` and `worldElement`. No, it doesn't look like that's going to work very well.

[00:51] How do you put two variables side by side and pass them both as the first argument? It's impossible, but if we were to do this in HTML, it would be pretty straightforward. We'd just say Hello and World. That would work just fine.

[01:05] This is straightforward to do in HTML, but it's not possible to do with React, which is why the React team created a special type of element called a React fragment. We can say `React.createElement`. `React.Fragment` is a type. We don't need any props for it.

[01:22] Then we can provide both of our children. Let's put this down here. We'll take our `helloElement`, pass that as the first child, and `worldElement`, pass that as the second child. Then this is going to get our element that we're going to render.

```html
<script type="text/babel">
  const helloElement = React.createElement('span', null, 'Hello')
  const worldElement = React.createElement('span', null, 'World')
  const element = React.createElement(React.Fragment, null, helloElement, worldElement)

  ReactDOM.render(element, document.getElementById('root'))
</script>
```

[01:37] With that, we have the output that we're expecting. We could also add an extra child in here to give us an extra space. Again, nobody wants to use the React.createElement API directly. Let's comment this out and see what this would look like if we wanted to do it with JSX.

[01:53] Our root element is this React fragment. We'll do an open bracket and say `React.Fragment`. We'll close that off with `React.Fragment`. Then we'll want to create a span for hello, so "Hello". Then we'll do a space and then "World" and close that off. If we save this, we're going to get the exact same output, and we're using our `React.Fragment` element.

```html
<script type="text/babel">
  const element = (
    <React.Fragment>
      <span>Hello</span> <span>World</span>
    </React.Fragment>
  )
  ReactDOM.render(element, document.getElementById('root'))
  </script>
```

[02:22] Let's take a look at the compiled JSX here. You notice that we get our element with React.createElement. It's just passing `React.Fragment` as a first argument to `React.createElement`, whereas this, the tag name, is being passed as a string.

[02:36] **The React fragment allows us to put elements side by side without having to have some sort of container element like a div**. This can be useful when you're creating things like tables that have a specific structure to them.

[02:49] **Because this is so common to do, JSX has a special syntax for React fragments. That is to simply remove the React fragment and have an open and closing angle bracket**. This is functionally equivalent to what we had before. This syntax is typically what I use whenever I need React fragments.


```html
<script type="text/babel">
  const element = (
    <>
      <span>Hello</span> <span>World</span>
    </>
  )
  ReactDOM.render(element, document.getElementById('root'))
</script>
```


