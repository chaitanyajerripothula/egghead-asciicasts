Kent: 0:00 Here, we have a simple `App` component where we're managing some `name` state so we can pass that to our `Name` component and pass it to our `Display` component, so that we can render out, "Hey \${name}, you are great."

0:12 We also have a `FavoriteAnimal` right here that's managing its own state and rendering the input with a value and an onChange handler.

```js
function Name({ name, onNameChange }) {
  return (
    <div>
      <label>Name: </label>
      <input value={name} onChange={onNameChange} />
    </div>
  )
}

function FavoriteAnimal() {
  const [animal, setAnimal] = React.useState('')
  return (
    <div>
      <label>Favorite Animal: </label>
      <input value={animal} onChange={event => setAnimal(event.target.value)} />
    </div>
  )
}

function Display({ name }) {
  return <div>{`Hey ${name}, you are great!`}</div>
}

function App() {
  const [name, setName] = React.useState('')
  return (
    <form>
      <Name name={name} onNameChange={event => setName(event.target.value)} />
      <FavoriteAnimal />
      <Display name={name} />
    </form>
  )
}
```

0:21 Let's say that this `Display` component, which is a sibling to our `FavoriteAnimal` component actually needs to know what the animal is, because instead of you are great, we want to say, "Your favorite animal is..." and then animal right here.

0:36 We'll accept that as a prop, but how are we going to get access to that animal if that state is living in our favorite animal component? **These two are sibling components, so the favorite animal component can't pass the animal to the display component**.

```js
function Display({ name, animal }) {
  return <div>{`Hey ${name},your favorite animal is ${animal}`}</div>
}
```

0:54 Let's keep on going. We're going to need to accept an animal. That's going to come from somewhere. We're not sure where yet, but from the looks of things, we're going to need to do the same thing to our animal state that we're doing with our name state. **That is specifically, we need to lift the state from the favorite animal component to the app component, which is the least common parent between these two components**. Let's do that.

1:17 I'm just going to grab this and I'll move that down to right here. Now the favorite animal doesn't have access to `animal` or `setAnimal`, so I'm going to accept an `animal` and an `onAnimalChange`. We'll do the same thing that we did for our `Name` component, so `onAnimalChange` will be passed to the onChange prop, and the animal gets passed on to the input.

1:46 Then we come down here to the favorite animal, and we need to pass the `animal` and `onAnimalChange`. We'll just paste in what we had before. Now, our display has access to the animal. If we save that, then everything should work, except this is misspelled. Let's do animal. Here we go. Great.

```js
function FavoriteAnimal({ animal, onAnimalChange }) {
  return (
    <div>
      <label>Favorite Animal: </label>
      <input value={animal} onChange={onAnimalChange} />
    </div>
  )
}

function App() {
  const [name, setName] = React.useState('')
  const [animal, setAnimal] = React.useState('')

  return (
    <form>
      <Name name={name} onNameChange={event => setName(event.target.value)} />
      <FavoriteAnimal
        animal={animal}
        onAnimalChange={event => setAnimal(event.target.value)}
      />
      <Display name={name} />
    </form>
  )
}
```

2:09 Now, we can say "Mulan", and Mulan's favorite animal is a dragon, but specifically "Mushu".

2:16 **What we did here is what's called lifting state**. Typically, you want to have your state as close to the code that's using that state.

2:23 That's why we had the state in this `FavoriteAnimal` component, but when we had a use case for a sibling component to have access to that state, we had to lift that state to the least common parent, which was this `App` component, and we put it right there, and then we passed that state and the mechanism for updating state, which is our `setAnimal` function, as props to the components that need it.

2:46 Here, for our `FavoriteAnimal`, we're passing the `animal` prop and an `onAnimalChange` prop so we have a mechanism for updating that state. Then here for the `Display`, all it needs is the animal, so that's all that we pass.

2:59 Once you learn how to do this, it becomes second nature, but one thing we're not quite as good at is pushing state back down or co-locating state. Let's say that our `Display` use case gets changed and we no longer need to pass the animal down, so we're going to say, "You are great," again, and we no longer need this `animal` prop.

3:18 Often, people will actually just leave it right there without making any other changes, but we need to remember that we no longer need to accept this `animal` prop on this display where we're rendering it, and because the `animal` state is only being used by a single component, that means we can actually move that state back into that component to get it co-located.

3:38 Let's move it back up here. We no longer need to accept either one of these props, and we can come down here, grab that, remove both of these props, and then we'll paste that `onChange` event handler back into our favorite animal input `onChange` prop. With that, we've co-located our state, making it easier to maintain our application in the long-term.

```js
function Name({ name, onNameChange }) {
  return (
    <div>
      <label>Name: </label>
      <input value={name} onChange={onNameChange} />
    </div>
  )
}

function FavoriteAnimal() {
  const [animal, setAnimal] = React.useState('')
  return (
    <div>
      <label>Favorite Animal: </label>
      <input value={animal} onChange={event => setAnimal(event.target.value)} />
    </div>
  )
}

function Display({ name }) {
  return <div>{`Hey ${name}, you are great!`}</div>
}

function App() {
  const [name, setName] = React.useState('')
  return (
    <form>
      <Name name={name} onNameChange={event => setName(event.target.value)} />
      <FavoriteAnimal />
      <Display name={name} />
    </form>
  )
}
```

4:00 In our view, what we did here was we explored how to lift a state and then push it back down with state co-location. Our `App` component here is rendering out some state, which does need to be used by multiple elements. Our app is maintaining the `name` state.

4:16 Then we also had this `FavoriteAnimal`, which is maintaining its own state, but then our `Display` component needed to have access to that state, so we lifted the animal state up to the least common parent, which was our `App` component. Then we passed that state and a mechanism for updating the state down to the components that needed those things. That was the lifting state part.

4:37 Then we did the reverse to push the state back down for state co-location by moving the state back to where it was before, removing the state from the component that doesn't need it anymore, and removing the props that are no longer necessary.

4:51 This enhances both the performance and the state management maintainability of our application.
