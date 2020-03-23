Kent: 0:00 It's great that we're able to display an `error` message if there's an uppercase character and we disable the submit button. **It would be even cooler if we didn't allow the user to type uppercase characters in the first place.** If they try to type an uppercase character, we just lowercase it for them.

0:15 We could do that pretty easily just by saying, `setUsername(event.target.value.toLowerCase())`. Now, the `username` that's stored inside of our state is going to be lowercase. 

```js
function handleChange(event) {
  setUsername(event.target.value.toLowerCase())
}
```

Now we can type uppercase forever. When we hit submit, that's all going to be lowercase, but it's not exactly a great user experience to be able to type something here and have the end result be different from what I submitted.

0:39 What we need to do is control the `input` value to be exactly the same thing that I have in my state, because I'm programmatically changing the value that I get from the `input` to set the value that I store in my state. If I want to keep those two in sync, then I need to control the input's value.

0:57 To do this is pretty simple. We simply add a `value prop` here.

We'll pass in the `username`. 

```html
<input
  id="usernameInput"
  type="text"
  onChange="{handleChange}"
  value="{username}"
/>
```

As soon as we pass this `value prop` onto an `input`, we're communicating to React that React no longer needs to manage the state of this `input`. Now we are going to control that state. It should not change that value to anything other than what we enter in here, which includes whatever the user is typing.

1:22 If I save this and I come back up to our web browser and I try to type an uppercase string, that's not going to work. It's going to lowercase my `string` for me automatically. Now, it's impossible for me to type something that's incorrect. It might be useful to have some sort of info thing here that indicates why I can't type uppercase characters in here.

1:42 Hopefully, you get the general idea that if you ever need to make the `input` value something different from what the user is actually typing, then you can do so by passing a `value prop`.

1:52 Let's go ahead and clean some stuff up here. We no longer need to display an error because it's impossible to make an error now. We'll get rid of that submit here.

```jsx
function UsernameForm() {
  const [username, setUsername] = React.useState('')

  function handleSubmit(event) {
    event.preventDefault()
    alert(`You entered: ${username}`)
  }

  function handleChange(event) {
    setUsername(event.target.value.toLowerCase())
  }

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label htmlFor="usernameInput">Username:</label>
        <input
          id="usernameInput"
          type="text"
          onChange={handleChange}
          value={username}
        />
      </div>
      <button type="submit">Submit</button>
    </form>
  )
}
```

We'll get rid of the `isLowerCase` and `error` right here. We can save that. Everything looks great.

2:07 You can't tell, but I'm holding down the shift key. I promise I'm trying to type something uppercase, but the value in the `input` isn't uppercase because I'm setting the `username` to a lowercase version of what's being typed in that input's `value`.

2:21 Then when React re-renders this component, the `username`, which is all the lowercase characters, is getting passed into the `value` of the `input`. React is sending that input's `value` to the lowercase version of what it was.