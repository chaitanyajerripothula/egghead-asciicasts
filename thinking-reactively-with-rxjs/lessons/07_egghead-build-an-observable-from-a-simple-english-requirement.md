# Build an observable from a simple english requirement

[Video link](https://www.egghead.io/lessons/egghead-build-an-observable-from-a-simple-english-requirement)

Instructor: [00:00] We started from some very low-level terms like tasks starting or ending. We went up our floors tackling one small problem at a time creating higher and higher-level abstractions that eventually brought us to being able to solve our top-level requirement. We now have all the pieces we need for this. Let's go and assemble it.

### Obstruction Levels Diagram
![Obstruction Levels Diagram](../images/egghead-build-an-observable-from-a-simple-english-requirement-obstruction-levels-diagram.png)

[00:19] I'll add a comment for this new layer as well. When the spinner needs to show, show the spinner. Remember, we consider `showSpinner` to be an observable that when activated, shows the spinner. We're not going to worry yet about how that's going to work. When a task starts, switch to displaying the spinner until -- and I'll `pipe()` this to a take until -- until it's time to hide it.

### TaskProgressService.js
```js
// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx //

/*
When the spinner needs to show
    → show the spinner..until it's time to hide it
*/

shouldShowSpinner.pipe(
  switchMap(() => showSpinner.pipe(takeUntil(shouldHideSpinner)))
)

export default {};
```

[00:48] We don't really care what or when this *Observable* emits. This is meant to be a top-level overview of how everything is wired up. We don't really need to reuse it, but we do want to activate it. I'll just `.subscribe()` to it in here.

```js
shouldShowSpinner.pipe(
  switchMap(() => showSpinner.pipe(takeUntil(shouldHideSpinner)))
).subscribe();
```

[01:02] The moment somebody *imports* this file, this *Observable* will be activated. Yes, it will live on for the duration of our app. We never *unsubscribe* from it, but that's something we're OK with because we do want this to keep tracking tasks for the lifetime of our app.

[01:16] To recap, we've marked our low-level building blocks and we kept assembling them into more powerful building blocks, but we also made sure not to dump everything in the same stream. Instead, obstruct the obstructions that make sense on their own.

[01:32] Not only does this keep our RxJS code more readable, almost flowing like an English sentence, but it also allows us to reuse some of these should we need to do so later. And we will need to.