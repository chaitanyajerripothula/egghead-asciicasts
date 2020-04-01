Instructor: [00:00] Now that our component is properly set up to fetch a Pokemon with an ID and communicate promise status to Suspense and error boundaries, let's add the ability to see the next Pokemon.

[00:14] To do that, we need a place to store our state. For that, there's no better place than React useState. As an initial state, I want to pass in our Pokemon, but we're going to have a naming collision here. As a pattern, I refer to this as initialPokemon and change the name up here as well.

[00:36] Now, I'm free to use the version that I get from useState as Pokemon, keeping everything in my component the same. It can't be additionally helpful to call this a resource and let Pokemon read that Pokemon resource only once.

```js
import React from "react";
import { fetchPokemon, suspensify } from "./api";

let initialPokemon = suspensify(fetchPokemon(1));

export default function PokemonDetail() {
let [pokemonResource] = React.useState(initialPokemon);
let pokemon = pokemonResource.read();
  return <div>{pokemon.name}</div>;
}
```

[00:57] As I interpolate other values across this component, as it gets larger, I don't have to keep calling a read(). It's cosmetic but it demonstrates how quickly these names become polluted against each other.

[01:11] We've got our initial Pokemon in state and we're using that state version of our Pokemon. We can set state by assigning this setPokemonResource function.

[01:24] Let's add a button. Button type = button and it'll have the text "Next." This is where the magic happens. On click, we want to set our Pokemon resource.

[01:43] What do we set it with? We can just fetch the next Pokemon. As long as we wrap it with our suspensify function, everything will work as we expect. Format, save and give it a try.

```js
export default function PokemonDetail() {
let [pokemonResource, setPokemonResource] = React.useState(initialPokemon);
let pokemon = pokemonResource.read();

  return (
    <div>
      {pokemon.name}{" "}
      <button
      type="button"
      onClick={() => setPokemonResource(suspemsify(fetchPokemon(2)))}
      >
      Next
      </button>
    </div>
  );
}
```

[01:59] When we press this again, we're going to keep getting Ivysaur because we've hardcoded this, too, in here. But we can pull that previous value off of our Pokemon state, `Pokemon.id +1`.

```js
onClick={( => setPokemonResource(suspemsify(fetchPokemon(pokemon.id + 1))))}
```

[02:16] Now, as we click through, it will fetch a new Pokemon every time.
