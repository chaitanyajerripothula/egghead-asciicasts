Kent C. Dodds: [00:00] As hard as you work on your application, **sometimes unexpected things will happen**. I don't expect people will be throwing errors in the `body` of your function, but they could be calling functions that don't actually exist, which will result in an error. Let's take a look at how you can manage these with React.

[00:16] Here we have some `explode` state where we initially that to `false`. Then we have a `button` where when you click on it, it changes that explode state to `true`. Then we all surrender this `div` where if there is some explode state, then we'll render that `bomb`, which will trigger this error.

```js
function App() {
  const [explode, setExplode] = React.useState(false);
  return (
    <div>
      <div>
        <button onClick={() => setExplode(true)}>💣</button>
      </div>
      <div>{explode ? <Bomb /> : 'Push the button Max!'}</div>
    </div>
  );
}
```

[00:33] If we take a look at what things look like right now, we click on the `bomb`. Our entire application goes away. We get this `uncaught error`, `kaboom`, in our console. If we scroll down here a little bit, React actually logs out a component stack trace so we can track down which component threw this `error`.

[00:52] We have our `app`. That is rendered right here. Then we have this `div`, which is rendered here. Then we have this `div`, which is rendered here. Then we have this `bomb`, which is rendered here. That `bomb` is the thing that threw the `error`. Then it's giving us this tip, consider adding an error boundary to your tree to customize error-handling behavior.

That's just what we're going to do.

[01:14] Normally, I use a third-party library for this, but we're going to build our own little error boundary. Error boundaries have to be class components. To create a class component with React, we're going to say:

```js
class ErrorBoundary extends React.Component {}
```

[01:30] In the body of our React class component, we're going to need a `render method`. This is what's going to be rendered. It's basically the same thing as the `body` of our regular function components.

```js
class ErrorBoundary extends React.Component {
  render() {}
}
```

[01:42] Here we're actually not going to render anything special with this error boundary. We're simply going to `return this.props.children`. The React elements we return for this error boundary are going to be the same React elements that are provided to this error boundary as children.

[01:57] Let's go ahead and put those right here, inside of our second `div` in our `App` function. We'll say, `<ErrorBoundary> {explode ? <Bomb /> : 'Push the button Max!'} </ErrorBoundary>` paste in what we had before, and then close off that error boundary. If we save this, we get a refresh. When I click on this `bomb`, we get the exact same behavior that we had before. Now let's make our ErrorBoundary handle that `error`.

[02:17] The first thing that we're going to need is some state. We'll say `error` is `null`. Then we'll have a static method called `getDerivedStateFromError` that'll accept an `error`. Then it'll return the state change that we want to make based on this `error`. We'll just return an object that has an `error` property. That's going to be assigned to the `error` that we're getting for this static method.

```js
class ErrorBoundary extends React.Component {
  state = { error: null };
  static getDerivedStateFromError(error) {
    return { error };
  }
  render() {
    return this.props.children;
  }
}
```

[02:40] When this happens, we're going to get a re-render. Let's grab that `error` from our state. Then we can say if there's an `error`, then we'll return a `div` that says, `Oh no!`

```js
render() {
  const {error} = this.state
  if (error) {
    return <div>Oh no!</div>
  }
  return this.props.children;
}
```

Let's save that. When we click this, we're going to see, `Oh, no!`.

[03:00] We'll still see `errors` logged to the console for our benefit, but the application won't completely crash. React will simply re-render this error boundary with the error that was thrown. The error boundary gets to control what's being rendered.

[03:13] I'm going to go ahead and make a function component below our `ErrorBoundary` called `ErrorFallback`, which is going to be my generic fallback component for this application. We'll make this except a `prop` called `error`. Then we'll return a `div` that has a `p` tag with `something went wrong` and then a `pre` tag with the `error` message.

```jsx
function ErrorFallback({error}) {
  return (
    <div>
      <p>Something went wrong:</p>
      <pre>{error.message}</pre>
    </div>
  )
}
```

[03:40] As the `user` of this error boundary component, I want to be able to provide to the error boundary the `FallbackComponent` I want it to render when there's an `error`. I'm going to provide it the prop `FallbackComponent={}`. Here I'll provide that component that I just created, this `errorFallback`. Paste that right in there.

```js
<ErrorBoundary FallbackComponent={ErrorFallback}>
  {explode ? <Bomb /> : 'Push the button Max!'}
</ErrorBoundary>
```

[04:01] For our error boundary to accept and render that, we're going to have access to that `FallbackComponent` on `this.props`. We can say, <this.props.FallbackComponent error={error} />. We'll provide the prop `error` as `{error}`.

```jsx
render() {
  const {error} = this.state
  if (error) {
    return <this.props.FalbackComponent error ={error} />
  }
  return this.props.children
}
```

[04:18] You may recall that rendering custom components requires that the first character you use is capitalized, but the compiler also has a rule that **if there are dots included in the component name, then those will also be treated as custom components**, which is why this works.

[04:34] We save this. We push the button. We're going to get `Something went wrong. Kaboom`. The app is still working. We get all of the information logged to the console as well.

[04:45] As I said, I never use class components, even for error boundaries, because I prefer to use a third-party library, which I'm going to include now. That is called **React Error Boundary**. With the React Error Boundary UMD `export`, it exposes a global variable called `ReactErrorBoundary`. 

```html
<script src="https://unpkg.com/react-error-boundary@1.2.5/dist/umd/react-error-boundary.js"></script>
```

It has a property on there called `ErrorBoundary`. I'm going to simply assign that to `ErrorBoundary`.

```js
const ErrorBoundary = ReactErrorBoundary.ErrorBoundary
// class ErrorBoundary extends React.Component {
//   state = {error: null}
//   static getDerivedStateFromError(error) {
//     return {error}
//   }
//   render() {
//     const {error} = this.state
//     if (error) {
//       return <this.props.FallbackComponent error={error} />
//     }

//     return this.props.children
//   }
// }
```

[05:11] You'll be pleased to know that I don't have to change any of my code for this to work. It'll continue to work because we basically built a simple version of the error boundary from this library. **The library is doing a fair bit more than what we're doing here, which is why I recommend you do that instead, but the API is exactly the same.**

[05:30] One other thing I want to demonstrate here is that this `<ErrorBoundary>` can be rendered anywhere in the tree, but the location of the error boundary has a special significance.

[05:38] The error boundary can handle any errors that are thrown by its descendants, but it's also important to note that the error boundary is going to render something in place of all of its descendants when there is an error.

[05:50] That means that while we can see the `bomb` still in our browser and the rest of our application, if we were to move this error boundary up to encompass our entire app and save that, when we click this button, then the entire app is replaced by our `ErrorFallback` component.

```js
<ErrorBoundary FallbackComponent={ErrorFallback}>
  <div>
    <div>
      <button onClick={() => setExplode(true)}>💣</button>
    </div>
    <div>{explode ? <Bomb /> : 'Push the button Max!'}</div>
  </div>
</ErrorBoundary>
```

[06:08] This may or may not be desirable based on the specific use case, but **it could be useful to have one error boundary rendered at the top level and then other error boundaries rendered throughout your application** with more specific error fallbacks.

[06:22] Here we could have `Something went wrong there`. That error is going to be handled by the nearest error boundary here. If something went wrong somewhere else in our application, then this top-level error boundary could handle that error. This gives us some fine-grained control over what part of the tree is going to be replaced by what's rendered by our error boundary.

[06:43] Another important thing to note is that our error boundary can only handle certain errors, specifically errors that are happening within the React call stack.

[06:51] **It won't handle errors that are happening in event handlers** or if there's an error in an asynchronous callback, like a promise handler. It will only handle errors that happen within a React call stack, like the render method or a React `useEffect` callback.

[07:06] In review, to handle React errors, you need to create an error boundary, or, as I recommend, use React Error Boundary from npm. With this error boundary, you can wrap parts of your code. Any descendants of that error boundary will have its errors handled by the error boundary.

[07:23] With the error boundary from the React Error Boundary library, you can provide a `FallbackComponent`. That `FallbackComponent` will be rendered in the event of an error allowing you to recover from the error or simply display an error message for the user to read.