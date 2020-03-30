Kent C Dodds: [00:00] We have an `App` component that is rendering some JSX here for there have been events, click me, you typed, and then an input here. We have some state that is running this application component. 

```html
<script type="text/babel">
  const rootElement = document.getElementById('root')

  const state = {eventCount: 0, username: ''}

  function App() {
    return (
      <div>
        <p>There have been {state.eventCount} events.</p>
        <p>
          <button>Click Me</button>
        </p>
        <p>You typed: {state.username}</p>
        <p>
          <input />
        </p>
      </div>
    )
  }
  ...
</script>
```

If I update the event count to 10, then we're going to see there have been 10 events.

[00:16] If I update the username to 'Hello there' and save that, then we're going to see you typed Hello there. The value of the input isn't getting updated and there have actually not been 10 events, so let's go ahead and wire up things so we can update our state and re-render the app.

[00:32] We already have a function at the bottom called setState, which will update our state variable and then render the app. That will just call React.render on our root again. 

```js
function setState(newState) {
  Object.assign(state, newState)
  renderApp()
}

function renderApp() {
  ReactDOM.render(<App />, document.getElementById('root'))
}

renderApp()
```

This is not typically how you re-render an application or manage state in a React application, but this will work for our purposes of learning how events work in React.

[00:51] First let's deal with this button. We have on button's an `onClick` event that we can call. This we're going to provide a function. I can make this an inline arrow function here.

[01:03] We'll just say `setState`, calling our setState function down here with the new state of an object, eventCount. We want that eventCount to be the current `state.eventCount + 1`. 

```html
<button onClick={() => setState({eventCount: state.eventCount + 1})}>Click Me</button>
```

If we save that and then I go over to our browser and click the button, we're going to get an increment every single time I click.

[01:21] There are a lot of different events that we can use on our button. We can have an `onFocus` and save that and every time I focus the input we're going to get an increment on that event. Or we could say `onMouseOver`, save that, and then every time I mouse over, we'll get an increment on that event count. There are a lot more events that are supported by React.

[01:43] Let's set this back to `onCount` and then we can also extract this function to `handleClick`. We could put that right above our return. `function handleClick()` and then we'll call `setState` in there. 

```js
function App() {
  function handleClick() {
    setState({eventcount: state.eventCount + 1})
  }
  ...
  <button onClick={handleClick}>Click Me</button>
}
```

That will work just as well.

[02:03] Then let's take a look at this input. We want this to get updated when we enter some new username. I'm going to say `onBlur` and we'll say `setState({username: ''})`. Then we need to specify a value here.

[02:18] What we need is to get the value of the input that is receiving the onBlur event. As an argument to the event handler, we receive the `event`. Then we can use that event to get the `target` of the event, which will be our input. Then we'll get that `value` from the input.

```html
<p>
  <input onBlur={event => setState({username: event.target.value})} />
</p>
```

[02:36] If we save that, then we can type in Kent C Dodds and then click out of the input. Our state gets updated. If we want this to get updated as we're typing, then we can change `onBlur` to `onChange`. As the user types in this input, we'll get that update to our state and we'll re-render the application.

[02:54] Let's go ahead and pull this `onChange` handler out. We'll call it `handleChange`. We'll bring it up just below our `handleClick`. `function handleChange` will take the `event`. Let's go ahead and take a look at that event. We'll say console.log(event), save that.

```js
function handleChange(event) {
  console.log(event)
  setState({username: event.target.value})
}
```

[03:10] We'll pop open our Dev Tools here, look at the console, and then type in one character. We'll notice we get a synthetic event object. That's not the native event. The native event is this property, `nativeEvent: inputEvent`. React does some serious performance optimizations for our events and that's what this synthetic event thing is all about.

[03:33] Most of the time you don't need access to the native event, but if you ever do then you can say `event.nativeEvent` and we'll hit save. 

```js
function handleChange(event) {
  console.log(event.nativeEvent)
  setState({username: event.target.value})
}
```

We'll type in a character and we'll get that native input event. But the synthetic event has pretty much all the same properties as a regular event, so that's what you're typically going to use.

[03:54] One thing I like about React's event system is that you pass the event handler directly to the element that you want to attach the event to, so it makes following the flow of events and state updates really straightforward.