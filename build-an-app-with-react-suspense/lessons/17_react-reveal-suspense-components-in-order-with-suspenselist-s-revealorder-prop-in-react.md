Instructor: [00:00] **The Suspense list reveal order prop has a number of options -- forwards, backwards and together**. Let's see what they do.

[00:09] We've seen previously that, by default, Suspense list will render the first available Suspense components. If we hit refresh again, we'll see the list of Pokemon come in first, followed by the Pokemon.

[00:25] However, when we pass in the prop `revealOrder`, and use the value `together`, it works like a promise.all. 

```jsx
<h1>Pokedex</h1>

<React.SuspenseList revealOrder="together">
  <React.Suspense fallback={<div>Fetching Pokemon...</div>}>
    <ErrorBoundary fallback={"Couldn't catch 'em all."}>
      <PokemonDetail
        resource={deferredPokemon}
        isStale={deferredPokemonIsStale}
      />

```


Anything that is in here, any of these suspense components, we're going to wait for all of them to resolve before we show anything. Let's watch that again, waiting for both of these to come back. Then we show it all at once.

[00:48] Because we're controlling the speed of these responses, we know that this one comes in faster than this one. Let's change this to `backwards` first.

```jsx
<h1>Pokedex</h1>

<React.SuspenseList revealOrder="backwards">
  <React.Suspense fallback={<div>Fetching Pokemon...</div>}>
    <ErrorBoundary fallback={"Couldn't catch 'em all."}>
      <PokemonDetail
        resource={deferredPokemon}
        isStale={deferredPokemonIsStale}
      />

```

[01:02] We'll always see the bottom one resolve first or see them resolve together. We saw the first case, but let's verify that they'll show together if this promise resolves second. Jump into our API and just switch these values.

[01:20] This one's now 1.5 seconds, and this one is 3 seconds. 

### api.js
```js
export function fetchPokemon(id) {
  return fetch(`https://pokeapi.co/api/v2/pokemon/${id}`)
    .then(res => res.json())
    .then(sleep(1500));
}

export function fetchPokemonCollection() {
  return fetch(`https://pokeapi.co/api/v2/pokemon`)
    .then(res => res.json())
    .then(sleep(300));
}
```

Now we'll see that they come together, because backwards always tries to resolve the bottom Suspense component first. Because it takes longer, they render together.

[01:36] We can see this effect in reverse if we try out forwards. Now the individual Pokemon resolves first, and then the second Suspense component when it resolves. I find that in most cases, forwards or together is what I want to use.

```jsx
<h1>Pokedex</h1>

<React.SuspenseList revealOrder="forewards">
  <React.Suspense fallback={<div>Fetching Pokemon...</div>}>
    <ErrorBoundary fallback={"Couldn't catch 'em all."}>
      <PokemonDetail
        resource={deferredPokemon}
        isStale={deferredPokemonIsStale}
      />

```
