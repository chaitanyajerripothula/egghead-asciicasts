Instructor: [0:00] We're using the useTransition hook and setting the optional timeoutMS option for 1,000 seconds. What that's doing is ensuring that if we're on a slowish connection, as we click through these, it'll hold on to the previous Pokemon name rendering as we wait for the next Pokemon to resolve. I'll click, we wait, the next one shows up.

[0:24] That's better than all of this jumping back to the receded state, and us seeing "Loading Pokemon" again. Unfortunately, we don't see anything happen immediately as we click this button. **React gives us a way to indicate that the button was clicked even while we're waiting for more data to arrive.That comes in via the second element of the useTransition hook, which is called by convention isPending**. 

This is Boolean, so we can use it to render UI conditionally. Let's say in our button we wanted to add a couple of dots. We'd say isPending is true will show a couple dots there, save that.

```js
let [pokemonResource, setPokemonResource] = React.useState(initialPokemon);
let [startTransition, isPending] = React.useTransition({ timeoutMs: 1000});
let pokemon = pokemonResource.read();
```

```js
<button
  type="button"
  onClick={() =>
    startTransition(() => 
      setPokemonResource(suspensify(fetchPokemon(pokemon.id + 1)))
    )
  }
  >
    Next {isPending && "..."} 
  </button>
 ```

[1:06] As we click through on our slow connection, we'll see these dots rendered in place. That happens immediately, even though the data hasn't come back yet. As soon as the data does come back, those go away.

[1:18] We can also use `isPending` for props like disabled. If we don't want this button to be pressed again, we can use `isPending` here. While it is true, we won't be able to click this button. 

```js
<button
  type="button"
  disabled={isPending}
  onClick={() =>
    startTransition(() => 
      setPokemonResource(suspensify(fetchPokemon(pokemon.id + 1)))
    )
  }
>
  Next {isPending && "..."} 
</button>
 ```

As I go over here on our slow connection, hit Next, you can see that the button is disabled. I can't touch it again until the next Pokemon arrives back.

[1:44] What I'd really love is a little spinner right over here by the Pokemon name. Let's implement that using isPending. We add another JSX block, `isPending` is true, we'll render `delaySpinner`. Because this isn't a workshop on CSS, I'm going to do you a favor and just paste in a component that I've already made.

```js
return(
<div>
  {pokemon.name} {isPending && <DelaySpinner />}
</div>
)
```

[2:09] At eye level, this is pretty simple. It's just an emoji spinner, wrapped in a span with some ARIA attributes, so that can be read by a screen reader and a class with some inline CSS to make it rotate. As we click through, we'll see a little emoji spinner right up here.

[2:32] That's really nice. It's a little over the top with these dots and this button, so take those out. We have a nice little experience here.

```js
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
```
