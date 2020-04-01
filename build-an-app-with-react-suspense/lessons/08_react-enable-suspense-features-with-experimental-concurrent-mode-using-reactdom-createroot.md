Instructor: [00:00] If you've been developing React apps for a long time, you are familiar with this old, faithful ReactDOM.render function. It's quite simple. It just takes a component and renders it to a root element somewhere. Most often, it's document.getElementById and some id. To take advantage of experimental concurrent mode, you have to change this.

[00:26] React's experimental build actually has three different modes that refer to as legacy mode, which is the mode we use today, blocking mode, which is an intermediate between these two major modes, and concurrent mode.

[00:41] Concurrent mode is what's required for pretty much everything that we are going to cover in this course. At this point, we have to have it enabled to actually see these features working.

[00:53] How do we do it? Well, using an experimental build of ReactDOM, we can change this to createRoot. Now, createRoot has a slightly different signature. We don't pass in both of these at once. We create a root from the root element and then to it we render our app.

[01:15] If I did everything correctly, our application should still render and work as it did before.

```js
ReactDOM.createRoot(rootElement).render(<App />);
```
