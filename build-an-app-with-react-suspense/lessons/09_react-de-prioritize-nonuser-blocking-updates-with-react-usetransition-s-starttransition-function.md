Instructor: [0:00] With Concurrent Mode enabled, we notice a new error as we click this button a couple times. React's errors are excellent. Let's read every word of this one.

[0:11] Warning. Pokémon Detail triggered a user-blocking update that suspended. The fix is to split the update into multiple parts, a user-blocking update to provide immediate feedback and another update that triggers the bulk of the change. Refer to the documentation for useTransition to learn how to implement this pattern.

[0:34] There's no link in this one yet, but we can search the docs for `useTransition`. Here, I'll show you the bare minimum to make this error go away, which is just to use `startTransition`. What React is trying to tell us is that when we did this `onClick`(in pokemon-details.js), this fetch request gets queued up and it has the potential to block user interaction.

[0:56] If someone's typing on an input or clicks another thing, those things could be delayed because of this, this longer fetch request. We use `useTransition` to communicate that we're OK with running this update slightly deprioritized if it'll keep the UI nice and snappy for the customer.

[1:17] Let's use this hook, `React.useTransition`. Like useState, we're going to make an assignment. By convention, this first item is known as `startTransition`. It's a function. 

```js
let [pokemonResource, setPokemonResource] = React.useState(initialPokemon);
let [startTransition] = React.useTransition()
let pokemon = pokemonResource.read();

```

To use it, we just sneak it between our `onClick` callback and our `setPokemonResource` function.

[1:41] This takes a function. We'll wrap our `setPokemonResource` with an arrow function, save, and see if we made that error go away. 

```js
return (
 <div>
  {pokemon.name}{" "}
    <button
      type="button"
      onClick={() =>
      startTransition(() =>
        setPokemonResource(suspensify(fetchPokemon(pokemon.id + 1)))
        )
      }
    >
      Next 
    </button>
  </div>
);

```

Perfect. We've now successfully deferred this state update by using startTransition from the useTransition hook.
