Instructor: [00:00] We solved this problem and now we can move up one floor of obstruction. I'll copy this to our source page, and I'll copy this comment over here, just to mark that we're moving up one level of obstruction in our code as well. We're building an observable that's going to answer this question. Let's name it accordingly.

### TaskProgressService.js
```js
/*
When does the loader need to hide?

When the count of async tasks goes to 0
*/

const shouldHideSpinner = 

export default {};
```

[00:22] When the count of async tasks goes, we'll start with our `currentLoadCount` and once that goes to 0, we want to emit. I'll `pipe()` this to the `filter()` operator. We'll just go to the top and `import` it. It's only going to let values through that are 0.

### TaskProgressService.js
```js
import {
  mapTo,
  scan,
  startWith,
  distinctUntilChanged,
  shareReplay,
  filter
} from "rxjs/operators";

...

/*
When does the loader need to hide?

When the count of async tasks goes to 0
*/

const shouldHideSpinner = currentLoadCount.pipe(
  filter(count => count === 0)
)
```

[00:38] We don't care that this (`shouldHideSpinner`) emits 0. We don't care what it emits. We just care when it emits because that's the time to hide the spinner.

[00:46] Let's pick our second requirement. I'll just paste it here. Now we'll build an *Observable* that answers this question, . We'll name it `shouldShowSpinner`. Again, we need to listen to the count. We'll `pipe()` this to a `filter()` function again that will only let emissions go through when the load is 1.

```js
/*
When does the loader need to show?

When the count of async tasks goes from 0 to 1
*/

const shouldHideSpinner = currentLoadCount.pipe(
  filter(count => count === 0)
);

const shouldShowSpinner = currentLoadCount.pipe(
  filter(count => count === 1)
);
```

[01:05] This is not really right because we can go from a count of 2 to a count of 1 and then this (`shouldShowSpinner`) will emit, but that doesn't really mean that we have to show it. It's already showing. This part is really important.

[01:18] We need to keep track of the previous value, as well as the current one and only emit when the previous is 0 and the current is 1. This is not going to work as it is. `filter()` only works with the current value. How do we keep track of the previous?

```js
/*
When does the loader need to hide?

When the count of async tasks goes to 0
*/

const shouldHideSpinner = currentLoadCount.pipe(
  filter(count => count === 0)
);

const shouldShowSpinner = currentLoadCount.pipe(
  filter((prevCount, currCount) => prevCount === 0 && currCount === 1))
);
```

[01:32] Well, `scan()` is one option. It allows us to keep track of previous state, but RxJS has a lot of operators that are named really well. If we can pretend for a moment that we just looked through the RxJS API, we notice that we can `import` the `pairwise` operator.

[01:50] If I go back and add it, we'll see that it emits a tuple of the previous and the current value. We're just going to use some destructuring here to get the previous (`prevCount`) and the current count (`currCount`) from *pairwise*.

```js
import {
  mapTo,
  scan,
  startWith,
  distinctUntilChanged,
  shareReplay,
  filter,
  pairwise
} from "rxjs/operators";

...

/*
When does the loader need to hide?

When the count of async tasks goes to 0
*/

const shouldHideSpinner = currentLoadCount.pipe(
  filter(count => count === 0)
);

const shouldShowSpinner = currentLoadCount.pipe(
  pairwise(),
  filter(([prevCount, currCount]) => prevCount === 0 && currCount === 1))
);
```

[02:01] Even though we could have done this with `scan()`, with `pairwise()` we signal our intent much better to other developers reading this. The more operators we know, the bigger the chance that we're going to find a nicely named obstruction that signifies intent much better than a custom solution.