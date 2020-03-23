Kent: [00:00] We've added a little bit here to simulate another optimization that we want to make. In our `App` function now, we have a count of `state`, and that's just being rendered into this `button`, and every time we click on that button, it re-renders our app component, which triggers a re-render of our greeting component. Every time our greeting component re-renders, this `useEffect` up at the top is going to be called.

```jsx
function App() {
  const [count, setCount] = React.useState(0)
  return (
    <>
      <button onClick={() => setCount(c => c + 1)}>{count}</button>
      <Greeting>
    </>
  )
}
```

[00:20] If we console.log in that useEffect, `'greeting useEffect'`, 

```jsx
function Greeting() {
  const [name, setName] = React.useState(
    () => window.localStorage.getItem('name') | '',
  )

  React.useEffect(() => {
    console.log('greeting useEffect')
    window.localStorage.setItem('name', name)
  })
  ...
}
```

then we'll save that, open up our dev tools, and we see we get the greeting useEffect for the initial render, and every time I click our button, we get another greeting useEffect.

[00:35] But the effect that we're running is updating local storage to set the name to the name value. This side effect, it doesn't actually need to be run, because the name value hasn't changed. We only need to synchronize the local storage value with the name value that we have in memory for the state of this component.

[00:54] React useEffect accepts a second argument as an optimization to combat this problem. That second argument is a dependency array where you pass all the dependencies for your side effect. This is where you pass anything that you want to make sure you synchronize the state of the world with the state of our application.

[01:13] In our case, the only thing that we're trying to synchronize here is the state of local storage, which is the state of the world, with the state of our application, which is the name. For our dependency array, we're going to provide the name.

```jsx
React.useEffect(() => {
  console.log('greeting useEffect')
  window.localStorage.setItem('name', name)
}, [name])
```

[01:27] If we save that, then we'll notice that we get that greeting useEffects called initially. That's because useEffect is always going to be called on the initial mount, and then hereafter, when we click on this button, we're not going to get the greeting useEffect.

[01:44] Then when we update the name value, we're going to get greeting useEffect called, because the name has changed and the state of the world is now out of sync with the state of our application, and so React is going to call our useEffect callback.

[01:58] It's very important that you keep this dependency array accurate according to the dependencies that your callback function relies on. For example, we rely on name, and that's why it's important that we keep this dependency list in here.

[02:11] If you don't keep this list accurate, then you could be missing out on synchronizing the state of the world with state changes that happen in your app, and your users could lose saved work.

[02:21] For example, if I were to remove the name from this array and save that, then we'll get that greeting useEffect called, and then as I type in our input field, we'll notice that the state of my application is being kept updated, but I'm not getting any console.logs.

[02:37] If I refresh, I'm going to be back to where I was before. I, as the user of this application, have lost work, because my code was not properly synchronizing the state of my application with the state of the world outside my application.

[02:51] In an effort to help you avoid making mistakes here, the React team has created an ESLint plugin called [eslint-plugin-react-hooks](https://www.npmjs.com/package/eslint-plugin-react-hooks), which you can use to not only ensure that that dependency array is kept up to date, but actually keep it up to date automatically for you, using ESLint's fix feature.

[03:09] The rule that will help you with this is the `exhaustive-depths` rule. I strongly advice that you use this tool and follow that rule.

[03:17] In review, the problem that we were solving here is our effect callback was being called more than it needed to be. I want to make a special note here that just because it was being called more than it needed to be didn't mean we actually had a bug in our application. This is just an optimization to make our application run a little faster. A dependency array may not be necessary in all cases.

[03:39] In our case, we could simply add this dependency array with the one dependency that our effect callback relied on. This callback is only called when necessary.