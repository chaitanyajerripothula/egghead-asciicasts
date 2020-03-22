Instructor: 00:00 The logic that we have here for storing some state into local storage and keeping it synchronized could be really useful in other areas of our application. Thankfully, React hooks are pretty vanilla JavaScript, and so sharing that logic is just as straightforward as sharing any other logic in JavaScript.

00:16 What we're going to do is make a function. We'll call that `useLocalStorageState`, and then we'll move everything from `const [name, setName]` to `React.useEffect` into that function, and we'll replace them with a call to that function. 

```jsx
function useLocalStorageState(key, defaultValue = '') {
  const [name, setState] = React.useState(
    () => window.localStorage.getItem('name') || '',
  )

  React.useEffect(() => {
    window.localStorage.setItem('name', state)
  }, [name])

  return [state, setState]
}

function Greeting() {
  useLocalStorageState()
  ...
}
```

Then we need to generalize the code that's in our function.

00:33 Instead of a name, it might make more sense to call this `state` and `setState`, and instead of getting the item with the string name, it might make more sense for the user of this function to provide us a key for local storage, so we'll accept a parameter called `key`.

00:51 Instead of this as a default value, it might make sense for people to provide us their own `defaultValue`, so we'll accept that as another parameter. We can default that to an empty string, just in case they don't want to provide it.

01:05 Then we'll replace that with the default value. We'll replace the string name with the parameter that we accept called `key`, and then let's make sure we update these two references to that old state value as well.

01:19 Then, when we call `useLocalStorageState`, we're going to need to get access to that stateUpdater function and to the state value itself, so let's `return [state, setState]`. We can make our custom hook have a similar API to useState, so it's familiar to people who are used to the useState hook.

```jsx
function useLocalStorageState(key, defaultValue = '') {
  const [state, setState] = React.useState(
    () => window.localStorage.getItem(key) || defaultValue,
  )

  React.useEffect(() => {
    window.localStorage.setItem(key, state)
  }, [state])

  return [state, setState]
}
```

01:39 Then we can come down to `Greeting`, and we can get our `name` and our `setName` from `useLocalStorageState()`. We'll specify our key as `name`, and then we're done. 

```js
function Greeting() {
  const [name, setName] = useLocalStorageState('name')
}
```

That's a pretty straight-up refactor from the code that we had before into a custom function.

01:57 If we type in here Chuck and then refresh, we'll see that Chuck is still in there. One thing I want to call out here is that we prefaced our function with the word 'use'. That's a convention and not required. We could change `useLocalStorageState` to whatever we want, save that, and things will work just as well.

02:16 We can hit refresh, and there we have it. The reason that we followed this convention is because the ESLint plugin React hooks is able to enforce some of the same recommendations and rules on our custom hooks as it is on the built-in hooks, because it acts under the assumption that functions that begin with 'use' are custom hooks.

02:39 In review, what we did here is we took some code that was in our function component, we moved it into its own function, and then called it from our function component.

02:48 This was no different from any other regular JavaScript refactor, which makes it really easy to share logic across component, and even make open source libraries that expose custom hooks like this.

02:59 We also learned the importance of using the use prefix on our custom hooks so that we follow the community convention, and the ESLint plugin can help us avoid mistakes. Incidentally, one such mistake is happening right now, because in this index.html file, I can't have ESLint running to show me the mistakes that I'm making.

03:19 I made a mistake right here in this dependency array, `React.useEffect`. I wonder if you can figure out what it is. 

```js
React.useEffect(() => {
  window.localStorage.setItem(key, state)
}, [state])
```

I'm missing one of the dependencies for my callback. Before, we had this key hard-coded as name, so it couldn't ever change, so my dependency array didn't need to include that.

03:37 Now, I'm accepting a key as a parameter from the users of the localStorageState custom hook, and they could potentially change that value. If they do, then I need to make sure to respond to that change, so that I update localStorage and not lose any of the user's work.

03:53 It's important that in your applications, you use and follow that ESLint rule, so you can avoid bugs like this. 

```js
React.useEffect(() => {
  window.localStorage.setItem(key, state)
}, [key, state])
```

Now, with this in mind, we could make this useLocalStorageState hook a lot more robust by removing the item at the old key if that key has changed. I'm going to leave that as a fun exercise for you to do later.