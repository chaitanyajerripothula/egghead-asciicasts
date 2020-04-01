**app.js**
```js
function PokemonCollection({ onClick }) {
  return (
    <div>
     {initialCollection.read().results.map(pokemon => (
       <li key={pokemon.name}>
        <button type="button" onClick={() => onClick(pokemon.url.split("/"[6]))}>
          {pokemon.name}
        </button>
       </li>
     ))}
    </div>
  )
}
```

Instructor: [00:00] We had to do a really gross thing to get an ID out of this Pokemon. In an ideal world, this API would have an ID, but it doesn't, so we had to make our own. This is a bad place to keep it. I don't want anyone else to copy this code or to try to make this work on their own.

[00:20] **Instead, we can augment our resource so that it has an ID.** Now, if I save it, you can see how this app breaks spectacularly. Couldn't catch them all. Let's open our tree view, jump into API, and fix this problem at the right level.

**api.js**
```js
export function fetchPokemon(id) {
  return fetch(`https://pokeapi.co/api/v2/pokemon/${id}`)
    .then(res => res.json())
    .then(sleep(1000));
}

export function fetchPokemonCollection() {
  return fetch(`https://pokeapi.co/api/v2/pokemon`)
    .then(res => res.json())
    .then(sleep(1000));
}
```

[00:37] Here we have our `fetchPokemonCollection` function, which fetches the collection at this URL, turns it to JSON. Then we have a couple `sleep`s so we can see all of the transitions happen. We can chain an additional `then` here to modify the response.

```js
export function fetchPokemonCollection() {
  return fetch(`https://pokeapi.co/api/v2/pokemon`)
    .then(res => res.json())
    .then(res => res)
    .then(sleep(1000));
}
```

[00:55] We start by just taking a response and returning a response. That works. **To modify our response, we can return an object that we spread the response out on.** Now, anytime we use an arrow function and we want to return an object, we have to wrap it in these parens. Otherwise, JavaScript thinks this is a block and gets all kinds of confused.

[01:21] We can then customize certain properties. The one that we want to customize is results. We'll start by making that response.results and we'll map. Now we have a chance for each Pokemon to modify the Pokemon just a little bit. Start by returning the same Pokemon. Then again, with the parens, the objects, the spreading, and closing it up. It formatted, so, so far, so good.

[01:51] Finally, we'll want to add an ID. Then using that same code that we had before, `pokemon.url.split("/")`, and then take the sixth element. Format, which means our code's good. Save and see if it works.

```js
export function fetchPokemonCollection() {
  return fetch(`https://pokeapi.co/api/v2/pokemon`)
    .then(res => res.json())
    .then(res => ({...res, results: res.results.map(pokemon => ({ 
      ...pokemon, 
      id: pokemon.url.split("/")[6]
      }))
    }))
    .then(sleep(1000));
}
```

[02:08] Excellent. Now we have IDs baked into our resource, and we don't have to do any cringey stuff at the app level. We can just use ID directly.

**app.js**
```js
function PokemonCollection({ onClick }) {
  return (
    <div>
     {initialCollection.read().results.map(pokemon => (
       <li key={pokemon.name}>
        <button type="button" onClick={() => onClick(pokemon.id)}>
          {pokemon.name}
        </button>
       </li>
     ))}
    </div>
  )
}
```