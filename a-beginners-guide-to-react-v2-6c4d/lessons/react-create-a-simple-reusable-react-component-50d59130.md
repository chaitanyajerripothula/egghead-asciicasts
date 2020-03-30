Kent: [00:00] So, here we have this div with the class name `container`, and we have this div with the class name `message` in here that says "Hello, World!"

[00:06] Let's say that I wanted to have, I don't know, maybe two of those. So now we have "Hello, World!" and "Hello, World!" and then I don't want to repeat myself. So I'm going to make a constant message here that is that "Hello, World!" and we'll put that in place of here "message," just interpolate that there. Now I'm not repeating myself, which is good.

```js
const message = <div className="message">Hello World</div>
const element = (
  <div className="container">
    {message}
    {message}
  </div>
)

ReactDOM.render(element, document.getElementById('root'))
```

[00:27] But what if I wanted one of these to say, "Hello, World!" and the other to say, "Goodbye, World!" Well, we need to parameterize this somehow, and when we do that in JavaScript, we create a function. I'll make an arrow function here, and we'll accept an object. We'll call it `props`, and then whatever that `props.msg` or props.message value is, is what we'll interpolate into the children prop for this div that we create.

[00:52] Now, instead of just putting "message" in there directly, we're going to call it with an object, and we'll use msg as "Hello, World!" and then we'll do this one msg "Goodbye, World!" Save that. And we get "Hello, World!" and "Goodbye, World!" which is awesome.

```js
const message = props => <div className="message">{props.msg}</div>

const element = (
  <div className="container">
    {message({msg: 'Hello World'})}
    {message({msg: 'Goodbye World'})}
  </div>
)

ReactDOM.render(element, document.getElementById('root'))
```

[01:10] But again, this is not really ergonomic. It doesn't look very good, and one of the benefits to using JSX is the ability for our UI to resemble the declarative nature of HTML.

[01:21] What I'd really like to be able to do is say "message" here and then "Hello, World!" and "message." But if I try to do that, you'll notice we actually do get the "Hello, World!" but we're going to get a console warning here saying, "The tag message is unrecognized in this browser. If you meant to render a React component, start its name in an upper case letter."

[01:42] Oh, that's interesting. Thank you very much. So let's learn more about that.

[01:46] If we look at the compiled version of our code, we're going to see our message function that's returning a React element, that's creating a div. And then if we come down here, we're going to see `React.createElement` message as a string.

[01:57] That's problematic because we don't want to have the message as a string to create a DOM element that is a message type element. We actually wanted to use this function, so that we can use this function to create additional React elements.

[02:11] So, as the warning suggests, we need to use an upper case letter, and so if instead, we say capital "Message," then we use capital "Message" here, and let's make sure we don't get errors here. So we'll say capital "Message" for these, and we can save that.

```js
const Message = props => <div className="message">{props.msg}</div>

const element = (
  <div className="container">
    <Message>Hello World</Message>
    {Message({msg: 'Hello World'})}
    {Message({msg: 'Goodbye World'})}
  </div>
)

ReactDOM.render(element, document.getElementById('root'))
```

[02:25] Now, we're no longer getting that warning. If we look at our compiled code, we're going to see that capital "Message" here, and when we get past to `React.createElement`, we're going to get a capital Message right here, outside of a string.

[02:38] So, we're passing a reference to the function directly into the `React.createElement`. And then if we ultimately look at what's rendered to the DOM, we come down here, we see our ID of "root", our container, and then we see a div with the class of message.

[02:52] Now, let's take a look at what this message element really is. It's console.log that `Message` element. We'll look at our console, and we'll see that it is a React element, and its type instead of being a div like we had before, let's take a look at that to compare, console.log div "Hello, World!" Save that and we'll get two console logs, so the first one being our div. The type is a string of div, whereas the type here is our `Message` function.


```js
console.log(<div>Hello World</div>)
console.log(<Message>Hello World</Message>)
```

[03:19] Here we get the props of `children: "Hello World"` and we have `children: Hello World"` in this one as well.

[03:25] **So, when you give a capital letter in JSX, that's the same as calling `React.createElement` with the function that you're referencing**. That allows you to create a custom component that you can use to reuse code over and over again.

[03:39] Let's go ahead and cut this out, and we'll rewrite this using JSX. We'll say message, and then use the msg prop, because that's what we're expecting. And then we'll come back here, save that, and everything's working without any errors.

```js
const Message = props => <div className="message">{props.msg}</div>

const element = (
  <div className="container">
    <Message msg="Hello World" />
    <Message msg="Goodbye World" />
  </div>
)
```

[03:55] If we look at our compiled version of our code, then we'll see that we're calling `React.createElement` with that `Message` function.

[04:03] If we wanted to make it nice and declarative so that we could nest to these things as if it's the `children` prop, then, we just accept children for the name of the prop.

[04:12] Then we can rename this one to "children," and those two are functionally equivalent. If we save this, then we'll still get "Hello World!" and "Goodbye, World!" But honestly, one of those looks a little bit nicer to me, so I'm going to switch both of these to use that syntax.

```js
const Message = props => <div className="message">{props.children}</div>

const element = (
  <div className="container">
    <Message>Hello World</Message>
    <Message children="Goodbye World" />
  </div>
) 
```

[04:26] What's cool about this is I'm also able to nest these together and pass anything that React can render, which includes the string here, or additional components here, or additional elements here.

```js
const element = (
  <div className="container">
    <Message>
      Hello World
      <Message children="Goodbye World" />
      <span>Hey there</span>
    </Message>
  </div>
) 
```

[04:40] In review of what we did here was we had some code that was being duplicated, and we wanted to reuse that code in multiple places. So, we created our own custom function component which is a function that accepts a props object and returns more React elements.

[04:54] We had to make sure that our function component started with a capital letter so that Babel would compile this to pass the function itself to React.createElement rather than the string message to React.createElement. That way, when React renders our element, it knows what function to call.