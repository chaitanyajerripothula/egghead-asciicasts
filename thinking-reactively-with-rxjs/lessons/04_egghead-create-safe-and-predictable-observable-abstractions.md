# Create safe and predictable observable abstractions

[Video link](https://www.egghead.io/lessons/egghead-create-safe-and-predictable-observable-abstractions)

Instructor: [00:00] Our `currentLoadCount` *Observable* is great and useful. Anybody can subscribe to it and they'll get how many tasks are currently loading in their application. Because we can't assume who will subscribe to it, how it will be used, we need to make it work in a very predictable fashion, so it doesn't bring surprises to consumers.

[00:18] Good abstractions are as predictable as possible. If somebody subscribes to this, they won't get anything until this (`loadVariations`) emits a value. Which is not right. We know that the count is 0 initially so might as well emit 0 and then start tracking properly as tasks begin to start and finish. This scenario is captured in our requirements, start from zero and the nice thing about RxJS is that it usually flows like an English instruction. Let me just `import` the `startWith` operator. We want to `startWith(0)` because `startWith` now gives us the initial value, we don't really need the second argument to `scan()`.

### TaskProgressService.js
```js
import { Observable, merge } from "rxjs";
import { mapTo, scan, startWith } from "rxjs/operators";

/*
How do we count?
    Start from zero
    When an async task starts, increase the count by 1
    When a task ends, decrease the count by 1
*/

const taskStarts = new Observable();
const taskCompletions = new Observable();
const showSpinner = new Observable();

const loadUp = taskStarts.pipe(mapTo(1));
const loadDown = taskCompletions.pipe(mapTo(-1));

// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx //

const loadVariations = merge(loadUp, loadDown);

const currentLoadCount = loadVariations.pipe(
    startWith(0),
    scan((totalCurrentLoads, changeInLoads) => {
      return totalCurrentLoads + changeInLoads;
    })
)

export default {};
```

[00:57] To put that differently, if you don't give an initial value to `scan()`, it will just let the first value it gets flow through it. Any consumers to this will get 0 immediately, and this *function* won't even get called for that initial value. Once we start getting more values after that, it will start getting called and it will start adding values to the initial one.

[01:17] Another problem is what happens if for some reason we get way more `taskCompletions` than `taskStarts`? I don't know why that would happen, but this (`currentLoadCount`) would start going into the negative which, again, doesn't really make sense.

[01:29] Let's extract this into a variable and say that if it's smaller than 0, return 0, otherwise, just return the new actual amount. Nice. This doesn't get into the negative. If we do get to the scenario where we get way more completions than starts, this will just keep emitting 0 over and over again which is not a huge problem.

### TaskProgressService.js
```js
import { Observable, merge } from "rxjs";
import { mapTo, scan, startWith } from "rxjs/operators";

/*
How do we count?
    Start from zero
    When an async task starts, increase the count by 1
    When a task ends, decrease the count by 1
*/

const taskStarts = new Observable();
const taskCompletions = new Observable();
const showSpinner = new Observable();

const loadUp = taskStarts.pipe(mapTo(1));
const loadDown = taskCompletions.pipe(mapTo(-1));

// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx //

const loadVariations = merge(loadUp, loadDown);

const currentLoadCount = loadVariations.pipe(
    startWith(0),
    scan((totalCurrentLoads, changeInLoads) => {
      const newLoadCount = totalCurrentLoads + changeInLoads;
      return newLoadCount < 0 ? 0 : newLoadCount;
    })
)

export default {};
```


[01:50] Again, as good abstraction builders, we want to be as predictable as possible to our consumers. I'll go up and `import` the `distinctUntilChanged` operator. I'll use it here to filter subsequent values which are equal.

### TaskProgressService.js
```js
import { Observable, merge } from "rxjs";
import { mapTo, scan, startWith, distinctUntilChanged } from "rxjs/operators";

/*
How do we count?
    Start from zero
    When an async task starts, increase the count by 1
    When a task ends, decrease the count by 1
*/

const taskStarts = new Observable();
const taskCompletions = new Observable();
const showSpinner = new Observable();

const loadUp = taskStarts.pipe(mapTo(1));
const loadDown = taskCompletions.pipe(mapTo(-1));

// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx //

const loadVariations = merge(loadUp, loadDown);

const currentLoadCount = loadVariations.pipe(
    startWith(0),
    scan((totalCurrentLoads, changeInLoads) => {
      return totalCurrentLoads + changeInLoads;
    }),
    distinctUntilChanged()
)

export default {};
```
