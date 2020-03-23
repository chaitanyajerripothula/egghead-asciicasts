Kent: 00:00 When you start creating custom function components, you're going to have people using those function components and they may not use it quite right. For example, here, we have this, "Say Hello!" that's accepting a props object we're destructuring right here.

00:13 We're getting a `firstName` and a `lastName` prop and expecting to be able to render those out, but the way that it's being used is we have that first name prop being assigned to `false` and we're not providing the last name prop at all. The end result is not exactly what we're looking for.

```js
function SayHello({ firstName, lastName }) {
  return (
    <div>
      Hello {firstName} {lastName}!
    </div>
  )
}

const element = <SayHello firstName={false} />
```

00:28 The React team has developed a package called PropTypes that allows you to add run time validation of the props that are passed to your components. **This is why React supports a feature called prop-types that allows you to validate the types of props that are being passed to your components when they're rendered**.

00:45 To add support for that, we're going to have `SayHello.propTypes` equals this object. We'll have a key for the prop that we want to validate. That's going to be a function that takes the prop name and the component name. Any time our `SayHello` component is rendered, we can check that the first name prop was passed properly.

01:07 We can say, "If type of props at prop name is not equal to a string, then we can return a new error that says, `"Hey, the ${componentName} needs the prop ${propName} to be a string, but you passed a ${typeof props[propName]}."`

```js
SayHello.propTypes = {
  firstName(props, propName, componentName) {
    if (typeof props[propName] !== 'string') {
      return new Error("Hey, the ${componentName} needs the prop ${propName} to be a string, but you passed a ${typeof props[propName]}.")
    }
  }
}
```

01:35 If we save that, then we can see that warning shows up. Failed prop-type. "Hey, the component SayHello needs the prop first name to be a string, but you passed a boolean."

01:46 We could do the same thing for our last name prop. What I'm going to do is pull this out. We'll create a last name prop here and I'm going to make a prop-types object here where we have a string that is that validator that we just created. Then here, we can say first name is PropTypes.string and last name is PropTypes.string.

```js
const PropTypes = {
  string(props, propName, componentName) {
    if (typeof props[propName] !== 'string') {
      return new Error("Hey, the ${componentName} needs the prop ${propName} to be a string, but you passed a ${typeof props[propName]}.")
    }
  }
}

SayHello.propTypes = {
  firstName: PropTypes.string,
  lastName: PropTypes.string
}
```

02:08 We could add a bunch more validators to the prop-types, for numbers and whatever else you could imagine.

02:14 With that, we get a warning for both of these. This is so common that the React team created a package on npm that we can use and I'm going to pull it in from unpackage.

02:24 I'll paste that right in here and it's called `prop-types`. With that, it actually creates a global variable here for us called `prop-types` when we're using it as a script like that. It has the same API that we just created, what foresight.

```js
SayHello.propTypes = {
  firstName: PropTypes.string,
  lastName: PropTypes.string
}

const element = <SayHello firstName={false} />
```

02:37 We'll save that. We'll get a refresh and things are working a little differently. Before, we were getting a warning for both the first name and the last name, but now we're just getting one for the first name.

02:48 **The reason for that is that prop-types by default are not required. Because the last name wasn't provided, we're not getting the last name validated in this case**. That works out nicely if you provide a default for the last name, like if we were to say "Unknown" here (as `lastName` prop value).

03:04 **But if you want to make sure that it's required, then you can add a `.isRequired` on both of these**. Save that and now you'll get a warning saying that the first name is the wrong type and the last name is required but wasn't provided.

```js
SayHello.propTypes = {
  firstName: PropTypes.string.isRequired,
  lastName: PropTypes.string.isRequired,
}
```

03:18 One reasonable concern you might have about prop-types is that it adds a fair amount of code that needs to be run whenever React is rendering your components which may impact performance.

03:28 Let's take a look at the production.minified version of React and we'll notice that everything is working exactly as it was before but we're no longer getting those warnings. **That's because in production prop-types are not applied**.

03:44 If you want to take things a step further, then you can actually get a Babel plugin that will remove the prop-types from your source code for production. That plugin is called `babel-plugin-transform-react-remove-prop-types`. You could install that and use that in your production build, or many tool kits install and use this by default.

04:02 If you want to learn more about the prop-types that are available, you can go to the [prop-types page on npm](https://www.npmjs.com/package/prop-types). There are a lot of different types that you can use for your components to validate that people are using your components properly.
