Kent C. Dodds: 0:00 If we wanted to create a variable for "Hello World" and we wanted to interpolate that variable's value into the children position, then I'm going to go ahead and copy this. We'll make this our children.

0:12 Then we'll come in here and to interpolate we're going to use curly braces and put children in here. **Anything you put between these two curly braces can be a JavaScript expression**. Whatever that expression evaluates to is the value that gets placed into this position for the `React.createElement` call.

```html
<script type="text/babel">
  const rootElement = document.getElementById('root')
  const children = 'Hello World'
  const element = <div className='container'>{children}</div>
  ReactDOM.render(element, rootElement)
</script>
```

0:30 We can do the same thing for our className. If I copy the container and make a `className` variable, and instead of the quotes here, we'll put curly braces to suggest to the Babel compiler that we want this value to be evaluated as an expression, then we'll pass the `className` variable. If we save this, then we'll get the exact same result as we had before.

```html
<script type="text/babel">
  const rootElement = document.getElementById('root')
  const children = 'Hello World'
  const className = 'container'
  const element = <div className={className}>{children}</div>
  ReactDOM.render(element, rootElement)
</script>
```

0:53 You can see the compiled version of the code here in our script tag where you see we have our children as a variable, our className as a variable. Our element is an assignment to `React.createElement` with our div. Then our object where we have `className` being assigned to the variable `className`.

1:09 We can rename this to whatever we want. We could say `myClassName` and then copy this and paste it in there. If we save this, we'll get a refresh. There it is, `myClassName` is being assigned to the className property of our object here.


1:24 Then children is the same thing. We could say `myChildren`. Save that. We'll get a refresh and there it is, `myChildren` being passed as my children. When you use the curly braces, that's basically telling Babel, "Leave this alone and pass it directly as the property that you pass for the props here and for the children arguments here."

```html
<script type="text/babel">

  ...

  const myChildren = 'Hello World'
  const myClassName = 'container'
  const element = <div className={myClassName}>{myChildren}</div>
  ReactDOM.render(element, rootElement)
</script>
```

1:47 If we wanted to split these up, then we could do so with worldChild and do worldChild. Put them side by side just like that and we'll get `myChildren` and `worldChild`. Except to get it to render you need to spell things correctly. That's important in programming. There we go.

```html
<script type="text/babel">

  ...

  const myChildren = 'Hello'
  const worldChild = ' World'
  const myClassName = 'container'
  const element = <div className={myClassName}>
    {myChildren} 
    {worldChild}
  </div>
  ReactDOM.render(element, rootElement)
</script>
```

2:08 This allows us to be expressive with the way that we're building our UI. If we want to add another React element in here, all we need to do is add a . We can say "Hello". Then we could do a "World". Then get rid of these _(`myChildren` and `worldChild` variables)_, save that, and here we go. We get "Hello World". Babel is managing, compiling that down to JavaScript that the browser can execute using React APIs.

```html
<script type="text/babel">

  ...

  const myClassName = 'container'
  const element = <div className={myClassName}>
    <span>Hello</span> <strong>World</strong>
  </div>
  ReactDOM.render(element, rootElement)
</script>
```

2:37 There's another useful JSX trick that I want to show you. Let's come back to our children: 'Hello World'. We'll just call this className. We'll get rid of this. We'll change this to className. Then we'll interpolate children here.

```html
<script type="text/babel">

  ...

  const children = 'Hello World'
  const className = 'container'
  const element = <div className={className}>{children}</div>
  ReactDOM.render(element, rootElement)
</script>
```

2:56 **One important thing to remember is that children is just a special prop that we could provide right here**. Children is children. With that it works exactly the same way as it did before, except now instead of being compiled to add additional arguments, it's just using the shortcut here as one of the props.


3:16 In addition, because this is JSX and not HTML, we can have self-closing tags for divs. We can save that, and this is self-closing.

```html
<script type="text/babel">

  ...

  const children = 'Hello World'
  const className = 'container'
  const element = < className={className} children={children} />
  ReactDOM.render(element, rootElement)
</script>
```

3:24 Another thing we can do is if I make an object here where we have our props, we're going to make children and className part of those props. Then I can come right here, remove all of those, and I can say, "Hey, Babel, I want you to take all of these props and pass them to the React.createElement call that you create for this div." Interpolate onto this div's prop's position a spread of that prop's object.

```html
<script type="text/babel">
    
    ...

    const children = 'Hello World'
    const className = 'container'
    const props = {children, className}
    const element = <div {...props} />
    ReactDOM.render(element, rootElement)
</script>
```

3:50 If we save that, we can take a look at the compiled version of this. We get our props object. We have `React.createElement`. Then it just passes the props directly. Then I can add additional props if I want to. I could say `id="app-root"`, save that. Then in this case we're going to get the extends helper, which is basically object.assign.

4:10 Then Babel is going to extend the props that we provided with the props that we're spreading. We get a single combined object for the second argument of the `React.createElement` call. Because of the way that `object.assign` works, if we wanted to override one of the properties in this props object, then we could do so simply by providing it after we spread those props in the props position for this element.

4:35 We can say `className="not-container"`. Save that. Then if we look at the compiled version, we'll see our extends for the props that come before the spread. Then we'll see that object and then we'll see another object for the props that come after the spread.

```html
<script type="text/babel">
  const rootElement = document.getElementById('root')
  const children = 'Hello World'
  const className = 'container'
  const props = {children, className}
  const element = <div id="app/root" {...props} className="not-container" />
  ReactDOM.render(element, rootElement)
</script>
```

4:51 Then if we look at the results under our root, we'll see we have the id of `app-root` and the class of `not-container`. App root because it's not getting overwritten, so it appears as is.

5:04 We get the children as they are because the children are not being overwritten, but then we get the className of `not-container` because we are overwriting the className that's in the props which comes before a className. **Whatever comes last is the winner in the event of a conflict like we have here**.

5:22 In review, what we learned here is that you can interpolate values with these curly braces by putting any expression between the curly braces and have that expression passed along to the `React.createElement` API.

5:34 We also learned that you can spread props in the props position of a JSX element and those props will be combined with the other props that are provided to that element in a declarative and deterministic way.