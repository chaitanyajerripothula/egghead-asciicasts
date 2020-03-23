**THE VIDEO IS CRITICAL FOR THIS LESSON.**

Instructor: [00:00] Here I can fill out a Pokémon name like Pikachu. I submit, and I get some Pikachu data. If I make a typo here, then I'm not going to get anything back. It might be useful for me to get an understanding of what's going on behind the scenes in the state of the components that I'm rendering to this page so that I can debug the problems that are happening in my components.

[00:22] What I'm going to do is I'm going to google "React Dev Tools". That'll come up with the Chrome Web Store for "React Dev Tools". **There's also React Dev Tools supported for other browsers**, but I'm using Chrome, so I'm going to add this to Chrome. Click on "Add Extension", and the React Developer Tools has been added to Chrome so I can use that by clicking on that icon.

[00:47] When I come back to my app, I'll go ahead and refresh here. **We'll see that Chrome extension icon for React Dev Tools has turned red. That's indicating to us that React is on this page, and we're using the development build of React**. When we use the production build of React, then this will look a little differently.

[01:06] We can see that by going to [twitter.com](twitter.com) because Twitter is built using React. Here, "This page is using the production build of React." If we close this, and now I open up my dev tools, I can go inspect here or I can go here to "More Tools" and "Developer Tools".

[01:26] That'll open up the Developer Tools here. **If I click here and look at "Components", then this is going to show me the components that are driving this page**. I can see that we have an `App` component, and we have a `PokemonInfo` component.

[01:42] Under the `App` component, I can see all of the hooks that are being used for this `App` component to work. We have some state here that is an empty string. As I type in here and click submit, now we see that state is what I typed. We can even change that value here. We can click "undo" if we want to undo that change, or we can hit enter to commit that value.

[02:05] Now that value has been committed, and you will see that we get "null" is printed because there is no Pokémon by the name of value. If we change this to "mew" and hit enter, then we're going to retrieve Mew's information. On the Pokémon info side, we can see the Pokémon name is coming in via props. We can change this prop to "mewtwo", change that prop there.

[02:28] Then we can also see the hooks that are being used by the Pokémon info component. We have state is resolved. We also have some more state for the Pokémon information itself, some more state, and this is for our error state. We can see this hook also has an effect.

[02:45] There are a lot of options that we have here. We can click on this _(<> icon)_, and that will show me the source code for this particular component. If we go back to our components here and click on this button _(bug icon)_, then that will log this component's information to the console.

[02:59] We can click that to expand it. We'll see the props, the hooks, and the root DOM node that's being rendered by this component. We'll come back here again. If you want to, you can drag this over. Since we're going to be using this a lot, I'm going to move that over there.

[03:14] We also have this button _(eye icon)_ to inspect the matching DOM element. That'll take us straight to our "Elements" tab here with that element highlighted. Then this _(stopwatch icon)_ is for Suspense. If you're using any React Suspense components, you can test those out here by suspending the selected component. We also have some options here for the theme and the way that this is displayed.

[03:37] We also have some view options for our "Components" tab where we can filter out different types of components or ever filter them out by their name. If we wanted to get rid of that app, then we could type that in. Now our app is no longer being shown. I do want to show that, so I'll remove that, and now our app is being shown.

[03:59] We also have a "Profiler" tab that will help us debug performance issues in our app. We can enable record while each component is rendering. We can also hide commits below a certain threshold. If a commit is really fast, maybe it only takes five milliseconds to update the DOM, then we can enable that.

[04:16] Now I can go to my profiler. I'll drag that over here as well. We can hit this button _(green circle icon)_ to start profiling where we are, or we can hit this button _(reload icon)_ to reload and start profiling when the page loads. I'll click on that. Then we can click stop, and we'll see that there's no data matching the current filter criteria.

[04:35] Let's go ahead and we'll uncheck this button. Now we can see that there was a commit, and it just happened really fast. That commit was for the initial render of our components. We have our app and our Pokémon info. It says that this was rendered because it's the first time that it was rendered. Now let's go ahead and do another recording.

[04:53] We'll say "mew", we'll submit that, and then hit stop. We'll see a couple of commits here. For this one, it rerendered because hooks changed and props changed. For this one, it rerendered because hooks changed. On this commit, it rerendered because hooks changed again so the data came back from the Pokémon request. This rerendered because we updated our state.

[05:19] **The React Dev Tools gives us this "Components" and this "Profiler" tab in our Developer Tools to give us a lot of insight into what our components are doing while we're developing them**. Then we also have this indicator up here to tell us when we're using the development build of React versus the production build so we can make sure that our production application uses the production build.

[05:40] One last pro-tip I can to show is if you open up the console while you have the "Components" tab open, and you can do that by hitting the `esc` key, then we have our console here. **You can type "\$r", and that will give you the hooks, the props, and the type for the component that you have currently selected**. We can click on the app.

[06:00] We can do "$r" again, and now we'll see the hooks, the props, and the type for that component that we have selected. This is very similar to the Elements tab where you can do a $0and that will give you the DOM node that you have currently selected in the Elements tab. In the Components tab, you have a "\$r", and that will give you information on the component that you have currently selected.

[06:22] I strongly advise that you install the React DevTools and play around with them so you become familiar with their capabilities and make you more productive developing React applications.