**THE VIDEO IS CRITICAL FOR THIS LESSON.**

Instructor: 00:00 Sometimes it can be really useful to understand the order in which your code is going to be run when you're using React hooks. I've made this little app that has a checkbox for showing a child. Inside of this box, this will be rendered when that checkbox is checked, and when you click on this button, it's going to increment the count.

00:17 The way that this works is we have a child component that's maintaining a count state. Then that has a whole bunch of useEffects, which are simply logging to the console when the callback is called, and logging to the console in a clean-up function that it provides.

00:32 We create our React element for rendering to the page. Then we log to the console that our render is finished, and then we return that React element we created. We do the same thing for our app component, except here we're maintaining a show child Boolean state. We have all of those useEffects, and then we render a few React elements for rendering this UI.

00:54 Let's go ahead and take a look at what happens when we initially load this page. I'm going to refresh. I'll open up our DevTools, and I'll scroll down there to the `App` so we can follow along in the code with what we're seeing in the console.

01:08 The first thing that we see is this `App: render start`. That's the first thing that happens when we call reactDOM.render our app. It calls our app function component. The next thing that happens is we call react.useState. Immediately, React is going to call this function to retrieve the initial state for our show child state. That's why were getting this `App: useState callback` called.

01:31 Then we call all these `React.useEffects`, but you'll notice that the logs in those are not the next thing that appear in our console. Instead, we create this element, and then we get a log to the console for `App: render end`. Once that happens, React is updating the DOM, and then asynchronously, later it's going to call our useEffect callbacks, `App: useEffect no deps`, one at a time, in the order in which they were called.

01:55 We come up here to the top, and we'll see App: useEffect no deps is the first one that appears. We don't get our clean-up because there's no clean-up necessary yet because, right now, we're just mounting the component. We haven't had any updates yet.

02:08 Next, we get our `App: useEffect empty deps`. We have an empty list of dependencies there. Then `App: useEffect with dep`. That's our show child state. That's the last thing that gets called here. Great.

02:21 Now let's take a look at what happens when we click on show child. Remember that this is the last console log that we saw when we initially mounted the component. If I click show child, then we're going to get an `App: Render start`. When we click show child, that triggers an `onChange` to `setShowChild` to the `checked` value of our checkbox input.

02:44 This set show child is going to trigger a rerender of our app, which is why we get our app render start. We'll come up here to the top again. We'll say app render start, and we'll go through all of this code just like we had at the previous render, except this time you'll notice we don't have an app useState callback. We go straight from app render start to app render end.

03:06 This is because React has already retrieved the initial state value for our show child state, and it doesn't need to retrieve that value again. Anytime you use a function callback for useState, that function is only going to be called when this component is initially rendered, for the rest of the lifetime of that component.

03:24 We go through all of these useEffect calls again. We create our element, and then we logged to the console that the app render has finished. Then React calls our child to start rendering of that child. One thing that I want to stress here is that we're creating our element, which includes creating the child, `{showChild ? <Child /> : null}`.

03:42 You'll notice that we get to this line of code before we start rendering the child. The important thing to remember here is that just because you create a React element doesn't mean that React element's function is going to get called. You're not calling the function. React is. React will only call that function when that component is actually going to be rendered.

04:02 That's really important because you could say `const uiIWillNotRender = <Child />`, and render a whole bunch of these all day long. Unless those things are added to some UI that's actually going to get rendered, all that you're creating is a bunch of React element objects. You won't actually be calling into that child function.

04:25 Let's go ahead and scroll up to our `Child: render start`. Then we get our `useState` callback because this is the first time that this useState is going to be called. It needs to retrieve the initial value of zero. Then we call all these useEffects, just like in our app. We create our element. Then we get a log for this `Child: render end`.

04:47 After the entire DOM has been updated, React is going to start calling our useEffects. It calls them in the order in which they're called but starting at the child component. We get `Child: useEffect no deps` was called. We get the `Child: useEffect empty deps` is called. Then we get the `Child: effect with dep` is called. The dep here is our count value.

05:09 Then we're going to start calling the `App: useEffect` callbacks. You'll notice that we called the clean-up first, and then we call the setup. Here we have the clean-up and then the setup. We also do the same for the clean-up when we have a dependency and then the setup when we have a dependency.

05:31 You'll notice that the clean-up for both of these is called before the setup for both of these. The clean-ups are both called in the order in which they appear, just like the setups. You'll also notice that this useEffect callback and its clean-up were not called.

05:45 That's because useEffect callbacks are only called if they have no dependencies listed or if they have a dependency listed, and one of those dependencies changed. We have a dependency list, but it's empty. Therefore, none of the dependencies changed because it doesn't have any dependencies. This useEffect callback and its clean-up will not be called on updates.

06:07 Let's go ahead and click on this button. We'll remember that `App: useEffect with dep` is the last log that we saw for that last update. When I click on that button, we're going to call `sentCount`. We're providing function updater function where we take the previous value of the state and return the new value of our state. We're going to take the `previousCount` and add one to it.

06:27 This is going to trigger a rerender. Let's take a look at what happens here. I click on that, and you'll notice that the app has none of its logs called. That's because the state update only resides within our `const [count, setCount]` component, so React knows that this component is the only one that needs to be rerendered.

06:45 Let's follow the console logs. First, we start with this `Child: render start`. We call this again, and we don't get a log for our useState callback because this component's already been rendered, and we already retrieved the initial value. We no longer need that initial value, so React doesn't bother calling this function anymore.

07:01 Then we call all these `useEffect` hooks. We create our React element for our UI. Then we call this `Child: render end`. Then our `useEffect` clean-ups are called in order if those particular useEffects need to be rerun. In this case, we haven't listed any dependencies, so this will be rerun on every render of this component.

07:23 We'll get the clean-up there. This one is listing a dependency array, and there are no dependencies that can change, so it's not going to be called. This one does have a dependency array, and that dependency did change, so the clean-up is called here.

07:39 Then we start with the setups for the useEffect that has no dependencies and the useEffect that has a dependency that changed. This is the last one that was called. If we click this again, we'll see the exact same order of calls. Then, if we uncheck show child, let's go ahead and highlight `Child: useEffect with dep`, so we know which one was the last that we called.

08:02 Then we uncheck show child, and that's going to trigger a rerender of our `App` component because we changed that `showChild` state using `setShowChild`. That triggers a render start here. We go through all of this code, again skipping the useState callback. We come down here, create our elements, and then render end right here.

08:26 Then we're doing a clean-up on all of the children because the child is being removed from the page so we're now rendering null. React notices, Hey, the previous JSX that you gave me included the child, and this next JSX that you gave me does not include the child, so that means I need to remove the child from the page.

08:44 And so I'm going to unmount the component and call all of the clean-ups for all of the useEffect that that child had going. Let's come up to `Child: useEffect no deps cleanup`. We'll see that happens in the order in which they appear in the code. We see our no deps clean-up runs. We'll see our empty deps clean-up run.

09:01 Even though we have listed dependencies, but none of them changed because we have no dependencies, our clean-up is going to run because we're unmounting this component. Then we get a clean-up of the effect with dependencies. Even though this count value didn't change, it's going to be called because this component's getting unmounted.

09:19 Because our state changed in the app, we're going to run some clean-up for the useEffects that are relevant. Here we have this clean-up is getting called because it has no dependencies. This clean-up is not getting called because it has an empty dependency array. This clean-up is getting called because the dependency that it has, has changed.

09:40 Then we go ahead and run the effects that are relevant, the useEffect with no dependencies and the useEffect with a dependency that changed.

09:50 I encourage you to play around with this code example. Something else that might help you is this [diagram](https://github.com/donavon/hook-flow) from Donovan. You go to donovan/hookflow on GitHub, scroll down here, and you'll see the diagram that Donovan has created.

10:05 We get our lazy initializers. That's our useState callback. We'll get our render function that finishes after the lazy initializers are all called. React will update the DOM, and then React also has a useLayoutEffect hook that will be run here that operates pretty similarly to the useEffect.

10:23 The browser updates the screen based on the updates to the DOM that React made. Then our effects are going to be called. This is exactly what we observed in our example. When there's a state update, we get our render called. Our lazy initializers are not called.

10:38 React is going to update the DOM. We get a clean-up of layout effects, and then our layout effects are run. Browser updates the screen, then we get a clean-up of our effects, and then our effects are run. When the component is unmounted, we get the clean-up of all of our effects.

10:54 Having a firm understanding on the order in which these things are called is not totally necessary for you to be effective with React, but it can help you in some situations, so I encourage you to play around with this.