Instructor: [00:00] We have this component, and it's using useTransition to deprioritize this setState update in a way that keeps our UI responsive to user interaction, but we have a problem. You might not be able to see it right now, but let's slow things down a little bit.

[00:19] When we click the Next button, our entire component goes back to the loading state. Click Next, loading. Next, loading.

[00:30] That's because as we set our Pokemon, our Suspensified fetch request is saying that this component is loading again. Even though we have a Pokemon and a button from the previous rendering, this entire component's state is pending. At app.js level, where we have a Suspense boundary, the fallback is being enacted, and we're showing "Loading Pokemon."

[01:00] **Seeing the loading state again when we have had a previous state is what's known as the receded state**. You can read more about it on the React docs.

[01:10] What's important to know is that this is React's default state. Fortunately, useTransition has given us a way to configure how long we're willing to wait on the previous rendering before switching to this receded state. We control that with timeoutMS option.

[01:30] To get that going, we pass in an object to our useTransition hook. The key we'll use is timeoutMS. The value is how long we're willing to wait on the previous rendering. I'll use 1,000 milliseconds. Let's go back to our app and see what happened.

```js
let [pokemonResource, setPoekmonResource] = React.useState(initialPokemon)
let [startTransition] = React.useTransition({ timeoutMs: 1000});
let pokemon = pokemonResource.read();
```

[01:49] Now when I click Next, React holds on to the previous rendering of this component for 1,000 milliseconds. If we slow things down even further and this transition takes longer than 1,000 milliseconds, React will switch to the receded state after that 1,000-millisecond timeout.

[02:15] We click, wait a second, see the loading state, new state. Click, wait a second, see the loading state, new state.

[02:23] In Ideal conditions, we would see no transition at all. Next, next, next. That is the timeoutMS option for useTransition, giving you fine grain control over how long you're willing to wait on the view that you have now before transitioning to the next view.
