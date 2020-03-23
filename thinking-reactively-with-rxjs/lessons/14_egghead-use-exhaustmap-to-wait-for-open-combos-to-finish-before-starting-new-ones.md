# Use exhaustMap to wait for open combos to finish before starting new ones

[Video link](https://www.egghead.io/lessons/egghead-use-exhaustmap-to-wait-for-open-combos-to-finish-before-starting-new-ones)

Instructor: [00:00] But there's still a problem with our implementation. Let's change the combo to have the initiator, the letter A somewhere in the middle as well. Now to trigger the combo, we need to press the letters `'a', 's', 'a'` and finally `'f'`. I'll switch to the app. The timer starts. I press the letters A, S, A, and then F.

### EventCombo.js
```js
const anyKeyPresses = fromEvent(document, 'keypress').pipe(
  map(event => event.key)
  tap(console.log)
)

function keyPressed(key) {
  return anyKeyPresses.pipe(filter(pressedKey => pressedKey === key));
}

/*
    whenever somebody starts a combo
        keep taking(listening) for the rest of the combo keys
            until the timer has run out
            while the combo is being followed correctly
            and until we got (comboLength - 1) keys back
*/

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

const comboTriggered = keyCombo(["a", "s", "a", "f"])

interval(1000).pipe(
  takeUntil(comboTriggered)
).subscribe({
  next: x => console.log(x),
  complete: () => console.log("COMPLETED")
})
```

### Unknown Continuation
![Unknown Continuation](../images/egghead-use-exhaustmap-to-wait-for-open-combos-to-finish-before-starting-new-ones-unknown-continuation.png)

[00:23] It keeps going. It doesn't complete even though we typed the letters in the required amount of time. Why is that? Well, this *Observable* fires whenever the *comboInitiator* is pressed. In our case, the *comboInitiator* is the letter A. When A is pressed, the inner combo starts. We then press S which goes through this and it's all good.

[00:42] Then when we press A again, this oper *Observable* fires. Because of how *switchMap* works, it's going to immediately dispose of this inner *Observable* and just start a brand-new combo. If we have the *comboInitiator* anywhere else in our combo, this will never work.

[00:58] To fix it, I'll replace our `switchMap` with an `exhaustMap`. What *exhaustMap* does is it ignores any notifications from the source until its inner *Observable* has completed. Now when we press the letter A, the inner combo will activate.

### EventCombo.js
```js
const anyKeyPresses = fromEvent(document, 'keypress').pipe(
  map(event => event.key)
  tap(console.log)
)

function keyPressed(key) {
  return anyKeyPresses.pipe(filter(pressedKey => pressedKey === key));
}

/*
    whenever somebody starts a combo
        keep taking(listening) for the rest of the combo keys
            until the timer has run out
            while the combo is being followed correctly
            and until we got (comboLength - 1) keys back
*/

function keyCombo(keyCombo) {
  const comboInitiator = keyCombo[0];
  return keyPressed(comboInitiator).pipe(
    exhaustMap(() => {
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

const comboTriggered = keyCombo(["a", "s", "a", "f"])

interval(1000).pipe(
  takeUntil(comboTriggered)
).subscribe({
  next: x => console.log(x),
  complete: () => console.log("COMPLETED")
})
```

[01:13] Once this is active, any notifications from the source will be ignored until one of these takes ends the inner combo and we're ready to start listening for a new combo. Let's try that out. The timer starts. I'll press A, S, A and then F and it completes. Nice, it works.

### Exhaust Map Output
![Exhaust Map Output](../images/egghead-use-exhaustmap-to-wait-for-open-combos-to-finish-before-starting-new-ones-exhaust-map-output.png)

[01:31] Let's use this to disable our spinner. I'll remove our debug element. I'll `export` the *function* (*keyCombo()*). I'll now go back to our *TaskProgressService.js* and `import` our `keyCombo` from our *EventCombo* package.

### EventCombo.js
```js
const anyKeyPresses = fromEvent(document, 'keypress').pipe(
  map(event => event.key)
  tap(console.log)
)

function keyPressed(key) {
  return anyKeyPresses.pipe(filter(pressedKey => pressedKey === key));
}

/*
    whenever somebody starts a combo
        keep taking(listening) for the rest of the combo keys
            until the timer has run out
            while the combo is being followed correctly
            and until we got (comboLength - 1) keys back
*/

export function keyCombo(keyCombo) {
  const comboInitiator = keyCombo[0];
  return keyPressed(comboInitiator).pipe(
    exhaustMap(() => {
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

### TaskProgressService.js
```js
import { Observable, merge, Subject } from "rxjs";
import {
  mapTo,
  scan,
  startWith,
  distinctUntilChanged,
  shareReplay,
  filter,
  pairwise
} from "rxjs/operators";
import { initLoadingSpinner } from '../services/LoadingSpinnerService';
import { keyCombo } from "./EventCombo";

const taskStarts = new Observable();
const taskCompletions = new Observable();

const showSpinner = (total, completed) => new Observable(() => {
  const loadingSpinnerPromise = initLoadingSpinner(total, completed);

  loadingSpinnerPromise.then(spinner => {
    spinner.show();
  });

  return () => {
    loadingSpinnerPromise.then(spinner => {
      spinner.hide();
    });
  };
});

export function newTaskStarted() {
taskStarts.next();
}

export function existingTaskCompleted() {
  taskCompletions.next();
}

const loadUp = taskStarts.pipe(mapTo(1));
const loadDown = taskCompletions.pipe(mapTo(-1));

// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx //

const loadVariations = merge(loadUp, loadDown);

const currentLoadCount = loadVariations.pipe(
    startWith(0),
    scan((totalCurrentLoads, changeInLoads) => {
      return totalCurrentLoads + changeInLoads;
    }),
    distinctUntilChanged(),
    shareReplay({ bufferSize: 1, refCount: true })
);

// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx //

const loadStats = currentLoadCount.pipe(
  scan((loadStats, loadingUpdate) => {
    const loadsWentDown = loadingUpdate < loadStats.previousLoading;
    const currentCompleted = loadsWentDown ? loadStats.completed + 1 : loadStats.completed;
    return {
      total: currentCompleted + loadingUpdate,
      completed: currentCompleted,
      previousLoading: loadingUpdate
    }
  }, {total: 0, completed: 0, previousLoading: 0})
)

// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx //

const spinnerWithStats = loadStats.pipe(
  switchMap(stats => showSpinner(stats.total, stats.completed))
);

const spinnerDeactivated = currentLoadCount.pipe(
  filter(count => count === 0)
);

const spinnerActivated = currentLoadCount.pipe(
  pairwise(),
  filter(([prevCount, currCount]) => prevCount === 0 && currCount === 1))
);

const flashThreshold = timer(2000);

const shouldShowSpinner = spinnerActivated.pipe(
  switchMap(() => flashThreshold.pipe(takeUntil(spinnerDeactivated)))
)

const shouldHideSpinner = combineLatest(
  spinnerDeactivated,
  flashThreshold
)

// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx //

shouldShowSpinner
  .pipe(switchMap(() => spinnerWithStats.pipe(takeUntil(shouldHideSpinner))))
  .subscribe();

export default {};
```

[01:46] I'll define a new *Observable* called `hideSpinnerCombo`. It's going to be a `keyCombo` of the letters, `'q', 'w', 'e', 'r', 't', 'y'`. Our requirement was that we need to disable the spinner completely when the combo is triggered. It's not enough to just add it to this *takeUntil*. We need to add it right at the tail.

### TaskProgressService.js
```js
import { Observable, merge, Subject } from "rxjs";
import {
  mapTo,
  scan,
  startWith,
  distinctUntilChanged,
  shareReplay,
  filter,
  pairwise
} from "rxjs/operators";
import { initLoadingSpinner } from '../services/LoadingSpinnerService';
import { keyCombo } from "./EventCombo";

const taskStarts = new Observable();
const taskCompletions = new Observable();

const showSpinner = (total, completed) => new Observable(() => {
  const loadingSpinnerPromise = initLoadingSpinner(total, completed);

  loadingSpinnerPromise.then(spinner => {
    spinner.show();
  });

  return () => {
    loadingSpinnerPromise.then(spinner => {
      spinner.hide();
    });
  };
});

export function newTaskStarted() {
taskStarts.next();
}

export function existingTaskCompleted() {
  taskCompletions.next();
}

const loadUp = taskStarts.pipe(mapTo(1));
const loadDown = taskCompletions.pipe(mapTo(-1));

// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx //

const loadVariations = merge(loadUp, loadDown);

const currentLoadCount = loadVariations.pipe(
    startWith(0),
    scan((totalCurrentLoads, changeInLoads) => {
      return totalCurrentLoads + changeInLoads;
    }),
    distinctUntilChanged(),
    shareReplay({ bufferSize: 1, refCount: true })
);

// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx //

const loadStats = currentLoadCount.pipe(
  scan((loadStats, loadingUpdate) => {
    const loadsWentDown = loadingUpdate < loadStats.previousLoading;
    const currentCompleted = loadsWentDown ? loadStats.completed + 1 : loadStats.completed;
    return {
      total: currentCompleted + loadingUpdate,
      completed: currentCompleted,
      previousLoading: loadingUpdate
    }
  }, { total: 0, completed: 0, previousLoading: 0 })
)

// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx //

const spinnerWithStats = loadStats.pipe(
  switchMap(stats => showSpinner(stats.total, stats.completed))
);

const spinnerDeactivated = currentLoadCount.pipe(
  filter(count => count === 0)
);

const spinnerActivated = currentLoadCount.pipe(
  pairwise(),
  filter(([prevCount, currCount]) => prevCount === 0 && currCount === 1))
);

const flashThreshold = timer(2000);

const shouldShowSpinner = spinnerActivated.pipe(
  switchMap(() => flashThreshold.pipe(takeUntil(spinnerDeactivated)))
)

const shouldHideSpinner = combineLatest(
  spinnerDeactivated,
  flashThreshold
)

// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx //

const hideSpinnerCombo = keyCombo(['q', 'w', 'e', 'r', 't', 'y']);

shouldShowSpinner
  .pipe(switchMap(() => spinnerWithStats.pipe(takeUntil(shouldHideSpinner))))
  .subscribe();

export default {};
```

[02:07] I'll do another `takeUntil(hideSpinnerCombo)`. Whenever this is triggered, it's going to completely dispose of everything above it. Once this fires, the spinner should get disabled forever.

### TaskProgressService.js
```js
import { Observable, merge, Subject } from "rxjs";
import {
  mapTo,
  scan,
  startWith,
  distinctUntilChanged,
  shareReplay,
  filter,
  pairwise
} from "rxjs/operators";
import { initLoadingSpinner } from '../services/LoadingSpinnerService';
import { keyCombo } from "./EventCombo";

const taskStarts = new Observable();
const taskCompletions = new Observable();

const showSpinner = (total, completed) => new Observable(() => {
  const loadingSpinnerPromise = initLoadingSpinner(total, completed);

  loadingSpinnerPromise.then(spinner => {
    spinner.show();
  });

  return () => {
    loadingSpinnerPromise.then(spinner => {
      spinner.hide();
    });
  };
});

export function newTaskStarted() {
taskStarts.next();
}

export function existingTaskCompleted() {
  taskCompletions.next();
}

const loadUp = taskStarts.pipe(mapTo(1));
const loadDown = taskCompletions.pipe(mapTo(-1));

// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx //

const loadVariations = merge(loadUp, loadDown);

const currentLoadCount = loadVariations.pipe(
    startWith(0),
    scan((totalCurrentLoads, changeInLoads) => {
      return totalCurrentLoads + changeInLoads;
    }),
    distinctUntilChanged(),
    shareReplay({ bufferSize: 1, refCount: true })
);

// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx //

const loadStats = currentLoadCount.pipe(
  scan((loadStats, loadingUpdate) => {
    const loadsWentDown = loadingUpdate < loadStats.previousLoading;
    const currentCompleted = loadsWentDown ? loadStats.completed + 1 : loadStats.completed;
    return {
      total: currentCompleted + loadingUpdate,
      completed: currentCompleted,
      previousLoading: loadingUpdate
    }
  }, { total: 0, completed: 0, previousLoading: 0 })
)

// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx //

const spinnerWithStats = loadStats.pipe(
  switchMap(stats => showSpinner(stats.total, stats.completed))
);

const spinnerDeactivated = currentLoadCount.pipe(
  filter(count => count === 0)
);

const spinnerActivated = currentLoadCount.pipe(
  pairwise(),
  filter(([prevCount, currCount]) => prevCount === 0 && currCount === 1))
);

const flashThreshold = timer(2000);

const shouldShowSpinner = spinnerActivated.pipe(
  switchMap(() => flashThreshold.pipe(takeUntil(spinnerDeactivated)))
)

const shouldHideSpinner = combineLatest(
  spinnerDeactivated,
  flashThreshold
)

// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx //

const hideSpinnerCombo = keyCombo(['q', 'w', 'e', 'r', 't', 'y']);

shouldShowSpinner
  .pipe(
    switchMap(() => spinnerWithStats.pipe(takeUntil(shouldHideSpinner)))
    takeUntil(hideSpinnerCombo))
  .subscribe();

export default {};
```

[02:19] If I switch back to the app, I'm going to trigger a task. I'll start typing in my combo and the moment I press Y, the spinner hides. If I try to trigger more tasks, we can see that the spinner doesn't even show at all because it's been disabled.

### Disabling Spinner Output
![Disabling Spinner Output](../images/egghead-use-exhaustmap-to-wait-for-open-combos-to-finish-before-starting-new-ones-disabling-spinner-output.png)
