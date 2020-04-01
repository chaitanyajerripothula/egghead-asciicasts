**app.js**
```js
function PokemonCollection({ onClick }) {
  return (
    <div>
     {initialCollection.read().results.map(pokemon => (
       <li key={pokemon.name}>
        <button type="button" onClick={() => onClick(pokemon.id))}>
          {pokemon.name}
        </button>
       </li>
     ))}
    </div>
  )
}
```

Instructor: [00:00] React is about making reusable components, like this `PokemonCollection`. Unfortunately, this component isn't particularly reusable at this point. Sure we can customize this `onClick` function, but that's all we can customize.

```js
<React.Suspense fallback={<div>Connecting to database...</div>}>
  <ErrorBoundary fallback="Couldn't catch 'em all.">
    <PokemonCollection
      onClick={id => 
        startTransition(() => 
          setPokemonResource(suspensify(fetchPokemon(id)))
        )
      }
    />
  </ErrorBoundary>
</React.Suspense>
```

[00:19] That means if you want to give this any other design or functionality, you're going to have to make a component of your own. What if you wanted an anchor instead of a button? Well, you got to make your own component.

[00:31] I think that we can do much better by using a couple of React tricks. **We can make this more reusable by using a render prop, and as component prop, object default values and spread props in JSX.**

[00:52] Let's dive in with render props. **Render props are great because they allow us to invert control of rendering.**

[01:00] That means that instead of writing this function, we can call a function that's given to us. By convention, this is generally called `renderItem`. 

```js
function PokemonCollection({ onClick, renderItem }) {
  return (
    <div>
     {initialCollection.read().results.map(renderItem)}
    </div>
  )
}
```

We take that as props and move it up into our component implementation.

```js
<React.Suspense fallback={<div>Connecting to database...</div>}>
  <ErrorBoundary fallback="Couldn't catch 'em all.">
    <PokemonCollection
      renderItem={pokemon => (
       <li key={pokemon.name}>
        <button type="button" onClick={() => startTransition(() => 
          setPokemonResource(suspensify(fetchPokemon(pokemon.id))))}>
          {pokemon.name}
        </button>
       </li>
      )}
    />
  </ErrorBoundary>
</React.Suspense>
```

[01:15] Find `renderItem` and modify it ever so slightly so that instead of using `onClick`, we just call the function directly. Bye-bye, `onClick`. Format and save. Everything looks good, but does it work? It does, indeed.

[01:47] Render prop, check. You might notice a little mismatch between our markup here. I'm rendering list items inside of a `div`. That's not right.


```js
function PokemonCollection({ renderItem }) {
  return (
    <div>{initialCollection.read().results.map(renderItem)}</div>;
  )
}
```

[01:58] I could change this, but then, anyone who implements this component, will have to use list items. **I'd really like to make this dynamic and I can't, using it as component prop. Here, instead of making the decision of what component I should render, I'll let the implementing developer make that decision by taking a prop.**

[02:21] To do that, we'll do a little switch-a-roo. I can ask for a capitalized "A" prop that JSX needs to make this a dynamic component, but that is a little strange, so I'm just going to switch it out using this fancy JavaScript Syntax, which allows me to take a prop with a lowercase `as` and kind of call it whatever I want. We could even call it component, if we wanted to, but let's just leave it as `As`

```js
function PokemonCollection({ as: As, renderItem }) {
  return (
    <As>{initialCollection.read().results.map(renderItem)}</As>;
  )
}
```

[02:55] We're getting an error now because it needs to be defined. In this case, `As` is going to be a ul. 

```js
<PokemonCollection
  as="ul"
  renderItem={pokemon => (
    <li key={pokemon.name}>
    <button type="button" 
      onClick={() => 
        startTransition(() => 
          setPokemonResource(
              suspensify(fetchPokemon(pokemon.id))))}>
      {pokemon.name}
    </button>
    </li>
  )}
/>
```

Let's check our work. If we did everything right, this should be an unordered list surrounding a bunch of list items with buttons in them.

[03:11] It is, as prop check. Let's **use object default values to make sure that if someone decides not to pass any props at all, we don't have that nasty error.**

[03:27] We can use object default values when we destructor these props. Simply by using equals, we can define a default for each of these props. We'll do `div` and function that takes an item and returns that item in a `div`.

```js
function PokemonCollection({ as: As = "div", renderItem = (item) => <div>{item.name}</div> }) {
  return (
    <As>{initialCollection.read().results.map(renderItem)}</As>;
  )
}
```

[03:48] In our case, that'll be `item.name`. I always get this wrong. That needs to be equals. Format, save and we should see a list of `div`s inside of another `div`, as we do.

[04:02] The cool thing is that this is just the base implementation. If you render this with no props, that's what you see. But as soon as you start customizing it, you can make it whatever you want. Not everyone wants to define this component. Some might like to define it outside, like this.

```js
<ul>
  <PokemonCollection
    // as="ul"
    renderItem={pokemon => (
      <li key={pokemon.name}>
      <button type="button" 
        onClick={() => 
          startTransition(() => 
            setPokemonResource(
                suspensify(fetchPokemon(pokemon.id))))}>
        {pokemon.name}
      </button>
      </li>
    )}
  />
</ul>
```

[04:25] How can we make it so that our default implementation doesn't get in the way of good markup? In this case, we have an unordered list with a div inside when it should just be the list items. Not a problem. We're going to use `React.Fragment`.

[04:43] Here, instead of a `div`, we'll use `React.Fragment`. `React.Fragment` allows these to be returned as an array. In the event that we don't define what component we'd like it to be, we just don't use any component at all.

```js
function PokemonCollection({ 
  as: As = React.Fragment, 
  renderItem = (item) => <div>{item.name}</div> 
}) {
  return (
    <As>{initialCollection.read().results.map(renderItem)}</As>;
  )
}
```

[05:00] If we check, we'll see that, now, these list items go directly into the parent UL. That's great, but I much prefer to see that definition inside of my components.

[05:18] **Our component is looking great and there's only one last thing that we need to do to make this a truly reusable list component. We need to remove anything related to a Pokémon. Let's do a little refactoring.**

[05:34] Start by calling this a `List` and rendering `List` from our `PokemonCollection` component. We'll add a `new` prop called `items`, that'll be where we have all of the opinions about where we actually get that array of items.

```js
function PokemonCollection() {
  return <List items={initialCollection.read().results}/>;
}

function List({
  as: As = React.Fragment,
  items = [],
  renderItem = item => <div>{item.name}</div>,
}) {
  return <As>{items.map(renderItem)}</As>;
}
```

[05:57] We add that `prop`. It can be defaulted to an empty array, and call it. The last thing is we need to take all of the `props` that are handed to Pokémon collection and pass them along.

[06:12] To do that, we can take `props` as an entire object here, not destructuring out anything, and use JSX Spread Attributes to spread everything that we get along to the underlying component, which is this `List`.

```js
function PokemonCollection(props) {
  return <List items={initialCollection.read().results} {...props}/>;
}
```

[06:27] We're going to pass it "items" and we're going to pass it everything else, which just happens to include the `renderItem` and "as." Both `props` that we've defined and those that are getting passed through to us, all end up on this `List`.

[06:45] Let's hit save and see how we did. Do we get Squirtle? We do. Tremillion? We do.

[06:54] How does this `List` look markup wise? We have an unordered list with a bunch of list items inside. We've successfully separated this generic `List` component from the opinions of getting data in our application.

[07:09] There's only one thing left to do. Take this and put it with our other generic UI components in our UI file.

[07:18] Let's go to the bottom here, export and paste. Back in our App, we just go to the top of the file and import the named export list from "./ui." There we go. We've successfully created a perfectly reusable `List` component.

```js
import { List } from "./ui";
```
