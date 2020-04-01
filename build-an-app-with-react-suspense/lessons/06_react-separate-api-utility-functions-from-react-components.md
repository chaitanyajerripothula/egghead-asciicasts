Instructor: [00:00] A component that can only fetch one Pokémon is not very interesting. Let's extract this fetch into another API function that would allow us to make this dynamic.

[00:11] We'll take this entire line and move it into a new function, fetchPokemon. This function will take an ID as an argument. We'll define that here, fetchPokemon, ID, and return our fetch request. Format that up, save.

```js
function fetchPokemon(id) {
  return fetch(`https://pokeapi.co/api/v2/pokemon/${id}`).then(res => res.json());
}
```

[00:30] Everything works beautifully, but all this API code is starting to clutter up this component file. Let's remove it into its own file, which we'll call API. Paste what we had before, and export both of these functions as named exports

```js
export function suspensify(promise) {
  let status = "pending";
  let result;
  let suspender = promise.then(
    response => {
      status = "success";
      result = response;
    },
    error => {
      status = "error";
      result = error;
    }
  );

  return {
    read() {
      if (status === "pending") {
        throw suspender;
      }
      if (status === "error") {
        throw result;
      }
      if (status === "success") {
        return result;
      }
    }
  };
}

export function fetchPokemon(id) {
  return fetch(`https://pokeapi.co/api/v2/pokemon/${id}`).then(res => res.json());
}
```

[00:51] Now we can refer to both of them by name inside of our PokemonDetail component. Import fetchPokemon and suspensify from API. 
```js
import React from "react";
import { fetchPokemon, suspensify } from "./api";
```

As your application grows, keeping these clean boundaries between components and APIs is a maintenance habit that you want to repeat regularly.
