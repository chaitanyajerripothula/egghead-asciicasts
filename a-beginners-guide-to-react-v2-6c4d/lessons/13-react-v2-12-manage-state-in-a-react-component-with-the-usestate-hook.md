Kent: [00:00] Let's start by making our form. We'll have our `div` and then we'll make a `form`. Then we'll have a `label` with the `name` and our `input` and we'll save that. 

```js
function Greeting() {
  return (
    <div>
      <form>
        <label>Name: </label>
        <input />
      </form>
    </div>
  )
}
```

We get that form in our browser.

[00:15] Then we'll have this message, "Please type your name." Then we want to associate this `label` to this `input` so that it's properly associated and you can tell that by clicking on the label. If it doesn't focus the input, then it's not properly labeled.

[00:30] Let's add an `id` to our input of `name` and an `htmlFor` of `name`. htmlFor is like the for attribute in HTML but in JSX you need to use htmlFor. That's one of the few differences between JSX and regular HTML. 

```js
function Greeting() {
  return (
    <div>
      <form>
        <label htmlFor="name">Name: </label>
        <input id="name" />
      </form>
      Please type your name
    </div>
  )
}
```

Let's save that, come back here. When I click on name, it now focuses the input. Great.

[00:53] Now, we're going to have some state here for the name. If there is a name, then we'll have it say `<strong>`, `Hello {name}` Otherwise we'll have it say, "Please type your name." Great. 

```js
function Greeting() {
  return (
    <div>
      <form>
        <label htmlFor="name">Name: </label>
        <input id="name" />
      </form>
      {name ? <strong>Hello {name}</strong> : "Please type your name"}
    </div>
  )
}
```

Then if we create a name variable and name it 'Kent', save that. 

```js
function Greeting() {
  const name = 'Kent'
  return (
    <div>
      <form>
        <label htmlFor="name">Name: </label>
        <input id="name" />
      </form>
      {name ? <strong>Hello {name}</strong> : "Please type your name"}
    </div>
  )
}
```

It will say, "Hello, Kent!" By default it doesn't say anything.

[01:15] Now, let's wire up an `onChange` handler for this input. We'll call that `handleChange`. I'll bring that up here and say handle change is an arrow function that accepts an event and it needs to update the state of the name.

[01:30] We need to have some way to update that and we can't simply say name = event.target.value. That's not going to work because that won't trigger a re-render and, even if it were to trigger a re-render, this whole function would be recalled. Then the name that we would be setting would get garbage collected and we'd create a new name variable.

[01:52] Instead, React has what's called a React hook for maintaining state for a component. We're going to use that with `react.useState`. We'll pass it the default value of an empty string and `react.useState` returns an array, we'll call this our `stateArray`, and we'll get our name from stateArray at index zero and our set name from state array at index one.

[02:21] Here instead of trying to reassign that name variable, we'll call `setName` with the `event.target.value`. 

```js
function Greeting() {
  const stateArray = React.useState('')
  const name = stateArray[0]
  const setName = stateArray[1]
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

If we save that, now everything is working, but nobody wants to write their code like this every time they want to use state, so we're going to destructure `stateArray` and `name` to be `name` and `setName`. Then we can delete those lines and save that and it's still working.

```js
function Greeting() {
  const [name, setName] = React.useState('')
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

[02:48] Woo! React rocks! In review, to use state in a React function component, you use the useState hook from React. The useState hook accepts the initial value, so when this greeting component is initially rendered, that is going to be the value of our name variable.

[03:07] Any time we call this second element of the array, our updater function will trigger a re-render of this entire function component. When React useState is called again, it will ignore the initial value and instead give us the current value of that name.

[03:23] Because React keeps track of the order in which these are called, we could add a second one. Here we'll call this `name2` and `setName2`. We'll make another handle change to `handleChange2`. We'll have that call set name two. Then we'll just duplicate all this stuff, put that inside of another div here, and then we'll reference name two and name two. This will be handle change two.

```js
function Greeting() {
  const [name, setName] = React.useState('')
  const [name2, setName2] = React.useState('')
  const handleChange = event => setName(event.target.value)
  const handleChange2 = event => setName2(event.target.value)
  return (
    <div>
      <form>
        <label htmlFor="name">Name: </label>
        <input onChange={handleChange} id="name" />
      </form>
      {name ? <strong>Hello {name}</strong> : "Please type your name"}
    </div>
    <div>
      <form>
        <label htmlFor="name">Name: </label>
        <input onChange={handleChange2} id="name" />
      </form>
      {name2 ? <strong>Hello {name2}</strong> : "Please type your name"}
    </div>
  )
}
```

[03:49] Now we can type Kent 1 and Kent 2. Those states are managed independently of one another because React keeps track of the order in which these are going to be called, allowing you to use state as much as you need for your component.

[04:05] The values in useState can be anything. You can make it a Boolean or you can make it a number or you can make it an object or an array -- whatever makes sense for the state that you're trying to manage.