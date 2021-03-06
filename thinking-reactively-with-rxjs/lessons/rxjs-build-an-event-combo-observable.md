Instructor: [00:00] Our virtual manager now tells us that our internal QA team needs to disable the spinner while testing certain features. The way we're going to do that is that whenever somebody quickly presses a secret key combination on the keyboard, the spinner will get disabled.

[00:16] Let's try and understand that requirement and see how we can break it down into simple English statements. Assume that we have a list of keys, A, S, D, and F that the user has to type within a certain time limit. Our first thought is that we don't need to do anything until the combo has started.

[00:32] How do we know that a combo has started? Well, we wait for the user to press the A key and once they press that, then we are in combo mode. At this point, we need to listen really closely to see if they're correctly pressing the rest of the keys in the combo. Let's write that out.

### Entering Combo Mode
![Entering Combo Mode](https://res.cloudinary.com/dg3gyk0gu/image/upload/v1585168487/transcript-images/egghead-build-an-event-combo-observable-entering-combo-mode.jpg)

[00:48] `whenever somebody starts a combo` - `keep taking(listening) for the rest of the combo keys`. How long do we want to listen for keys? What are going to be the conditions taking us out of the combo mode again?

### EventCombo.js
```js
/*
  whenever somebody starts a combo
    keep taking(listening) for the rest of the combo keys
*/
```

[01:00] Well, time is the first one. If the user hasn't finished the combo within a certain time limit, the combo will fail. Let's write that out, `until the timer has run out`. Let's say that the user starts the combo again. They press the first letter in the combo, then they press the second, S, the third, D, but then on the fourth, they press W, which is the wrong key, so they miss out.

```js
/*
  whenever somebody starts a combo
    keep taking(listening) for the rest of the combo keys
      until the timer has run out
*/
```

[01:24] That's our second condition, combo was not followed correctly. Write that out, `while the combo is being followed correctly`. Now, the user presses A again to start the combo a third time. They then quickly press S, D, and F to complete the combo. Our third condition can be, `and until they've reached the end of the combo`.

```js
/*
  whenever somebody starts a combo
    keep taking(listening) for the rest of the combo keys
      until the timer has run out
      while the combo is being followed correctly
      and until they've reached the end of the combo
*/
```

[01:47] We can be more specific than that. How do we know we got to the end of the combo? We know because we got back three letters while in combo mode. Why does getting three letters represent the completion of the combo?

### End of Combo Mode
![End of Combo Mode](https://res.cloudinary.com/dg3gyk0gu/image/upload/v1585168487/transcript-images/egghead-build-an-event-combo-observable-end-of-combo-mode.jpg)

[02:00] If our previous two conditions, the one that keeps track of time and the one that keeps track of whether the combo is being followed correctly, have let us get to all the three keys in our combo, and they have not cut us off, that means that the combo is successful.

[02:16] Three is actually the total length of the combo, which is four minus one, because we don't count the first letter. We can go back and revise our condition to, `and until we got (comboLength - 1) keys back`.

```js
/*
  whenever somebody starts a combo
    keep taking(listening) for the rest of the combo keys
      until the timer has run out
      while the combo is being followed correctly
      and until we got (comboLength - 1) keys back
*/
```

[02:31] I'll now declare an *Observable* called `anyKeyPresses`. I'll use the `fromEvent()` *Observable* factory that allows us to pass a DOM element and the event we want to listen for. In our case, we want to listen for key presses (`'keypress'`) on the whole `document` object. Because we get raw DOM events from this, we're going to extract the underlying `key` from each `event`.

```js
/*
  whenever somebody starts a combo
    keep taking(listening) for the rest of the combo keys
      until the timer has run out
      while the combo is being followed correctly
      and until we got (comboLength - 1) keys back
*/

const anyKeyPresses = fromEvent(document, 'keypress').pipe(
  map(event => event.key)
)
```

[02:54] I'm also going to create an *Observable* factory called `keyPressed`, which is going to take in a `key` as the input and it's going to return an *Observable* that emits anytime that specific key is pressed. Cool. Now that we have our building blocks, we can start assembling them to solve our problem.

```js
const anyKeyPresses = fromEvent(document, 'keypress').pipe(
  map(event => event.key)
)

function keyPressed(key) {
  return anyKeyPresses.pipe(filter(pressedKey => pressedKey === key));
}
```

[03:11] To make this truly useful and not lock it down to a single set of keys, we're going to have a *function* that can be invoked with an array of keys and will return an *Observable* that will emit anytime a combo involving these four keys in order has happened. I'll define the `function` and move our requirements right above it so we can keep track of them.

```js
/*
  whenever somebody starts a combo
    keep taking(listening) for the rest of the combo keys
      until the timer has run out
      while the combo is being followed correctly
      and until we got (comboLength - 1) keys back
*/

function keyCombo(keyCombo) {

}

const comboTriggered = keyCombo(["a", "s", "d", "f"])
```

[03:33] The `comboInitiator` will be the first letter from our combo. Whenever the `comboInitiator` is pressed, we want to `switchMap()` to another *Observable* that only becomes alive when the combo has started.

```js
function keyCombo(keyCombo) {
  const comboInitiator = keyCombo[0];
  return keyPressed(comboInitiator).pipe(
    switchMap(() => {
      //WE ARE NOW IN COMBO MODE
    })
  )
}
```

[03:46] Once we're in combo mode, we want to continue listening for key presses and `takeUntil` our timer has expired. In this case, if the user hasn't completed the combo within three seconds, this inner *Observable* will be disposed of and they're going to need to start again from scratch and keep taking while the combos being followed correctly.

```js
function keyCombo(keyCombo) {
  const comboInitiator = keyCombo[0];
  return keyPressed(comboInitiator).pipe(
    switchMap(() => {
      //WE ARE NOW IN COMBO MODE
      return anyKeyPresses.pipe(
        takeUntil(timer(3000))
      )
    })
  )
}
```

[04:08] `takeWhile()` the key that was pressed `keyPressed` matches the combo `keyCombo`. *takeWhile()* is an operator that keeps its source alive, as long as the *function* passed to it keeps returning true. It calls this *function* with each new element from the source, but it also passes in the index from the emission starting from zero.

```js
function keyCombo(keyCombo) {
  const comboInitiator = keyCombo[0];
  return keyPressed(comboInitiator).pipe(
    switchMap(() => {
      //WE ARE NOW IN COMBO MODE
      return anyKeyPresses.pipe(
        takeUntil(timer(3000)),
        takeWhile((keyPressed, index) => keyCombo[index + 1] === keyPressed)
      )
    })
  )
}
```

[04:28] The reason we do `index + 1` in here is because the first element of our inner combo will be at index one. The first time this is called, index will be zero. User presses A, we start the inner combo. They then press S, this gets called with S and index zero. We check if S matches the letter that is at position one in our combo, which it is and so on for the rest of the keys.

[04:57] Finally, we want to *takeUntil* we get *comboLength - 1* keys back. `take(keyCombo.length - 1)`. *take()* keeps its source alive until it receives *keyCombo.length - 1* emissions. After that, it just terminates the source.

```js
function keyCombo(keyCombo) {
  const comboInitiator = keyCombo[0];
  return keyPressed(comboInitiator).pipe(
    switchMap(() => {
      //WE ARE NOW IN COMBO MODE
      return anyKeyPresses.pipe(
        takeUntil(timer(3000)),
        takeWhile((keyPressed, index) => keyCombo[index + 1] === keyPressed),
        take(keyCombo.length - 1)
      )
    })
  )
}
```

[05:15] Let's try this out. I'll declare an `interval`, which will emit every second and I'll keep taking values from it until the combo is triggered (`comboTriggered`). I'll `subscribe` to `console.log(x)` notifications and I'll also log when it completes.

```js
const comboTriggered = keyCombo(["a", "s", "d", "f"])

interval(1000).pipe(
  takeUntil(comboTriggered)
).subscribe({
  next: x => console.log(x),
  complete: () => console.log("COMPLETED")
})
```

[05:30] Just so you can see what I'm pressing on the keyboard, I'll log out values anytime I press a key. Also don't forget our imports.

```js
import { fromEvent, timer, interval } from "rxjs";
import {
  map,
  tap,
  filter,
  switchMap,
  takeUntil,
  takeWhile,
  take
} from "rxjs/operators";

const anyKeyPresses = fromEvent(document, 'keypress').pipe(
  map(event => event.key)
  tap(console.log)
)

...

interval(1000).pipe(
  takeUntil(comboTriggered)
).subscribe({
  next: x => console.log(x),
  complete: () => console.log("COMPLETED")
})
```

If I bring in the app, you can see that we get values each second. I'll press A, S, D and F and we can see that the timer definitely stopped. We also see that it completed only after the second letter S and not when the full combo finished.

### Console Output
![Console Output](https://res.cloudinary.com/dg3gyk0gu/image/upload/v1585168496/transcript-images/egghead-build-an-event-combo-observable-console-output.jpg)

[05:53] What happened there is that the first key was pressed correctly, combo was initiated. Then, we press another correct key within our time limit so both of these let that value go through. Because we hadn't reached our limit for take, it went through this one as well.

[06:09] Our second letter was immediately emitted as a notification, which triggered the *takeUntil*. What we really want is instead of taking all these three values and letting all of them go through, we want to skip the first two and only take the last one if we get to it. If the combo is bigger, we want to skip the first four and only take the last one.

### Small Combo
![Small Combo](https://res.cloudinary.com/dg3gyk0gu/image/upload/v1585168493/transcript-images/egghead-build-an-event-combo-observable-small-combo.jpg)

### Large Combo
![Large Combo](https://res.cloudinary.com/dg3gyk0gu/image/upload/v1585168484/transcript-images/egghead-build-an-event-combo-observable-large-combo.jpg)

[06:33] Basically, we want to skip the first *combo length - 2* values inside the inner combo and only take the last one. In that case, I'll add the `skip(keyCombo.length - 2)` right before the `take()` and then I'll just take the last `1`.

### Our Plan
![Our Plan](https://res.cloudinary.com/dg3gyk0gu/image/upload/v1585168492/transcript-images/egghead-build-an-event-combo-observable-our-plan.jpg)

```js
function keyCombo(keyCombo) {
  const comboInitiator = keyCombo[0];
  return keyPressed(comboInitiator).pipe(
    switchMap(() => {
      //WE ARE NOW IN COMBO MODE
      return anyKeyPresses.pipe(
        takeUntil(timer(3000)),
        takeWhile((keyPressed, index) => keyCombo[index + 1] === keyPressed),
        skip(keyCombo.length - 2),
        take(1)
      )
    })
  )
}
```

[06:51] If these two conditions don't terminate our combo prematurely, we're going to keep skipping elements right until we get to the last one. If that last one is correct, we're going to take it and emit it as a notification. We should now only be triggering the *takeUntil* if we successfully got to the end of the combo.

[07:10] Let's test that again. The timer starts, I'll start pressing keys and once we got to the last one, it completes. We can see that it actually completed once we press the combo keys in the correct order. 

### Complete Combo
![Complete Combo](https://res.cloudinary.com/dg3gyk0gu/image/upload/v1585168499/transcript-images/egghead-build-an-event-combo-observable-complete-combo.jpg)

[07:24] I'll now try to refresh and I'll press the first letter correctly, but then press the wrong letter and then continue pressing the correct ones. Now you can see that the interval keeps going. What happened there is I press the first one, but then I broke the combo and then it didn't matter that I continue them press the letters correctly. The combo didn't complete.

### Invalid Combo
![Invalid Combo](https://res.cloudinary.com/dg3gyk0gu/image/upload/v1585168500/transcript-images/egghead-build-an-event-combo-observable-invalid-combo.jpg)

[07:45] If I refresh again, I start the combo but then wait for three seconds and then press the rest of the combo, again, it's not going to complete. It's going to continue notifying.

### Slow Combo
![Slow Combo](https://res.cloudinary.com/dg3gyk0gu/image/upload/v1585168497/transcript-images/egghead-build-an-event-combo-observable-slow-combo.jpg)

[07:56] Again, even though we did press all of the keys in the combo, because there was such a huge gap between the first letter and the second letter, we didn't complete the combo correctly, so it works.

[08:08] To recap, we spent time to really understand our requirement and break it down into small problems that are defined as simple English sentences. We then separated our combo starting condition from the rest of the logic as that allows us to think in isolation and focus just on the question of what actually terminates the combo.

[08:30] We then learned about the various take operators and how they can be used to dispose of the source when different events happen.
