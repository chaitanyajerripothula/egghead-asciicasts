Instructor: [0:00] SuspenseList gives us control over how these Suspense components render in the browser. revealOrder allows us to say that we want them to render forwards together or backwards, all of the Suspense components that are children of SuspenseList.

[0:17] Even with all of these options, we can have the problem of too many loading states. Fortunately, **SuspenseList also has a prop for controlling how many loading states you see. That's the tail prop. It has two options, hidden and collapsed**.

[0:34] Hidden doesn't show any loading items and collapsed shows only the next fallback in the list. Of course, by default, we see all of the fallbacks.

[0:45] Let's jump back and change this around a little bit. If we use the tail prop and change it to hidden, first, we won't see any of the loading states. They just pop in when they pop in. No Suspense fallbacks presented.

```jsx
<React.SuspenseList revealOrder="forwards" tail="hidden">
  <React.Suspense fallback={<div>Fetching Pokemon...</div>}>
    <ErrorBoundary fallback={"Couldn't catch 'em all."}>
```

[1:01] Something that I really like to see is collapsed. 


```jsx
<React.SuspenseList revealOrder="forwards" tail="collapsed">
  <React.Suspense fallback={<div>Fetching Pokemon...</div>}>
    <ErrorBoundary fallback={"Couldn't catch 'em all."}>
```

This will show only the next loading state. We're fetching the Pokémon and then connecting to the database. Let's watch that one more time. Fetching Pokémon. Connecting to the database. I find that I'm using this combination right here, revealOrder="forwards" tail="collapsed", all the time.
