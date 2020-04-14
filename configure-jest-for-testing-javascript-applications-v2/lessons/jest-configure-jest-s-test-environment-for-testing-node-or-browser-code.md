Instructor: [00:00] Jest does a lot of awesome things by default and one of those awesome things is simulate a browser environment in node using a module called JS DOM. If I run `console.log(window)` and then run `npm t`, I'm going to get that giant window object logged to the console.

[00:18] We're actually running a node. There is actually no window or document or anything like that, technically, but that window is being simulated by JS DOM. However, our utils that we're testing here can actually run in node or in the browser because it's not using anything that actually relies on the browser.

[00:35] We can actually run this test in a node environment and save ourselves a little time in the process because there's a little performance hit to set up JS DOM. If I run `npm t` and then forward along env = node, then that will run our test in the node environment, which excludes JS DOM and now we're getting a reference where window is not defined.

```bash
npm t -- --env=node
```

[00:57] If I wanted to configure this, so I don't have to pass that flag every time, then I would add a jestconfig.js file here. Here I'm going to module exports. An object that has a test environment and that's jest-environment node.

```js
module.exports = {
  testEnvironment: 'jest-environment-node'
}
```

[01:12] Now, if I run `npm t`, that configuration will be read automatically, and jest doesn't bother setting up jsdom for my test. If I want to be explicit about including jsdom, then I can replace node with jsdom, then, I can run `npm t` and I'm going to get that giant window object logged to the console.

```js
module.exports = {
  testEnvironment: 'jest-environment-jsdom'
}
```

[01:30] I'm actually going to leave it this way. I like to be explicit. The reason I'm going to use jsdom even though the utils doesn't need window is because most of this project actually does rely on browser APIs. That's where I'm going to be running this code anyway.

[01:44] I want to make sure that my tests are as close to reality as possible by simulating a global browser environment. Let's get rid of that `console.log(window)` because that's super annoying. We'll run our `npm t` again just to make sure that everything's working. It is.

[01:58] In review, what we had to do here is I created a jest-config file at the root of my project right by the package json here. Then, I module-exported the configuration object, which has a test environment property pointed to jest-environment-jsdom.

[02:12] Now, this actually is a node module, so if I go search node modules, then I'll find jest-environment-jsdom right here under my known modules. That's installed by default for me by jest. There's also a jest-environment node here.

[02:25] There are actually other third-party environments that you can install from npm, as well. We're going to use the built-in jest-environment-jsdom. That ensures that all of our tests are running in a browser-simulated environment thanks to jsdom.
