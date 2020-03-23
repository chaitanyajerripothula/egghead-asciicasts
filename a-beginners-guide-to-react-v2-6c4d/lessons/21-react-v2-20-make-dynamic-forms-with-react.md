Kent C. Dodds: 0:00 Let's say that this username form cannot have uppercase characters. **They can only have lowercase characters.** We could show the user an error when they hit the submit button, but it would be a lot better user experience if we display an error message as they're making changes to the input field if they change it to something that's not allowed.

0:18 We need to know what the user is typing as they're typing it, not just as they submit it. We're going to add an `onChange handler` right to the input field. We'll call this `handleChange`.

```html
<div>
  <label htmlFor="usernameInput">Username:</label>
  <input id="usernameInput" type="text" onChange={handlelChange} />
</div>
```

We'll make a function `handleChange`. That will take the `event`.

0:33 Then we can use `event.target`. Because the target of this handleChange event is our input, then `event.target` is going to be the `input`, `.value` is going to be the `username`. 

```js
function handleChange(event) {
  const username = event.target.value;
}
```

We need to **store that username** somewhere and **trigger a re-render** of the username form so that it displays the error message if the username is typed incorrectly.

0:55 Let's go ahead and add some state to this username form.

We'll say, `const [username, setUsername] = React.useState('')`. We'll initialize that to an empty string. In the function handleChange, we'll call `setUsername()` with the `event.target.value`.

```jsx
function UsernameForm() {
  const [username, setUsername] = React.useState('')
  function handleSubmit(event) {
    event.preventDefault() 
    const username = event.target.elements.usernameInput.value
    alert(`You entered: ${username}`)
  }
  ...
}
```

1:12 Then we no longer need to get the username from the form input elements because we're always going to be keeping username up to date with whatever the user is specifying. We can get rid of `const username = event.target.elements.usernameInput.value`. Now this `username` is just referencing that.

```jsx
function UsernameForm() {
  const [username, setUsername] = React.useState('')
  function handleSubmit(event) {
    event.preventDefault() 
    alert(`You entered: ${username}`)
  }
  ...
}
```

1:25 Next, let's go ahead and **determine whether this is lowercase** by saying, `const isLowerCase = username === username.toLowerCase()` If it's equal to its toLowerCase version of itself, then we know that it is lowercase. We know that we have an `error` if it's not lowercase.

1:45 If it is lowercase, then we'll just say the error is `null`. Otherwise, we'll say, `Username must be lowercase`. 

```jsx
function UsernameForm() {
  const [username, setUsername] = React.useState('')
  const isLowerCase = username === username.toLowerCase()
  const error = isLowerCase ? null: 'Username must be lower case'
  function handleSubmit(event) {
    event.preventDefault() 
    alert(`You entered: ${username}`)
  }
  ...
}
```

Then we just need to display that error message. We'll copy `error`. Come down to the form. Make a `div` with a style color red, just for the fun of it. We'll put the `error` message right in there.

```jsx
<div>
  <label htmlFor="usernameInput">Username:</label>
  <input id="usernameInput" type="text" onChange={handlelChange} />
</div>
<div style={{color: 'red'}}>{error}</div>
```

2:04 Then we can also make the button `disabled={}` if there's an `error`. Disabled accepts a `Boolean`. We'll just say, `disabled={Boolean(error)}`. If error is `truthy`, then we'll pass a true value for disabled. If there is no error or it's `falsey`, then we'll pass a false value for disabled.

```html
<button disabled="{Boolean(error)}" type="submit">
  Submit
</button>
```

2:25 Let's save that. We got a refresh. If we type an uppercase character, then we'll get `Username must be lowercase`. The submit button is disabled. If we type a lowercase character, then we don't get any problem until we get an uppercase character in there.

2:40 If you ever need to know exactly what the user is typing as they're typing it, then you can use the `onChange` event to get access to the value of the `input` and update that in the state of your component.

2:51 Then changes to that value will trigger a re-render of your component. That state `value` will be whatever the user has typed, allowing us to create this error message based on whether the `username` is lowercase. Then we display that error message `<div style={{color: 'red'}}>{error}</div>` in red, in this div. We disable the submit button if there is an error message.