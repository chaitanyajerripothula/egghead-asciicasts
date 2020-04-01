Instructor: [0:00] At this point, you know everything that anyone knows about React Suspense -- `createRoot`, `Suspense`, `SuspenseList`, `useTransition` and `useDeferredValue`. That is all that there is to know. You might be sitting there thinking, "I don't know how to pull it all together into an application." We're going to continue to build.

[0:24] The second half of this is going to be with the breaks off a little bit. We're going to build this app feature by feature. **Instead of talking specifically about each of these APIs in excruciating detail, we'll build features out to get a better sense of how Suspense actually works in a real application.**

[0:46] Let's start by using Suspense to select a Po`onClick`kemon on click. Let's jump down to our `PokemonCollection` component. We'll take a prop  and we'll insert a button into this LI, button type="button". Here, we'll call `onClick` inside of an anonymous function. We want to call it with the `pokemon.id` to reformat that.


[1:26] If you remember correctly, this API doesn't have an ID for Pokemon, but it does have a URL. We can do something really hacky here. If we split on all of the forward slashes of URL, I know that the ID of this Pokemon is in the sixth element of that array. Again, it's not pretty, but it does get us an ID.

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

[1:53] Now, we just need to write that function where it's called. We pass `onClick` as a prop and we'll just copy and paste this from another place. Snatch and grab. The only difference is that we'll be taking an `id` and using that to fetch the Pokemon. Format, save.

```js
<React.Suspense fallback={<div>Connecting to database...</div>}>
  <ErrorBoundary fallback="Couldn't catch 'em all.">
    <PokemonCollection
      onClick={id => 
        startTransition(() => 
          setPokemonResource(suspensify(fetchPokemon(id)))
        )
      }
    />
  </ErrorBoundary>
</React.Suspense>
```

[2:22] Now we see a bunch of buttons. If all of my code is correct, we can skip to each one with all of our little pending states intact, regardless of how fast or slow our connection speed is.

[2:46] The cool thing about this is that `PokemonCollection` doesn't have to be, at all, aware of what this `onClick` callback does. We simply call with the ID that we have. Then, at our application level, we can wrap it in all of the smarts that it needs to have in order to get these really nice transitions.
