Instructor: [00:00] Now that we have a list of Pokémon that we can click to jump to, it seems really silly to shuttle through them one by one. I'd like to change this so that the next button actually shifts to the next offset of Pokémon grabbing the next 20.

**app.js**
```js
let initialPokemon = suspensify(fetchPokemon(1));
let initialCollection = suspensify(fetchPokemonCollection());

export default function App() {
  let [pokemonResource, setPokemonResource] = React.useState(initialPokemon);
  let [startTransition] = React.useTransition();
  let deferredPokemonResource = React.useDeferredValue(pokemonResource, {
    timeoutMs: 3000
  });
  
  ...
}

```

[00:17] To do that, we'll need to put our `initialCollection` into state. Let's copy what we have here and duplicate it for collections. Let's change all this to collection, `collectionResource`, `setCollectionResource`, and `initialCollection`. Now that we have a `collectionResource`, we need to send it down to our component via props. 

```js
export default function App() {
  let [pokemonResource, setPokemonResource] = React.useState(initialPokemon);
  let [collectionResource, setCollectionResource] = React.useState(
    initialCollection
  );
  let [startTransition, isPending] = React.useTransition({ timeoutMs: 3000 });
  let deferredPokemonResource = React.useDeferredValue(pokemonResource, {
    timeoutMs: 3000
  });
  ...
}
```
```js
<React.Suspense fallback={<div>Connecting to database...</div>}>
  <ErrorBoundary fallback="Couldn't catch 'em all.">
    <PokemonCollection
      resource={collectionResource}
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
  </ErrorBoundary>
</React.Suspense>
```

We'll define that `resource` is `collectionResource` and rewrite the component to read from that.


[00:56] We'll destructure off `resource`, collect all of the remaining `props` to leave them intact, and change our `render` function to read from that `resource`.

```js
function PokemonCollection({ resource, ...props }) {
  return <List items={resource.read().results} {...props} />;
}
```

[01:09] Now we're left to modify our button. Let's first look at the response that we get and see what we can use. Visit the Network pane. We'll hit Refresh, and we see that the API actually gives us next and previous URLs, with the offsets and limits set appropriately.

[01:33] We don't have an API function yet that can do that, so let's add one. Let's duplicate this collection function, call it `fetchPokemonCollectionUrl`, and then take a URL.

[01:47] We then call that `url` directly. Of course, I copied this one, because I want to make sure that we still have our ID edition. In a world where I had more time, I would definitely extract this out into a function, but let's go with what we got.

**api.js**
```js
export function fetchPokemonCollection(url) {
  return fetch(url)
    .then(res => res.json())
    .then(res => ({
      ...res,
      results: res.results.map(pokemon => ({
        ...pokemon,
        id: pokemon.url.split("/")[6]
      }))
    }))
    .then(sleep(1000));
}

```

[02:04] Now that we have the right function, let's import it at the top of of our file here -- `fetchPokemonCollectionUrl`.

**app.js**
```js
import React from "react";
import ErrorBoundary from "./error-boundary";
import {
  fetchPokemon,
  fetchPokemonCollectionUrl,
  fetchPokemonCollection,
  suspensify
} from "./api";
```

Now, on Next, we want to fetch that URL. `fetchPokemonCollectionUrl`. We'll read from `collectionResource`. Instead of adding to the ID, we'll call it `next`.

```js
<React.SuspenseList revealOrder="forwards" tail="collapsed">
  <React.Suspense fallback={<div>Fetching Pokemon stats...</div>}>
    <ErrorBoundary fallback="Couldn't catch 'em all.">
      <PokemonDetail 
        resource={deferredPokemonResource}
        isStale={pokemonIsPending}
      />
      <div>
        <button
          type="button"
          disabled={pokemonIsPending}
          onClick={() =>
            startTransition(() =>
              setCollectionResource(
                suspensify(
                  fetchPokemonCollectionUrl(
                    collectionResource.read().next
                  )
                )
              )
            )
          }
        >
          Next
        </button>
      </div>
    </ErrorBoundary>
  </React.Suspense>

```

[02:37] Of course, we don't want to `setPokemonResource`, we want to set a `setCollectionResource`. Let's change that. Format, save and see how we did. Hit next and indeed, we get the next 20. You can see in our offset here that we now have a next and previous. Of course, we can keep going forward by 20 and selecting any Pokémon that we want.

[03:05] Now, every time we hit next, we're seeing the receded state connecting to database. We can use `useTransition` to bypass this receded state. Let's do that. Our `useTransition` app, we're going to add a `timeoutMs` prop. Set that for `3000` milliseconds, save and see what we got.

```js
export default function App() {
  let [pokemonResource, setPokemonResource] = React.useState(initialPokemon);
  let [collectionResource, setCollectionResource] = React.useState(
    initialCollection
  );
  let [startTransition] = React.useTransition({ timeoutMs: 3000 });
  let deferredPokemonResource = React.useDeferredValue(pokemonResource, {
    timeoutMs: 3000
  });

  ...
}
```

[03:29] Now when we hit next, the previous results stay in place until the new ones come along. Let's get some fresh results in here. If we want to make these results look deactivated, we can actually use `isPending` for this. Remember, `isPending` is the second element in this array and it's a Boolean.

```js
let [startTransition, isPending] = React.useTransition({ timeoutMs: 3000 });
```

[03:55] We can go down here into our list and make all these buttons disabled if we like. When we click next, all those get disabled until the next selection of Pokémon is loaded. 

```js
<React.Suspense fallback={<div>Connecting to database...</div>}>
  <ErrorBoundary fallback="Couldn't catch 'em all.">
    <PokemonCollection
      resource={collectionResource}
      as="ul"
      renderItem={pokemon => (
        <li key={pokemon.name}>
        <button 
          type="button"
          disabled={isPending}
          onClick={() => 
            startTransition(() => 
              setPokemonResource(
                  suspensify(fetchPokemon(pokemon.id))))}>
          {pokemon.name}
        </button>
        </li>
      )}
    />
  </ErrorBoundary>
</React.Suspense>
```

Now that this button is more related to this section than this section, we can move everything around.

```js
<React.Suspense fallback={<div>Connecting to database...</div>}>
  <ErrorBoundary fallback="Couldn't catch 'em all.">
    <div>
      <button
        type="button"
        disabled={pokemonIsPending}
        onClick={() =>
          startTransition(() =>
            setCollectionResource(
              suspensify(
                fetchPokemonCollectionUrl(
                  collectionResource.read().next
                )
              )
            )
          )
        }
      >
        Next
      </button>
      {isPending && <DelaySpinner />}
    </div>
    <PokemonCollection
      resource={collectionResource}
      as="ul"
      renderItem={pokemon => (
        <li key={pokemon.name}>
        <button 
          type="button"
          disabled={isPending}
          onClick={() => 
            startTransition(() => 
              setPokemonResource(
                  suspensify(fetchPokemon(pokemon.id))))}>
          {pokemon.name}
        </button>
        </li>
      )}
    />
  </ErrorBoundary>
</React.Suspense>
```

[04:28] When things are pending, let's add another one of those delay spinners. Add it to our imports from UI, format, save. As we click through, we'll see a cool little spinner as we fetch a new batch of Pokémon.
