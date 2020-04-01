Instructor: [0:00] Now that we've hoisted our component state up from our Pokemon detail into our application, we can start coordinating it with other state. Let's get some other state. I'd like to get a list of Pokemon that we might be able to select from. Let's duplicate `let initialPokemon = suspensify(fetchPokemon(1));`. Change to `initialCollection`, it'll be a list of our Pokemon.

[0:25] The way that PokéAPI is is that if I don't pass in a number, it'll give me an entire collection of Pokemon.

```js
import React from "react";
import ErrorBoundary from "./error-boundary";
import { DelaySpinner } from "./ui";
import { fetchPokemon, suspensify } from "./api";

const PokemonDetail = React.lazy(() => import("./pokemon-detail"));

let initialPokemon = suspensify(fetchPokemon(1));
let initialCollection = suspensify(fetchPokemon(""));
```

When we save our application, we'll now make this request as soon as the application loads. We can see the shape of this response. It has some Metadata, Account, Next, Previous, because it's paginated, and a Results Array, which has a bunch of Pokemon here.

[0:53] For now, just to hook everything up, we're just going to use Account. Doing that, we'll be able to see a few of the React Suspense gotchas. Here we'll use a `div`, interpolate out ` initialCollection.read` to get those values from the resource, .count. Save and see what happens. It blew up, but we've got two errors, which is always great in React.

[1:20] We see these two errors that should feel very familiar at this point. We know how to deal with those, so let's just copy and paste what we have up here around our Pokemon component. See that'll get us where we want to go. Format, save, nada. We have the same, exact errors.

[1:47] This might be perplexing because you say, "Well, I have an error boundary. I'm using it here. I also have a Suspense and a fallback, which I'm using here, so what gives?" Something that has caused me the most trouble learning Suspense is these errors when I'm wrapping up my component in error boundary and React Suspense.

[2:07] I can tell you from experience, **when you see these errors together and in particular, when you see the one that says, "Visit this URL to learn more about error boundaries," chances are you're trying to call read outside of a component. Whenever we call read, we need to do it inside of a component boundary**.

[2:28] Let's cut this code and use a component instead. `PokemonCollection`, go to the end of our file and define that component. We don't need to take props right now, just return what we had before. Just with that one little change, this will fix everything and make everything work the way it did before.

```jsx
return (
  <div>
    .
    .
    .

    <ErrorBoundary fallback="Couldn't cath 'em all.">
      <React.Suspense fallback="Loading pokemon...">
        <PokemonCollection />
      <React.Suspense>
    <ErrorBoundary>
  </div>
);
```

[2:55] Now that we know how to avoid that Suspense gotcha, I thought it would be nice to spit out a list of some of the Pokemon we have. Let's refresh this, close our console and see where that lives.

[3:11] We have a property on our response called results and change that here. Because it's an array, we can call `map()` on it. For each of those Pokemon, return a list item with the Pokemon name. Format, save and we have a nice little list.

```js
function PokemonCollection() {
  return (
    <div>
      {initialCollection.read().results.map(pokemon => (
        <li>{pokemon.name}</li>
      ))}
    </div>
  );
}
```

[3:33] We also got an error. This should be common to you if you are familiar with React. Every one of this list elements needs a key prop. Let's add that on.

Ideally, we would be able to use a Pokemon ID, but we don't have one. Fortunately, we know that Pokemon names are unique so we can use that as our unique key. Save and the error goes away, but our list remains.

```js
function PokemonCollection() {
  return (
    <div>
      {initialCollection.read().results.map(pokemon => (
        <li key={pokemon.name}>{pokemon.name}</li>
      ))}
    </div>;
  );
}
```
