Instructor: [0:00] Let's take a second and make sure that we understand what's going on between our promises, our promise wrappers, and Suspense and error boundary components.

[0:10] At the end of the day, import is just a fancy promise and promises can have three statuses -- pending, rejected and resolved. To prove that there's no magic, let's mock up all of these statuses manually. Duplicate and replace.

[0:34] Let's start with the rejected status. In place of our import, we'll just return a rejected promise. We can see the change in our browser and see that this renders the error boundary fallback. Displaying couldn't catch him all.

```js
//const PokemonDetail = React.lazy(() => import("./pokemon-detail");
const PokemonDetail = React.lazy(() => Promise.rejected());
```

[0:54] Next is the pending status. For this, we return a new promise with a resolve function. Renews set timeout to simulate some type of one second network delay and call that resolve function when we're done. Format and save.

```js
//const PokemonDetail = React.lazy(() => import("./pokemon-detail");
const PokemonDetail = React.lazy(
  () => new Promise(resolve => setTimeout(resolve, 1000))
);
```

[1:15] We see that updated to loading Pokemon enacting our Suspense boundary fallback and displaying loading Pokemon.

[1:24] Finally, we have the resolved status. This is where the function is called that returns our module with our component definition. We can just fake it with a div that says fakePokemon. Format and save.

```js
//const PokemonDetail = React.lazy(() => import("./pokemon-detail");
const PokemonDetail = React.lazy( 
  () => 
  new Promise(resolve => 
    setTimeout(
    () => resolve({ default: () => <div>[Fake] Pokemon</div> }),
    1000
    ) 
   ) 
 );
```

[1:42] Here, we see fakePokemon showing what happens when we get all of the data that we need. The latency is passed, and our module is resolved.

[1:52] I want to show you all this so that you can keep in mind how each of these promise statuses results in what's rendered. Pending will render the Suspense fallback. Rejected, the error boundary fallback. And resolved, the component that we meant to render all along. Understanding this will become critical as we write promise wrappers of our own to fetch data.
