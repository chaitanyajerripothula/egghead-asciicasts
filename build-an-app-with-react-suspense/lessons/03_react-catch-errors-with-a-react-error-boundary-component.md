Instructor: [00:00] We're using a dynamic import and Suspense to lazily load this PokemonDetail component. It's pretty dang cool. Unfortunately, not all promises resolve. Some get rejected. It could be the fault of a network error or a server error, but things are bound to go sideways, eventually.

[00:24] How can we catch any errors and gracefully tell the user that something went wrong? Let's start by making an error and see what happens. Let's duplicate `const PokemonDetail = React.lazy(() => import("./pokemon-detail"));` real quick and instead of importing here, let's just return a rejected promise. Save, and this is what our customers see. Nothing.

```js
import React from "react";
const PokemonDetail = React.lazy(() => Promise.reject());
```

[00:48] If one thing is true about React, is that the errors are fantastic. Let's open up a console to see what might be hiding here for us. Go to the errors and we see three errors.

[01:00] First, the uncaught exception that made the page blow up, and second, a really great error. The above error occurred in one of your React components. They provide a stack trace for us. App, div, Suspense and the unknown component.

[01:17] Here's the advice. Consider adding an error boundary to your tree to customize error handling behavior. Visit [this special URL](https://reactjs.org/docs/error-boundaries.html) to learn more about error boundaries. I love it when they sneak a URL into the console.

[01:33] On this doc, you'll find everything that you need to know about error boundaries, the philosophy, practices, and how to customize them. Truth be told, you might not really need them. You can just copy and paste this example and go from there. I've got it, let's put it in our code.
'
```js
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    // Update state so the next render will show the fallback UI.
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    // You can also log the error to an error reporting service
    logErrorToMyService(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      // You can render any custom fallback UI
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children; 
  }
}
 ```

[01:52] One thing you will have to change is logToMyErrorService(). In a production app, you should have one of these, but for our purposes, we can just use console.log() or, better yet, console.error().

```js
componentDidCatch(error, errorInfo) {
    // You can also log the error to an error reporting service
    console.error(error, errorInfo);
  }
```

[02:07] All that's left is to see how to implement it. We just need to wrap the error boundary component around a part of our tree that may be throwing an error. Let's start by wrapping it around the entire component tree.

```js
 export default function App {
return (
  <ErrorBoundary>
    <div>
    <h1>Pokedex</h1>
        <React.Suspense fallback={"Loading your Pokemon..."}>
          <PokemonDetail />
        </React.Suspense>
      </div>
  </ErrorBoundary>
  )
}
```

[02:25] Go back to our app, and we'll notice a few changes. First, we get this new fancy error, which we are displaying from our error boundary component. Second, we see that the callstack is still provided to us, but without the URL, because now we are using the error boundary. Third, we see the error that would have been provided to our error logging service, if we weren't using console.error.

[02:50] Now that we're catching and handling the error, there are a few things that we can do to improve the user experience.

[02:57] First off, this error boundary doesn't need to be at the top of the component. If we move it closer to our React Suspense code, reformat and save, we'll see that we can prevent other parts of the application from being interrupted, just because we got an error here. That's pretty great. It protects the rest of our application from anything that might go wrong in one component tree.

```js
 export default function App {
return (
  <div>
    <h1>Pokedex</h1>
    <ErrorBoundary>
      <React.Suspense fallback={"Loading your Pokemon..."}>
        <PokemonDetail />
      </React.Suspense>
    </ErrorBoundary>
  </div>
  );
}
```

[03:23] We're going to also customize this to take some type of customizable fallback, just like our Suspense component. I'll say something pity like, "Couldn't catch 'em all."

```js
 export default function App {
return (
  <div>
    <h1>Pokedex</h1>
    <ErrorBoundary fallback="Couldn't catch 'em all.">
      <React.Suspense fallback={"Loading your Pokemon..."}>
        <PokemonDetail />
      </React.Suspense>
    </ErrorBoundary>
  </div>
  );
}
```

Jump in to our error boundary code, cut <h1>Something went wrong</h1> and use this.props.fallback.

```js
render() {
    if (this.state.hasError) {
      // You can render any custom fallback UI
      return this.props.fallback;
    }
  ```

[03:47] If we want to make sure that something gets presented regardless of whether or not we put a fallback in, we can use static, default props and define our fallback. Something went wrong.

```js
static defaultProps = {
  fallback: <div>Something went wrong.</div>
}
```

[04:04] As I've said before, you really only need one of these Error Boundary code for an entire application, so we can put this in another file for import elsewhere. Let's open up our tree view, create a new file, error-boundary, and paste.

[04:21] We'll have to make two changes. We'll need to export defaults for our class component, and, because of the JSX, we'll need to import React.

```js
import React from "react";

export default class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  ...
}
```
[04:37] Back in app.js, we can now import our error boundary from our local path.

```js
import ErrorBoundary from "./error-boundary"
```

[04:46] You can lazy load those components with confidence knowing that no matter what happens in your component, you can catch it, present a fallback, and protect the rest of your application.
