# Encapsulate complex imperative logic in a simple observable

[Video link](https://www.egghead.io/lessons/egghead-encapsulate-complex-imperative-logic-in-a-simple-observable)

Instructor: [00:00] Let's finally display the spinner. I'll `import` the *function* `initLoadingSpinner` from this service. The implementation of this doesn't really matter. Imagine we're using an NPM library that shows spinners.

### TaskProgressService.js
```js
import { initLoadingSpinner } from '../services/LoadingSpinnerService';
```

[00:14] As we've seen in the previous lesson, we can't expect a lot of libraries out there to use RxJS. This case is no different. The way this specific library works is when you call the *function* `initLoadingSpinner`, you get back a *promise* of a spinner. When that *promise* resolves, you get access to an instance of the spinner, on which you can call `.show()`.

```js
shouldShowSpinner.pipe(
  switchMap(() => showSpinner.pipe(takeUntil(shouldHideSpinner)))
).subscribe();

const loadingSpinnerPromise = initLoadingSpinner();

loadingSpinnerPromise.then(spinner => spinner.show());
```

[00:36] Let's see that in action. I'll bring in our app, save the file and we can see our spinner at the bottom. Granted, the it is always on the screen now because we've not plugged it into our logic, but it's there, we can see it.

### Application of Spinner
![Application of Spinner](../images/egghead-encapsulate-complex-imperative-logic-in-a-simple-observable-application-of-spinner.png)

[00:48] Once we have the instance, let's set up a `setTimeout()` after a few seconds and then call another method that's available on the spinner, which is `.hide()`. I'll save this, wait for the app to refresh, I'll see it. After a few seconds, it goes away. It works.

```js
loadingSpinnerPromise.then(spinner => {
  spinner.show()
  setTimeout(() => {
    spinner.hide();
  }, 3000)
});
```

[01:06] This is a bit of a weird API, right? It takes a few steps to get the spinner instance and then to *show* it and then finally, to *hide* it. While I'm not using the exact same library, the angular loading spinner from ionic ([Ionic's Loading Spinner Docs](https://ionicframework.com/docs/api/loading)) actually exposes a `loadingController` service.

[01:24] You can call `.crate()` on that loading controller. You can pass it some options and you have to `await` the result because it's a *promise*. When you get back the result, you can call `present()` on it.

[01:34] It's pretty similar to our service and you might end up using something like this. The truth is you can't really know what types of APIs you'll be dealing with when using third party services. You need to be ready for anything.

[01:46] We made the assumption that this is an *Observable* that when switched to, it shows the spinner. When *unsubscribed* from it, it hides the spinner. That fits in very nicely with our whole event flow in here and reads like our initial requirement.

[02:02] We want to keep this intact and avoid polluting it with our multi step API. I'll just scroll a bit up and cut out the `showSpinner` and I'll just move it down here. Now, the *Observable* constructor actually accepts a callback, which will be invoked anytime somebody *subscribes* to our *Observable*.

```js
// xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx //

const showSpinner = new Observable(() => {
  //I've been subscribed to!!
});

shouldShowSpinner
  .pipe(switchMap(() => showSpinner.pipe(takeUntil(shouldHideSpinner))))
  .subscribe();

const loadingSpinnerPromise = initLoadingSpinner();

loadingSpinnerPromise.then(spinner => {
  spinner.show()
  setTimeout(() => {
    spinner.hide();
  }, 3000)
});
```

[02:24] Let's move our code for showing the spinner in here. I'll just get rid of the hiding part. Let's try this out. I'll bring in the app again. Whenever we start a few tasks, we can see the spinner now appearing. When they go back down to zero, it's still there.

```js
const showSpinner = new Observable(() => {
  //I've been subscribed to!!
  const loadingSpinnerPromise = initLoadingSpinner();

  loadingSpinnerPromise.then(spinner => {
    spinner.show()
  });
});

shouldShowSpinner
  .pipe(switchMap(() => showSpinner.pipe(takeUntil(shouldHideSpinner))))
  .subscribe();
```

[02:41] From this *Observable* callback, you can optionally return another *function* that will be called whenever the *Observable* is unsubscribed from.

```js
const showSpinner = new Observable(() => {
  //I've been subscribed to!!
  const loadingSpinnerPromise = initLoadingSpinner();

  loadingSpinnerPromise.then(spinner => {
    spinner.show()
  });

  return () => {
    //oh no, I've been unsubscribed from!
  }
});
```

[02:51] This is the perfect place to get an instance of the spinner again and call `.hide()` on it. Keep in mind that promises cached their values. Once this resolves and we get the spinner instance, by the time this gets called, the spinner instance will have already been cached in the *promise*. This is going to resolve instantly with the same exact instance.

```js
const showSpinner = new Observable(() => {
  //I've been subscribed to!!
  const loadingSpinnerPromise = initLoadingSpinner();

  loadingSpinnerPromise.then(spinner => {
    spinner.show()
  });

  return () => {
    //oh no, I've been unsubscribed from!
    loadingSpinnerPromise.then(spinner => {
      spinner.hide();
    })
  }
});
```

[03:12] Let's try that again. I'll save it, I'll start a few tasks and you can see that once they start going down and they reach zero, our spinner disappears now.

[03:23] To recap, we've seen how to wrap complex JavaScript-based logic inside an observer primitive where we took full advantage of its setup and tear down mechanics. It fits in neatly within our streams that switches to it whenever it's time to show the spinner and disposes of it whenever it's time to hide it.