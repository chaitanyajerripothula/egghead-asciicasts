Instructor: [0:00] Every promise can be wrapped to communicate its status to Suspense and error boundary components. This is something you'll need to know to wrap data-fetching requests.

[0:12] Let's walk through wrapping the promises returned by Fetch. Let's start by navigating to the pokemon-detail.js component implementation. We have this static text here. Let's start by making that dynamic, reading a Pokemon's name from a variable.

[0:32] Then let's fetch that name by making a request to Poke API for Bulbasaur. Let Pokemon=fetch(our URL), and let's not forget to call a JSON on the response.

```js
import React from "react";

let pokemon = fetch("http://pokeapi.co?api/v2/pokemon/1").then(res => 
res.json()
);

export default function PokemonDetail() {
  return <div>{pokemon.name}</div>;
}
```

[0:57] Just as react.lazy is a wrapper around imports, we need to write a wrapper around. Now, just like react.lazy is a wrapper around import promises, we need to write a wrapper for our fetch promises. We'll call that suspensify. We'll define it as a function, which takes a promise.

[1:33] In the body, we need to solve for our three statuses. Pending, rejected and resolved. We can play around with static data just to make sure that everything is hooked up right.

[1:47] Returning an object with a name fake Bulbasaur. Rejected is also easy to fake. We just throw an error. Couldn't catch them all.

[2:03] Finally, we can enact the pending state by throwing the unresolved promise. Loading Pokémon.

[2:13] To switch between these, let's add a condition before each of them and assign status to a variable. If status is pending, throw the promise. If status is error, throw the error. If status is success, return our data.

[2:43] We'll make this pending by default. 

```js
import React from "react";

function suspensify(promise) {
  let status = "error";
  
}
let pokemon = fetch("http://pokeapi.co?api/v2/pokemon/1").then(res => 
res.json()
);

export default function PokemonDetail() {
  return <div>{pokemon.name}</div>;
}
 ```


Now, we can switch on status. For errors, we see it couldn't catch them all. Success, we see our fake Pokémon.

[2:56] Now we need to write resolve and reject functions for our promise. We'll call this the suspender.

[3:06] To our promise, we chain it on event. We'll give it two callbacks. The first function is called when the promise resolves and we have a response. In that case, we want to change our status to success.

[3:19] The second function is called when the promise is rejected and we get an error. In that case, we want to change our status from pending to error.

```js
function suspensify(promise) {
  let status = "pending";
  let suspender = promise.then(
    response => {
      status = "success";
    },
    error => {
      status = "error"
  });
  //pending
  if(status === "pending") throw suspender;
}
```

[3:33] You'll notice that we see the pending message immediately, but neither of the success or error responses come through. Let's add a log statement to see what's going on.

```js
console.log(status)
//pending
  if(status === "pending") throw suspender;
```

[3:46] Open our console, and we see that this whole function is only run once. It is pending by default and we throw the suspender. The reason for this is we need to give our component a function to call when the promise is resolved and we want to read this information.

[4:03] By convention, this function is called read. To use it, we'll need to wrap up this whole block inside of a function. We'll wrap that function inside of a return object.

[4:20] Format and save. 

```js
return {
  read() {
  console.log(status);
  //pending
  if(status === "pending") throw promise;
  //rejected
  if (status === "error") throw Error;
  //resolved
  if (status === "success") return {name: Fake Bulbasaur};
    }
  };

let pokemon = suspensify("http://pokeapi.co?api/v2/pokemon/1").then(res => 
res.json()
);

export default function PokemonDetail() {
  return <div>{pokemon.read().name}</div>;
}
```

We see now that the pending state was rendered initially, but quickly followed by the success state when that promise resolved.

[4:31] But we still see fake Bulbasaur. We have one more thing that we need to hook up. That's to use our response and error as the returned result. In the success case, the result is the response. In the error case, the result will be the error.

[4:53] Now, we just need to go down to these returns and throws and change this to results in both places.

```js
function suspensify(promise) {
  let status = "pending";
  let results;
  let suspender = promise.then(response => {
    status = "success";
    result = response;
    },
    error => {
      status = "error"
      result = error;
  }
);

return {
  read() {
  console.log(status);
  //pending
  if(status === "pending") throw promise;
  //rejected
  if (status === "error") throw result;
  //resolved
  if (status === "success") return result;
    }
  };
}
let pokemon = suspensify("http://pokeapi.co?api/v2/pokemon/1").then(res => 
res.json()
);

export default function PokemonDetail() {
  return <div>{pokemon.read().name}</div>;
}

```

[5:02] Finally, we see Bulbasaur returned from our fetch promise. It's not critical that you understand all of this, but I believe that you can.

[5:13] If it's not immediately clear to you, watch this video a couple times. Eventually, it'll stick and you'll understand everything you need to know to write suspense wrappers for all your promises. Fortunately, like air boundaries, this will be something that you do once and are done.

[5:32] Hopefully, as suspense comes out of its experimentation phase, we'll see more and more of this wrapped up into libraries like Relay and Apollo.
