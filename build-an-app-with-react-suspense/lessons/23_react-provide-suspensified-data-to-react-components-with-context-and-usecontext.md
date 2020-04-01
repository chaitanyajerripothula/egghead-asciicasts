Instructor: [0:00] Let's explore and create a `Context.Provider` for our resource reading components. For me, context always starts in a new file that I can import wherever I need it.

[0:12] Here, I'll start a new file called `pokemon.js`. As this application grows, I'll put a lot of named exports in here, but we'll start with context. `import React from 'react'`. We'll need that for creating context, and `export const PokemonContext`, which we'll create with `React.createContext`.

**pokemon.js**
```js
import React from "react";

export const PokemonContext = React.createContext();
```

[0:39] Back to our app, where we will use it for the first time. Let's import it at the top. Now, it's a named export, so we need to use these curly braces, `PokemonContext` from Pokemon.

```js
import { PokemonContext } from "./pokemon";
```

[0:56] In this, `PokemonContext` is both a provider and a consumer. Let's use the provider to wrap this `PokemonDetail` component, which we already know is consuming a `PokemonResource`. `PokemonContext.Provider`, we'll give it as a `value` an object that we haven't created yet, `pokemonState`. Now, wrap our `PokemonDetail`.

```js
<React.SuspenseList revealOrder="forwards" tail="collapsed">
  <React.Suspense fallback={<div>Fetching Pokemon stats...</div>}>
    <ErrorBoundary fallback="Couldn't catch 'em all.">
      <PokemonContext.Provider value={pokemonState}>
        <PokemonDetail />
      </PokemonContext.Provider>
    </ErrorBoundary>
  </React.Suspense>

  ...

<React.SuspenseList>
```

[1:21] The goal is to update `PokemonDetail` in such a way that it doesn't require these props. Let's move up a few lines and create our `pokemonState`. What `pokemonState` = new object, change this to `pokemon`, update the syntax but leave the assignment the same at the bottom for `isStale`.

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

  let pokemonIsPending = deferredPokemonResource !== pokemonResource;


  let pokemonState = {
    pokemon: deferredPokemonResource,
    isStale: pokemonIsPending,
  };

  ...

}
```

[1:49] Now we have this little object that we're using to provide as context to `PokemonDetail`. Next, we just need to update `PokemonDetail`. At the top, we import our context again as a named export, `PokemonContext` from ./Pokemon. 

**pokemon-detail.js**
```js
import { PokemonContext } from "./pokemon";
```

We can delete these props. We won't need them anymore.

[2:14] We'll use `React.useContext`, the hook to capture context from `PokemonContext`. We will assign that to a couple of local variables. We'll reassign `pokemon` to `resource` since we already have that here and take `isStale` as it is.

```js
export default function PokemonDetail() {
  let { pokemon: resource, isStale } = React.useContext(PokemonContext);
  let pokemon = resource.read();

  ...
}
```

[2:36] Our app's complaining about this Pokemon value. Let's refresh it to make sure that's valid. Now it looks like our context is invalid, so let's go back and see what we're doing wrong. Looks like I just need to save this app file. There you have it, `PokemonDetail` is now using context to get its value. I'd like to set values as well. What does that look like? How can we set a Pokemon?

[3:05] Let's a find a function where we're doing it already. Here in `PokemonCollection` for `renderItem`, we are setting Pokemon anytime one of these buttons is clicked. 

```js
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
```

Let's comment this out so that we can have access to it later and get all the context in place. We'll call a function called `setPokemon`, and we'll use the same value as before, `pokemon.id`.

```js
<PokemonCollection
  resource={collectionResource}
  as="ul"
  renderItem={pokemon => (
    <li key={pokemon.name}>
    <button 
      type="button"
      disabled={isPending}
      onClick={() => 
        setPokemon(pokemon.id)
        // startTransition(() => 
        //   setPokemonResource(
        //       suspensify(fetchPokemon(pokemon.id))))}
      >
      {pokemon.name}
    </button>
    </li>
  )}
/>
```

[3:31] **We need to wrap this in a `PokemonContext.Consumer` if we want to get the callback out of it.** It uses a render prop API that we can pass a function to. That function will have access to the state that we gave it, including the setPokemon function, which is all we need here.

[3:56] Take that and wrap it on over to the bottom, and we're good to go on this side. 

```js
<PokemonContext.Consumer>
  {({ setPokemon }) => (
    <PokemonCollection
      resource={collectionResource}
      as="ul"
      renderItem={pokemon => (
        <li key={pokemon.name}>
        <button 
          type="button"
          disabled={isPending}
          onClick={() => 
            setPokemon(pokemon.id)
          >
          {pokemon.name}
        </button>
        </li>
      )}
    />
  )}
</PokemonContext.Consumer>
```

We just need to move this function up into our `pokemonState` object, and format.

```js
let pokemonState = {
  pokemon: deferredPokemonResource,
  isStale: pokemonIsPending,
  setPokemon: startTransition(() => 
    setPokemonResource(suspensify(fetchPokemon(pokemon.id))))}
};
```

[4:08] We have one last problem. Our Pokemon`Context.Provider` we initially kept very tight, but we're going to have to move it up in the tree if we wanted to include everything that we have, all the way down to this `setPokemon` down here.

```js
return (
<div>
  <h1>Pokedex</h1>

  <PokemonContext.Provider value={pokemonState}>
    <React.SuspenseList revealOrder="forwards" tail="collapsed">
      <React.Suspense fallback={<div>Fetching Pokemon stats...</div>}>
        <ErrorBoundary fallback="Couldn't catch 'em all.">
          <PokemonDetail />
        </ErrorBoundary>
      </React.Suspense>

      <React.Suspense fallback={<div>Connecting to database...</div>}>
        <ErrorBoundary fallback="Couldn't catch 'em all.">
          <PokemonContext.Consumer>
            {({ setPokemon }) => (
              <PokemonCollection
                resource={collectionResource}
                as="ul"
                className="pokemon-list"
                renderItem={pokemon => (
                  <li key={pokemon.name} className="pokemon-list-item">
                    <button
                      className="pokemon-list-item-button"
                      type="button"
                      disabled={isPending}
                      onClick={() => setPokemon(pokemon.id)}
                    >
                      <img
                        src={`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${pokemon.id}.png`}
                        alt={`${pokemon.name} image`}
                        width="50"
                      />
                      <h3>{pokemon.name}</h3>
                    </button>
                  </li>
                )}
              />
            )}
          </PokemonContext.Consumer>
        </ErrorBoundary>
      </React.Suspense>
    </React.SuspenseList>
  </PokemonContext.Provider>
</div>
);
```

[4:25] Let's keep our fingers crossed, see how we did. "Pokemon is undefined on line 33." Of course, we need to take an ID and use that instead. Format, save, and click a few buttons. We did it.

```js
let pokemonState = {
  pokemon: deferredPokemonResource,
  isStale: pokemonIsPending,
  setPokemon: id => startTransition(() => 
    setPokemonResource(suspensify(fetchPokemon(id))))}
};
```

[4:46] Something that I like about this is that we can create a `setPokemon` function that has everything that we need to make these transitions nice and smooth. Additionally, components that read resources, like this `PokemonDetail` component, aren't truly agnostic. You can't just take any Pokemon and then render it. They are always going to read from a resource.

[5:10] If they're already coupled to the application implementation, why not also read them from context? Of course, an issue that we have here is that we're recreating this state object every render. If it gets really big and unruly, maybe you might see some performance issues.

[5:27] As for right now, with the application that we have, I really like it. It hides all of the Suspensey details and lets us call functions like `setPokemon` instead of `startTransition` arrow function `setPokemonResource(suspensify(fetchPokemon(id)))`. Your mileage may vary, but as far as working goes, it definitely works.
