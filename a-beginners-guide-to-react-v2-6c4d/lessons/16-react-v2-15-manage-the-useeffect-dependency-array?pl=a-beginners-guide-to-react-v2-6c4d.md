Kent: 0:00 We've added a little bit here to simulate another optimization that we want to make. Here now, we have a count of state, and that's just being rendered into this button, and every time we click on that button, it re-renders our app component, which triggers a re-render of our greeting component. Every time our greeting component re-renders, this useEffect is going to be called.

0:20 If we console.log right here, greeting useEffect, then we'll save that, open up our dev tools, and we see we get the greeting useEffect for the initial render, and every time I click this, we get another greeting useEffect.

0:35 But the effect that we're running is updating local storage to set the name to the name value. This side effect, it doesn't actually need to be run, because the name value hasn't changed. We only need to synchronize the local storage value with the name value that we have in memory for the state of this component.

0:54 React useEffect accepts a second argument as an optimization to combat this problem. That second argument is a dependency array where you pass all the dependencies for your side effect. This is where you pass anything that you want to make sure you synchronize the state of the world with the state of our application.

1:13 In our case, the only thing that we're trying to synchronize here is the state of local storage, which is the state of the world, with the state of our application, which is the name. For our dependency array, we're going to provide the name.

1:27 If we save that, then we'll notice that we get that greeting useEffects called initially. That's because useEffect is always going to be called on the initial mount, and then hereafter, when we click on this button, we're not going to get the greeting useEffect.

1:44 Then when we update the name value, we're going to get greeting useEffect called, because the name has changed and the state of the world is now out of sync with the state of our application, and so React is going to call our useEffect callback.

1:58 It's very important that you keep this dependency array accurate according to the dependencies that your callback function relies on. For example, we rely on name, and that's why it's important that we keep this dependency list in here.

2:11 If you don't keep this list accurate, then you could be missing out on synchronizing the state of the world with state changes that happen in your app, and your users could lose saved work.

2:21 For example, if I were to remove the name from this array and save that, then we'll get that greeting useEffect called, and then as I type in here, we'll notice that the state of my application is being kept updated, but I'm not getting any console.logs.

2:37 If I refresh, I'm going to be back to where I was before. I, as the user of this application, have lost work, because my code was not properly synchronizing the state of my application with the state of the world outside my application.

2:51 In an effort to help you avoid making mistakes here, the React team has created an ESLint plugin called eslint-plugin-react-hooks, which you can use to not only ensure that that dependency array is kept up to date, but actually keep it up to date automatically for you, using ESLint's fix feature.

3:09 The rule that will help you with this is the exhaustive depths rule. I strongly advice that you use this tool and follow that rule.

3:17 In review, the problem that we were solving here is our effect callback was being called more than it needed to be. I want to make a special note here that just because it was being called more than it needed to be didn't mean we actually had a bug in our application. This is just an optimization to make our application run a little faster. A dependency array may not be necessary in all cases.

3:39 In our case, we could simply add this dependency array with the one dependency that our effect callback relied on. This callback is only called when necessary.


