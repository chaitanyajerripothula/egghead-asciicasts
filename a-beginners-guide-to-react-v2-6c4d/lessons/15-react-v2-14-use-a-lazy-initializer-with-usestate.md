Kent C. Dodds: 0:00 One thing that's important to know about the useState hook is that the initial value you provide is important for the initial render of our component, but then it's ignored for renders of our component thereafter.

0:11 That's normally not a problem, especially if you have just an empty string, but here we're reading into local storage every time our greeting component is re-rendered. We can add a `console.log('rendered')` right here to see how often that is.

```jsx
function Greeting() {
  const [name, setName] = React.useState(
    window.localStorage.getItem('name') || '',
  )
  console.log('rendered')
  React.useEffect(() => {
    window.localStorage.setItem('name', name)
  })
  ...
}
```

0:26 We open up our console. We'll see that we already have a rendered here for our initial render of the greeting component. As we type, we get a render every time we type a character. We only need it to read into local storage for the initialization of our state. We don't need to have it read into local storage for every single time we re-render.

0:48 This isn't really a huge deal here because reading into local storage is pretty fast and we're not parsing anything. If we were parsing a big JSON object, then this could be a problem. To combat this, React useState has a lazy initialization feature.

1:04 You can provide a function as the initial value. That function will be called to retrieve the initial value. That function will only be called when it's absolutely necessary to retrieve the initial value.

1:15 If we turn `React.useState()` into an arrow function which simply returns that initial value, then we save this, 

```jsx
function Greeting() {
  const [name, setName] = React.useState(
    window.localStorage.getItem('name') || '',
  )
  console.log('rendered')
  React.useEffect(() => {
    window.localStorage.setItem('name', name)
  })
  ...
}
```

we'll notice that we're still getting our renders, but this function is not getting called except on the initial value retrieval.

1:30 We can prove this by making this a multi-line arrow function, returning that value, and console.log("hello") in here. Now, we got that hello. Then we get that rendered.

```jsx
function Greeting() {
  const [name, setName] = React.useState(() => 
    console.log('hello')
    return window.localStorage.getItem('name') || '',
  )
  console.log('rendered')
  React.useEffect(() => {
    window.localStorage.setItem('name', name)
  })
  ...
}
```

1:44 That's an important note, is that this function is called synchronously. It's expected to be synchronous. Then, as we type H-E-L-L-O, you'll see that we do not get that console.log("hello"). We save ourselves the expense of reading into local storage on every render.

2:03 In review, the problem that we're solving here is that reading into local storage is not necessary except for the initial render of our component. We turned our initial value argument here into a function so that React will call it only when it's necessary to get that initial value, which is only on the first render.