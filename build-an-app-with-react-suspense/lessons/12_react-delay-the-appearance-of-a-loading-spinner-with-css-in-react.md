Instructor: [0:00] Our Pokemon component is doing a terrific job of indicating a delay for slow connections. We're using useTransition, taking the isPending Boolean that we get back from it and using it to disable the button and render a delay spinner.

[0:17] That's all great, but, unfortunately, on faster connections, this looks pretty terrible. As we click Next, the fetch happens really quickly, and we see just a split second of that loading spinner.

[0:29] Ideally, we'd like to delay that spinner from showing for a certain duration. In the future, it's likely that this useTransition hook will have an option for delaying that, but as for right now, the documented path is to handle that in CSS.

[0:44] Let's go to our delay spinner components, where we were using CSS animations to spin this emoji. This isn't a CSS workshop, but I will show you one line of CSS that might come in handy for these types of components.

[1:00] We'll add an animation. It takes  0 seconds, it's linear. We'll wait for .5 seconds forwards, because we don't want it to loop, and we will give it a name like makeVisible. Now we just make our component invisible by default and add a new animation.

[1:22] This is where we use that name makeVisible, and I'll animate two visibility visible. You don't need to understand how this `@keyframes` line works, or this `0s linear 0.5s forwards makeVisible` line works, but you can copy and paste it and it will work for you when you want to delay the presentation of these little spinners.

```js
function DelaySpinner() {
  return (
    <span role="img" aria-label="spinner" className="DelaySpinner">
      <style>{`
        .DelaySpinner {
          animation: 0s linear 0.5s forwards makeVisible, rotation 1.5s infinite linear;
          display: inline-block;
          font-size: .7rem
        }
        @keyframes makeVisible {
           to {
          visibility: visible;
          }
        }
        @keyframes rotation {
          from { transform: rotate(0deg) }
          to { transform: rotate(359deg) }
        }
      `}</style>
      🌀
    </span>
  );
}
```

[1:47] Let's click that Next button again on our fast connections and we don't see anything. Let's try it again on our medium speed connection. We see it popping for just a split second, but it's after our delay of .5 seconds. On a super slow connection, we'll see a short delay, the spinner, and then our receded state.

[2:11] Something you will notice is that this all feels a lot faster now that we have these intermediate states, the immediate feedback, the secondary feedback and the third feedback. We can go in and make our timeout a lot longer. Let's save this and make it 3 seconds.

```js
let [pokemonResource, setPokemonResource] = React.useState(initialPokemon);
let [startTransition, isPending] = React.useTransition({ timeoutMs: 3000 });
let pokemon = pokemonResource.read();
```

[2:31] On our slow connection, we have enough states where it doesn't feel super long, even though the timeout threshold is 3 seconds.

[2:45] If you want to get real fancy, you could add a prop on `<DelaySpinner />` to customize the delay duration. I'll leave that as an extra credit for you.
