Instructor: 00:00 Here we have a React element that is a React fragment. As its children, we have a `CharacterCount` with the text of "hello world," and a character count with the text of "" _(empty string)_ Ultimately, we want this to render the text "hello world has 11 characters," and the text "empty string has no characters."

```js
function CharacterCount({ text }) {
  return null
}

const element = (
  <>
    <CharacterCount text="Hello World" />
    <CharacterCount text="" />
  </>
)
```

00:18 Let's go ahead and implement this. We already have our function component here. We're accepting the `text` prop and destructuring it right there. What we're going to do is I want to wrap this in a div, and then we want to say "The text "text" has text.length characters" That's for this first case.

00:39 We need to interpolate some stuff here. For this text right here, I think it would be easiest to interpolate all of this as a template literal, so we'll do that. We'll put this inside of curly braces inside of that template literal. Then we'll put an extra space after this.

00:56 For the text length, we can interpolate that right there. We'll save this, and here we have the text "hello world has 11 characters." Let's go ahead and put this text length in a strong element so that it's bold. We'll say strong, `text.length`, strong, save that. Now it's bolded.

```js
function CharacterCount({ text }) {
  return (
    <div>
      {`The text "${text}" has `} <strong>{text.length}</strong> characters
    </div>
  )
}
```

01:16 We don't want to say `text.length` for zero characters. We want it to say no characters. Let's go ahead and say our length is equal to, if there is a `text.length`, then we'll say the `text.length`. Otherwise, we'll say no. Then we can interpolate that directly into here. Now we're getting 11 characters and no characters.

```js
function CharacterCount({ text }) {
  const length = text.length ? text.length : 'No'

  return (
    <div>
      {`The text "${text}" has `} <strong>{length}</strong> characters
    </div>
  )
}
```

01:38 Thanks to this curly braces allowing interpolation, we could take this whole expression, paste it directly into there, and get rid of that variable altogether. That will work just as well. Now let's say we don't want "no" to be inside of this strong. We only want this strong to be wrapped around an actual number. That will require us to change things up a little bit.

```js
function CharacterCount({ text }) {
  return (
    <div>
      {`The text "${text}" has `}{' '}
      <strong>{text.length ? text.length : 'No'}</strong> characters
    </div>
  )
}
```

01:58 I'll add that right here. We'll say `text.length`, so if there is a length, then we want that in a strong with `text.length`. Otherwise, we'll just use the string "No". With that, let's go ahead and interpolate this character string as a string so we can have an explicit space right there. Then we'll save this. Now we get "11" as bolded, and "No" as not bolded.

```js
function CharacterCount({ text }) {
  return (
    <div>
      {`The text "${text}" has `}
      {text.length ? <strong>{text.length}</strong> : 'No'}
      {' characters'}
    </div>
  )
}
```

02:23 It looks like Prettier added this extra space here. It's not really necessary, so I'll go ahead and remove that, and we'll get the same result here.

02:30 The reason that I wanted to show you this was to demonstrate what it's like to go between JSX land and JavaScript land and back again. Let's follow the flow of this syntax. When we start in the function, this is JavaScript land. We can do JavaScript functions, if statements, all of that stuff. Then when we do the return and this open parentheses, that's still JavaScript land.

02:49 We enter JSX land by doing this open-angle bracket with the tag. Now we're in JSX land, and we have prop syntax, we have children syntax, and then we have our closing tag, and we enter JavaScript land again. Inside of the opening and closing tag, we're in JSX land. Once we do an opening curly brace, we're now in JavaScript land again.

03:12 **This is a limited JavaScript land because we can only do expressions. We're not allowed to do things like for loops, if statements, or any of that. It has to be an expression of JavaScript that evaluates to some value**. A string like this or a ternary like this, those are expressions, and those are allowed within the curly braces.

03:34 If you stop to consider what this JSX is compiled to, this should all make sense. Let's take a look at the JavaScript code that Babel generates for us. Here we have our character counter. We're calling `React.createElement`. We pass the div that's representing this div right here. It's not taking any props, so we get `null`. Then as the third argument, we get this string.

03:55 Let's go ahead and write this over here. We have `React.createElement` div, null for the props, and then the string, "The text...", and so on. If we wanted to, instead of passing this string, do some sort of if statement, that wouldn't work at all. It doesn't make any sense to pass a statement as an argument to a function.

```js
// This does not work!
React.createElement("div", null, if (statement) {})
```

04:16 **For the same reason, you can't use statements inside of the curly braces of JSX because the curly braces basically are saying, "Hey, Babel, as you get to this part, I want you to take everything between these two curly braces and then just stick it in this argument of the createElement call."** Having a good understanding of how Babel is compiling your JSX will help you use JSX more effectively.

04:37 Once we enter that closing curly brace, we're back in JSX land. Then we come back to this curly brace, and we're now in JavaScript land again. We have this expression. While we're in the middle of this JavaScript land with the ternary expression, we actually enter JSX land again, so we're a couple layers into this.

04:55 We start out in JavaScript. Then we go into JSX, then we go into JavaScript, then we go into JSX again, and then we go into JavaScript. We exit JavaScript, we enter JSX, we exit JSX, and we're back into JavaScript. Then we exit JavaScript and go into JSX. This back and forth between JavaScript and JSX is really common.

05:20 This type of interpolation is not unique to just JSX. In fact, we have a couple other examples of interpolation right here. This file is an HTML file, and we start with HTML all right here. We're still going through HTML until we hit the end of this opening script tag. Now we're in JavaScript land. We also have style tags, and now we're in CSS land. **HTML has always had this type of interpolation**.

05:46 JavaScript also has built-in interpolation when we're talking about template literals. Right here, we have this template literal. We're in string land. Then when we do the `${}`, now we're in JavaScript land. In fact, this has the same limitations that JSX has for its interpolations.

06:04 **Specifically, you can't use if statements, for loops, or switch statements in here. Whatever you put in here has to be an expression**. That expression can be complex. We can put ternaries in there. We could even put an immediately invoked function expression, though I don't advise that.

06:20 Having a strong understanding of JSX and interpolating JavaScript expressions is key to using JSX well. It's something that you'll get better with over time as you're using JSX.
