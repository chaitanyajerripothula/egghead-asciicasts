Instructor: [00:00] There is one refactoring that will happen hundreds, if not thousands of times, in any React app, and that is the **hoist state refactoring. It's where we take all of the states in a component and we move it higher up in the component tree**.

[00:16] What this lets us do is coordinate between other states that may be asynchronous. We'll see this in future lessons, but for right now, we just need to do the work of moving this state from this component to a place higher up in the component tree, in this case, our app.

[00:33] When I do this refactoring, I like to start by just deleting everything that I don't want in the render function. We'll take that and paste it where it would have been had it existed in this component. I like doing that because it indicates to me via my editor what variables I'm no longer using. In this case, I'm going to pull all of `PokemonDetail()` out, save and move that into my app component.

### app.js
```js
import React from "react";
import ErrorBoundary from "./error-boundary";
const PokemonDetail = React.lazy(() => import("./pokemon-detail"));

export default function App() {
  let [pokemon, setPokemon] = React.useState(initialPokemon);
  let deferredPokemon = React.useDeferredValue(pokemon, {
    timeoutMs: 3000
  });

  return (
    <div>
      <h1>Pokedex</h1>

      <ErrorBoundary fallback={"Couldn't catch 'em all."}>
        <React.Suspense fallback={"Catching your Pokemon..."}>
          <PokemonDetail />
          <button
            type="button"
            disabled={isPending}
            onClick={() =>
              startTransition(() =>
           setPokemonResource(suspensify(fetchPokemon(pokemon.id + 1)))
              )
            }
          >
            Next
          </button>
        </React.Suspense>
      </ErrorBoundary>
    </div>
  );
}
```

[01:06] In pokemon-detail.js I want to take a Pokemon as props. I don't have to move a lot of code around, I'm just going to call this a resource, but you can call this whatever you want. I'll change this to read that resource.

[01:20] We have this isPending check and it checks to make sure that the previous resource isn't the same as the current resource. I don't want to have to take two resources to do that. I'm going to move this out as well and move it up.

### pokemon-detail.js
```js
export default function PokemonDetail({ resource }) {
  let pokemon = resource.read();

  ...
}
```

[01:36] IsPending doesn't make a lot of sense name wise in the application context, so I'm going to rename this to  `PokemonIsPending`. We'll take that as a prop named isStale, so we can know whether or not the resource that we received is stale or not. We'll rename that in our component.

[01:59] We just need to pass both of those down as props, `resource` is `deferredPokemonResource` `isStale = PokemonIsPending`. I realized that misnamed this here isState. You're probably yelling at me there.

[02:20] Let's look at our component and see what else we missed. We see that initialPokemon is no longer being used. We know that we moved that, we can delete it, move it over to app,js, save. We see that now `import { fetchPokemon, suspensify } from "./api";` is dulled out as well because they're being called for that initialPokemon, so we can copy those out as well. Save and import.

### app.js
```js
import React from "react";
import ErrorBoundary from "./error-boundary";
import { fetchPokemon, suspensify } from "./api";

const PokemonDetail = React.lazy(() => import("./pokemon-detail"));

let initialPokemon = suspensify(fetchPokemon(1));

export default function App() {
  let [pokemon, setPokemon] = React.useState(initialPokemon);
  let [startTransition] = React.useTransition();
  let deferredPokemon = React.useDeferredValue(pokemon, {
    timeoutMs: 3000
  });
  
  let pokemonIsPending = deferredPokemonResource !== pokemonResource;

  return (
    <div>
      <h1>Pokedex</h1>

      <ErrorBoundary fallback={"Couldn't catch 'em all."}>
        <React.Suspense fallback={"Catching your Pokemon..."}>
          <PokemonDetail
            resource={deferredPokemonResource}
            isStale={pokemonIsPending}
          />

          <button
            type="button"
            disabled={isPending}
            onClick={() =>
              startTransition(() =>
           setPokemonResource(suspensify(fetchPokemon(deferredPokemon.read().id + 1)))
                )
            }
          >
            Next
          </button>
        </React.Suspense>
      </ErrorBoundary>
    </div>
  );
}
```

[02:48] Now that we've moved all the state up, let's jump over to a browser and see how we did. We have two errors, isPending is not used and we're still calling Pokemon here. Let's change those out. This isPending should be PokemonIsPending.

[03:06] Here, we need to be reading from that deferredPokemon. DeferredPokemon resource.read, save that and that should clear this up. 

```js
<button
  type="button"
  disabled={pokemonIsPending}
  onClick={() =>
    startTransition(() =>
      setPokemonResource(suspensify(fetchPokemon(deferredPokemonResource.read().id + 1)))
    )
  }
>
  Next
</button>
```

Click through, see how we did, slow things down, make sure that our slow states are working as well. Looks perfect to me.

[03:32] Since we're in our refactoring mood, I thought I would take this delay spinner out and just put in a new file called ui. In this module, we'll need to export it. I won't export it as default because we'll have a bunch of other components in this ui file, but we will need to import React because of our usage of JSX.

### ui.js
```js
import React from "react";

export function DelaySpinner() {
  return (
    <span role="img" aria-label="spinner" className="DelaySpinner">
      <style>{`
        .DelaySpinner {
          animation: 0s linear 0.5s forwards makeVisible, rotation 1.5s infinite linear;
          display: inline-block;
          font-size: .7rem;
          visibility: hidden;
        }
        @keyframes rotation {
          from { transform: rotate(0deg) }
          to { transform: rotate(359deg) }
        }
        
        @keyframes makeVisible {
          to {
            visibility: visible;
          }
        }
      `}</style>
      🌀
    </span>
  );
}
```

[04:00] Save that, jump back into our pokemon-detail.js and import. We'll call it by name by using the named exports from ./ui. Format and save one last time, jump to our browser and we are off to the races.

### pokemon-details.js
```js
import { DelaySpinner } from "./ui";
```

[04:24] Now this Pokemon detail component does what it says, just takes a Pokemon resource and spits out all of the details about it. All of the state involved in fetching, requesting and passing down the Pokemon is all handled at our app level. Now that it's here, we can coordinate it with other app state.
