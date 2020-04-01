Instructor: [00:00] When we use isPending from the useTransition hook, we depend on React a lot. We depend on it to hold on to the previous rendering as we click this Next button and then make that transition at the right time. We don't actually have access to both values. We only know the value after the transition is complete.

[00:21] Fortunately, **React gives us the tools to implement our own isPending and hold on to access to the previous value and the current value. The hook that we use for that is useDeferredValue**.

[00:37] The first argument is the value we're interested in holding on to, which is our resource here. It also takes an object of options, timeoutMS being one of them just like our useTransition hook.

[00:53] Let's jump back to the front here and assign that to `deferredPokemon` resource. Instead of reading from Pokemon resource, we'll read from deferredPokemon resource. 

```js
let defferedPokemonResource = React.useDeferredValue(pokemonResource, {
 timeoutMs: 3000
});
```

Format, save, let's click through and see what changed. Changed to a slower speed.

[01:18] We notice that our spinner is gone, so let's remove `isPending` from our useTransition hook and with it, the `timeoutMS` option and implement our own isPending.

[01:32] Here we'll check to make sure that the deferredPokemon resource isn't the same as the Pokemon resource. Whereas, the Pokemon resource will be pending or the latest value, the deferredPokemon resource will be the previous value until the current value is result. We can implement isPending by checking to make sure that they're not the same.

[01:58] As long as they're not the same, we're in a pending state. Let's save and see what happens. 

```js
let [startTransition] = React.useTransition();
.
.
.
let pokemon = deferredPokemonResource.read();
let isPending = deferredPokemonResource !== pokemonResource;
```

Go back to a slower-ish connection and we get our little spinner back.

[02:15] You may be asking yourself, "Why go through all of this song and dance when I already had isPending from useTransition?" The answer is you don't have to. If ever you find yourself in a position where you need to know what both values are, that's where you'll use `useDeferredValue`.
