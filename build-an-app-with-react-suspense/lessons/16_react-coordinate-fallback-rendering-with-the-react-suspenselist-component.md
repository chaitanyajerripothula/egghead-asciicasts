Instructor: [00:00] Now that we have two Suspense components rendering two different pieces of data, we can start coordinating those renderings using SuspenseList. **SuspenseList is a component that wraps a bunch of child Suspense components and gives you handles for how to show all of the various loading states**.

[00:22] Let's dive in and see what we need to change about our application to make it work. As we saw, we'll wrap our Suspense components with `React.SuspenseList`, top and bottom. When we save, nothing happens, because by default, it doesn't do anything that it wasn't doing before.

[00:46] We need to give it some of these props as options. The first one is `revealOrder`, and the first option we'll play with for this is `together`. The idea here is that no matter how long one of this or the other takes, they'll render together as soon as the slowest one comes back. You can think of it like promise.all. Hit save and see what happens.

[01:10] Errors, error, errors. This particular error is because our SuspenseList doesn't have any Suspense direct children. These error boundaries are sitting in the way. We need to take those and just tuck them inside of our React Suspense boundaries.

[01:29] Everything is going to work exactly the same as it did before. Error boundaries will still get caught when errors happen in Suspense boundaries when pending, but now SuspenseList knows what to do with this direct Suspense children. Let's save and those errors will go away.

```jsx
<h1>Pokedex</h1>

<React.SuspenseList revealOrder="together">
  <ErrorBoundary fallback={"Couldn't catch 'em all."}>
    <React.Suspense fallback={"Catching your Pokemon..."}>
      <PokemonDetail
        resource={deferredPokemon}
        isStale={deferredPokemonIsStale}
      />

      <button
        type="button"
        disabled={deferredPokemonIsStale}
        onClick={() =>
          startTransition(() =>
            setPokemon(
              suspensify(fetchPokemon(deferredPokemon.read().id + 1))
            )
          )
        }
      >
        Next
      </button>
    </React.Suspense>
  </ErrorBoundary>
  <React.Suspense fallback={"Loading pokemon..."}>
    <ErrorBoundary fallback={"Couldn't catch 'em all."}>
      <PokemonCollection />
    </ErrorBoundary>
  </React.Suspense>
</React.SuspenseList>
```

[01:46] Everything's hooked up, but at this point, we have a little bit of a problem. At least for explaining how this works, our returns come back way too fast. We could slow them down in devtools, but that's going to slow them down to the same amount. I want to show you what happens when one request takes significantly longer than another request.

[02:06] Let's go to our api.js. Here we're going to do something that is a terrible idea but gives us an opportunity to see what's happening and how SuspenseList is helping us. We can import this little function called `sleep` from `sleep-promise`.

[02:27] That will allow us to delay these fetch promises from resolving by a set amount of time. We can simply chain on a then call sleep and give it some duration. I'll delay this fetch by 3 seconds.

[02:45] We're using the same endpoint for both the Pokemon and the list. We don't want them to be delayed by the same amount of time, so let's make another endpoint as well. We change this to `fetch PokemonCollection` and remove all the ID stuff.

[03:03] This fetch will sleep by half the amount of time at 1.5 seconds. Save that and we need to change one more thing in our app. 


### api.js
```js
import sleep from "sleep-promise"

import { fetchPokemon, fetchPokemonCollection, suspensify } from "./"
.
.
.
export function fetchPokemon(id) {
  return fetch(`https://pokeapi.co/api/v2/pokemon/${id}`)
  .then(res => res.json())
  .then(sleep(3000));
}

export function fetchPokemonCollection() {
  return fetch(`https://pokeapi.co/api/v2/pokemon`)
  .then(res => res.json())
  .then(sleep(1500));
}
```

Need to `import fetchPokemonCollection` and use it with `initialCollections`. We see that these are showing up inline.

### app.js
```js
import { fetchPokemon, fetchPokemonCollection, suspensify } from "./api"
.
.
.
let initialColection = suspensify(fetchPokemonCollection());

```

[03:27] It makes sense at this point, I'm going to change the React.suspense callbakcs to be components. We'll say something more descriptive like fetching Pokemon stats. Down here, we'll change this to connecting the Pokemon database. Now we are in good shape to see how this works.

[03:56] The first option here is together. We see that as we refresh this, no matter how long this takes, they always render together. The Pokemon is taking a full 3 seconds to fetch. This one only half that amount of time, but they render together.

[04:13] By default, the rendering is pretty different. If we remove the revealOrder option, we'll see the first available. Pokemon come in and then the specific Pokemon that we have an ID for. Let's watch that again.

[04:29] Pokemon come in first and then the Pokemon that we have an ID for. When we use `revealOrder="together"`, now we'll wait for the longest promise to resolve before showing anything at all.

```jsx
<h1>Pokedex</h1>
<React.SuspenseList revealOrder="together">
  <ErrorBoundary fallback={<div>Fetching Pokemon stats...>/div>}>
    <React.Suspense fallback={"Catching your Pokemon..."}>
      <PokemonDetail
        resource={deferredPokemon}
        isStale={deferredPokemonIsStale}
      />

      <button
        type="button"
        disabled={deferredPokemonIsStale}
        onClick={() =>
          startTransition(() =>
            setPokemon(
              suspensify(fetchPokemon(deferredPokemon.read().id + 1))
            )
          )
        }
      >
        Next
        </button>
    </React.Suspense>
  </ErrorBoundary>
  <React.Suspense fallback={<div>Connectiing to database...</div>
    <ErrorBoundary fallback={"Couldn't catch 'em all."}>
        <PokemonCollection />
    </ErrorBoundary>
  </React.Suspense>
<React.SuspenseList>
```
