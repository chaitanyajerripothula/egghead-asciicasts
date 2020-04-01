Instructor: [00:00] I want to ease you in the Suspense. To do that, we'll take something you've done 1,000 times, which is import a component, and make it a little bit more suspensy.

[00:10] To do that, we'll follow a [blog post](https://reactjs.org/blog/2018/10/23/react-v-16-6.html) that was written all the way back in October 2018 for the version 16.6 of React. The feature is code splitting. Simply by changing from a static import to a dynamic import, we can get our hands wet with Suspense. Let's dive in.

[00:31] First things first, we need to change the import statement. We'll call it as a function using the module path as an argument, PokemonDetail. The result of that import will be assigned to a constant with our desired name for that component, PokemonDetail.

[00:50] There's just one last piece. This dynamic import creates a promise. That promise will have pending error and success statuses. We need to wrap this generic promise in a function that will communicate with Suspense boundaries. That function is React.lazy(). We can copy and paste that in PokemonDetail.

[01:13] React.lazy expects a function, so we can use a handy dandy arrow function here and close our parens. 

```js
import React from "react";
import PokemonDetail from "./pokemon-detail";
const PokemonDetail = React.lazy(() => import("./pokemon-detail")
```

Now that we have our dynamic import in place, we can comment `import PokemonDetail from "./pokemon-detail"` out or delete it completely. Let's mozy on back to our app in the browser and see what happened.

```js
import React from "react";
const PokemonDetail = React.lazy(() => import("./pokemon-detail")
```

[01:31] Kaboom, the app blew up, but React gives us a great error telling us exactly what we need to do when using lazily loaded components. Let's read it. Error. "A React component suspended while rendering, but no fallback UI was specified. Add a <Suspense fallback=...> component higher in the tree to provide a loading indicator or placeholder to display."

[01:56] We just need to follow steps. Add a <Suspense fallback=...> component higher in the tree. Top and bottom, check. Provide a fallback prop to display a placeholder. For now, we can just use the text "Loading Pokemon." Save and fixed.
  
 ```js
 <h1>Pokedex</h1>
 
  <React.Suspense fallback={"Looking up Pokemon..."}>
  <PokemonDetail />
 </React.Suspense>
  ```

[02:19] Let's check our work. In the browser, open up the dev tools. Go to the network pane and filter on JS. Now, hit refresh. There are number of files here, but there are two that I want to point out.

[02:31] First is the main.chunk.js. It's all compiled and ugly, but you can see in here hints of this main application(app.js) component. What you won't see is anything related to the PokemonDetail. That came in a separate chunk. This chunk is splitting on that component boundary for PokemonDetail and only has the code for this one component.

[02:56] But what about this fallback? Everything loaded so quickly that we never saw it. We can check for it in two ways. Staying in the network tab, change to a slow 3G connection. Hit refresh again. It will take much longer this time, but you'll see, for a split second, "Loading Pokemon" before the component comes in and saves the day.

[03:19] Of course, if you don't want to slow down all of your requests to a screeching halt, you can install the Chrome DevTools, choose the component that you'd like to see in a suspended state and click this little button. That stopwatch will suspend the component and show the Suspense boundary fallback.

[03:39] Congratulations are in order. By changing this from a static to a dynamic import, you've now used your first React Suspense component.
