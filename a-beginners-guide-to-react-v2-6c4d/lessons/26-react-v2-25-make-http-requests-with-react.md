Instructor: [00:00] Here we have an app that's managing a `pokemonName` state. Then we're rendering out a form, and we're rendering an input. Every time we submit our form, we're going to update the `pokemonName` to whatever the user typed in the input.

[00:13] When this is rerendered, we're going to render the `PokemonInfo` with that `pokemonName`. Then the `PokemonInfo` should render out information based on that Pokémon. If we type in "Pikachu", then we should request some information for Pikachu.

```js
function App() {
  const [pokemonName, setPokemonName] = React.useState('')

  function handleSubmit(event) {
    event.preventDefault()
    setPokemonName(event.target.elements.pokemonName.value)
  }

  return (
    <div>
      <form onSubmit={handleSubmit}>
        <label htmlFor="pokemonName">Pokemon Name</label>
        <div>
          <input id="pokemonName" />
          <button type="submit">Submit</button>
        </div>
      </form>
      <hr />
      <PokemonInfo pokemonName={pokemonName} />
    </div>
  )
}
```

[00:31] We need to get that information by fetching it from a server, so we have this helper function here called `fetchPokemon` that creates a Pokémon GraphQL query. We're using `window.fetch` to fetch to this public API that has Pokémon information.

```js
function fetchPokemon(name) {
  const pokemonQuery = `
    query ($name: String) {
      pokemon(name: $name) {
        id
        number
        name
        attacks {
          special {
            name
            type
            damage
          }
        }
      }
    }
  `

  return window
    .fetch('https://graphql-pokemon.now.sh', {
      // learn more about this API here: https://graphql-pokemon.now.sh/
      method: 'POST',
      headers: {
        'content-type': 'application/json;charset=UTF-8'
      },
      body: JSON.stringify({
        query: pokemonQuery,
        variables: { name }
      })
    })
    .then(r => r.json())
    .then(response => response.data.pokemon)
}
```

[00:47] We're making a post. We have our proper headers to accept JSON. We serialize, and then we specify our body is a JSON stringified version of our query, and then the variables for our query, which is the name of the Pokémon. When that promise resolves, we're going to take the response, parse it as JSON.

[01:10] When that's done, we'll get that response object, and pluck the Pokémon off of the data in the response. We can call this function to get the information for this Pokémon that we get as a prop, but our render method here has to be synchronous, and our `fetchPokemon` function is asynchronous.

[01:30] **Making HTTP requests like this is a side effect, so we're going to use the React `useEffect` hook**. We'll say react.`useEffect`. The first thing that I want to say here is if there's no Pokémon name -- they haven't entered a Pokémon name yet, or they submitted an empty one -- then we'll simply return. We don't need to make a request for that.

[01:52] Otherwise, we'll call that `fetchPokemon` with the Pokémon name. Then in our success handler for this promise, we'll get the Pokémon data _(`pokemon`)_, and we can set some state. Let's go ahead and manage some state for this. We'll have `useState` for Pokémon. Then we'll call `setPokemon`, and we'll initialize this to `null`.

[02:15] Down here, let's rename this from `pokemon` to `pokemonData`. Then we can call `setPokemon` with the `pokemonData`. Down here, we can say if no pokemon (`!pokemon`), then we'll return just a "..." to indicate that we're loading. Otherwise, we know we have the Pokémon data, and so we can render it right here.

[02:38] Let's JSON stringify this. We'll put that in a `pre` so we get the right spacing. We'll say `JSON.stringify(pokemon, null, 2)` to get nice spacing for our JSON. We'll save this. We have a "..." right here because there's no Pokémon. Then we can enter in "Pikachu", submit that, and we get Pikachu's information.

```js
function PokemonInfo({ pokemonName }) {

  ...

  React.useEffect(() => {
    if (!pokemonName) {
      return
    }
    fetchPokemon(pokemonName).then(pokemonData => {
      setPokemon(pokemonData)
    })
  })

  if (!pokemon) {
    return '...'
  }

  return <pre>{JSON.stringify(pokemon, null, 2)}</pre>
}
```

[03:01] **In our view, to do anything asynchronous, that is a side effect that needs to happen inside a `useEffect` callback**. For our `PokemonInfo`, we accept that `pokemonName`. In our callback, if there is no `pokemonName` specified, then we'll simply return. Otherwise, we'll fetch the `pokemonData` with that `pokemonName`.

[03:19] When we get that data back, we'll update our own state to have that data. That'll trigger a rerender of `PokemonInfo`, and then we can return the stringified version of that `pokemonData`.

[03:31] We could apply an optimization here to make sure that this `useEffect` only runs when we want it to by putting `pokemonName` in here. That way, this useEffect will only rerun when the `pokemonName` changes. We could also add an if statement here for if there's no `pokemonName` supplied.

[03:51] We could say return "Submit a Pokémon." With that, we get "Submit a Pokémon." Then we can type in something like "Pikachu", submit that, and we'll get Pikachu's information loaded up.

```js
function PokemonInfo({ pokemonName }) {

  ...

  React.useEffect(() => {

   ...

  // Put pokemonName here so useEffect only reruns when this variable changes
  }, [pokemonName])

  if (!pokemonName) {
    return 'Submit a pokemon'
  }

  if (!pokemon) {
    return '...'
  }

  return <pre>{JSON.stringify(pokemon, null, 2)}</pre>
}
```
