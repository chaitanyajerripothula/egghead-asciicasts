**THE VIDEO IS CRITICAL FOR THIS LESSON.**

Instructor: 00:00 Once you've figured out how React works locally in an index.html, you probably want to build an actual application and the best next place to start is by going to [codesandbox.io](https://codesandbox.io/). CodeSandbox is amazing.

00:14 It's an entire IDE and prototyping tool for rapid web development built right into the browser. It's just like a typical development environment that you would expect from a local development.

00:26 Let's go ahead and create a sandbox. I'm going to create a React sandbox here from the React template. Here on the right, we have our browser. On the left, we have our sandbox. I can say, "Hello, you!" and it just gets updated automatically as we go.

### index.js
```js
export default function App() {
  return (
    <div className="App">
      <h1>Hello You!</h1>
      <h2>Start editing to see some magic happen!</h2>
    </div>
  )
}
```

00:44 There are a ton of things that you can do in CodeSandbox. You can add dependencies from npm, you can add external resources, you can even change your typeface. If we hit command and a comma, `cmd + ,`, you can open up your settings here and change the settings just like you would if you were using VS Code locally. It's really amazing.

01:05 You can build your whole app out here, you can have multiple files and use import statements and modules. You can even write tests in here. We can create a new directory called `_tests_` and then, in that directory, you can have a `test.js` file. Then, we click on the test tab in the top right of the browser and we get to run all the tests that we have in here. We can say test this works and then our tests will be run.

### test.js
```js
test('this works', () => {})
```

01:33 Another thing you can do with this is you can connect to this with GitHub. I'm going to go ahead and sign in with GitHub right here. You can find the github tab on the left. I'll authorize CodeSandbox and then we'll sign in to authorize CodeSandbox to create repos.

01:48 Then, I can create a new repository right in the CodeSandbox editor. Example, CodeSandbox. We'll click Create Repository and CodeSandbox will create a repository on GitHub based on what I have right here.

02:03 With that now, I can go to the information here and I see that there's a link. I can click on that and that opens up in this new GitHub repo that has all of the code that I had for that CodeSandbox. To take it a step even further, I can go to this deployment option and I can deploy this with Now or Netlify.

02:24 Let's go ahead and try deploying this with Netlify. The tab is the bottom one on the left of the CodeSandbox editor. We'll click deploy and then I get a sandbox site and now Netlify is building my CodeSandbox. I can look at the logs here and it's going to take a minute or two to complete, so I'll speed that up for you so you don't have to wait.

02:45 Now, it looks like it's been deployed. I can scroll through all these logs and take a look at what it's done here. Then, I can click visit and that will pop open the site in another tab.

02:57 This is available on the World Wide Web, so you can send this URL to anyone and they can see the amazing work that you created within CodeSandbox.

03:05 You can also claim this site so you can make it associated to your Netlify account and even have it wired up to the GitHub repo that you have, so anytime you push changes to this GitHub repo, you can get your site redeployed.

03:18 Within Netlify, you can even make a custom domain and so you could create an entire application using just CodeSandbox, Netlify, and GitHub.

03:28 As you make changes, for example, if we edit our index.js file to say, "Hello, there!" then CodeSandbox will automatically fork your sandbox and you can commit changes to the GitHub repo that this CodeSandbox is attached to.

03:43 We could say change title. We could commit it directly or even open a PR. Let's go ahead and commit. It'll create the commit and put it in our repo here. Now, we see change title, what's the latest commit.

03:59 If we wire up Netlify to our GitHub repo, then Netlify will automatically deploy that change as well. You can work entirely in CodeSandbox, or if you'd rather work locally, then you can also export this to a zip, unzip that, and work through it locally or you can clone this repository and work through it locally as well.

04:19 There's an entire world of things that you can do with React once you have these tools at your fingertips. I look forward to seeing the cool things that you build to make the world a better place with your newfound knowledge of React.