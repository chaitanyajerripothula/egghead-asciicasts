Instructor: [00:00] I want to be able to type into our input field some value and have that saved in localStorage so that when I refresh the page, that value will be retrieved from localStorage and be loaded into the input.

[00:11] This is called a side effect, and in react, to do this, you use `React.useEffect()`. This is another hook, like useState. This function will be called every time the greeting component is rendered.

```js
function Greeting() {
  const [name, setName] = React.useState('')
  React.useEffect(() => {

  })
  const handleChange = event => setName(event.target.value)
  return (
    <div>
      <form>
        <label htmlFor="name">Name: </label>
        <input onChange={handleChange} id="name" />
      </form>
      {name ? <strong>Hello {name}</strong> : "Please type your name"}
    </div>
  )
}
```

[00:23] Anytime the greeting component is rendered, we're going to say `window.localStorage.setItem` -- we'll call it `name` in localStorage, and we'll set it to the value of `name`. 

```jsx
React.useEffect(() => {
  window.localStorage.setItem('name', name)
})
```

If we save that, and then open our dev tools and in the application tab here, we can go to localStorage, and we'll see that there's nothing in there.

[00:45] Then we can type a name in our input field, and we'll see that the name gets updated in localStorage with whatever the current value for that name variable is.

[00:54] You'll notice that if I refresh here, I'm not getting that name value loaded into my input, and the value in localStorage is getting cleared. This is because we initialize our state to an empty string, and we don't take the localStorage value into account.

[01:09] Then, because we've rendered, we run this useEffect callback, updating the localStorage item for name to that new empty string name. We need to initialize that value to whatever is in localStorage if it's there, and an empty string if it's not.

[01:23] Here in our `useState`, we'll say `window.localStorage.getItem(name)`, and if that returns null because there's nothing in there, then we'll default that to an empty string. 

```js
function Greeting() {
  const [name, setName] = React.useState(
    window.localStorage.getItem('name') || '',
  )
  React.useEffect(() => {
    window.localStorage.setItem('name', name)
  })
  const handleChange = event => setName(event.target.value)
  return (
    <div>
      <form>
        <label htmlFor="name">Name: </label>
        <input onChange={handleChange} id="name" />
      </form>
      {name ? <strong>Hello {name}</strong> : "Please type your name"}
    </div>
  )
}
```

We save that. We type a name. We hit refresh, and we notice that the value in localStorage is still consistent.

[01:43] We notice the value below our input is correct, but the name input does not have the name in there. We need to specify what the value for the input should be. We specify a `value={name}` on our input.

```html
<div>
  <form>
    <label htmlFor="name">Name: </label>
    <input value={name} onChange={handleChange} id="name" />
  </form>
  {name ? <strong>Hello {name}</strong> : "Please type your name"}
</div>
```

[01:57] Now when we save this, we'll get a refresh, and now the value is the same value that's in localStorage and in memory for the state of our react component. We'll notice that as we change our input and refresh, we always get that value from localStorage, and we keep that value updated in localStorage as well.

[02:16] In review, to make all this work, we first used react useEffect to set the name value in localStorage to the name value in our state in memory. Then, to have our state in memory be initialized from localStorage, we used window.localStorage.getItem(name), and if there is nothing in there, then we'll initialize it to an empty string as a default.

[02:37] Then, to make sure that the input is showing the same value for the name as a name in memory, we specified a value prop, changing this input from an uncontrolled to a controlled input.