Kent: 0:00 Let's start by making our form. Here we'll have our div and then we'll make a form. Then we'll have a label with the name and our input and we'll save that. We get that form.

0:15 Then we'll have this message, "Please type your name." Then we want to associate this label to this input so that it's properly associated and you can tell that by clicking on the label. If it doesn't focus the input, then it's not properly labeled.

0:30 Let's add an ID to our input of name and an htmlFor of name. htmlFor is like the for attribute in HTML but in JSX you need to use htmlFor. That's one of the few differences between JSX and regular HTML. Let's save that, come back here. When I click on name, it now focuses the input. Great.

0:53 Now, we're going to have some state here for the name. If there is a name, then we'll have it say strong, "Hello, Name!" Otherwise we'll have it say, "Please type your name." Great. Then if we say Kent, save that. It will say, "Hello, Kent!" By default it doesn't say anything.

1:15 Now, let's wire up an onchange handler for this input. We'll call that handle change. I'll bring that up here and say handle change is an arrow function that accepts an event and it needs to update the state of the name.

1:30 We need to have some way to update that and we can't simply say name = event.target.value. That's not going to work because that won't trigger a re-render and, even if it were to trigger a re-render, this whole function would be recalled. Then the name that we would be setting would get garbage collected and we'd create a new name variable.

1:52 Instead, React has what's called a React hook for maintaining state for a component. We're going to use that with react.useState. We'll pass it the default value of an empty string and react.useState returns an array, we'll call this our state array, and we'll get our name from state array at index zero and our set name from state array at index one.

2:21 Here instead of trying to reassign that variable, we'll call set name with the event target value. If we save that, now everything is working, but nobody wants to write their code like this every time they want to use state, so we're going to destructure this to be name and set name. Then we can delete those lines and save that and it's still working.

2:48 Woo! React rocks! In review, to use state in a React function component, you use the useState hook from React. The useState hook accepts the initial value, so when this greeting component is initially rendered, that is going to be the value of our name variable.

3:07 Any time we call this second element of the array, our updater function will trigger a re-render of this entire function component. When React useState is called again, it will ignore the initial value and instead give us the current value of that name.

3:23 Because React keeps track of the order in which these are called, we could add a second one. Here we'll call this name two and set name two. We'll make another handle change to handle change two. We'll have that call set name two. Then we'll just duplicate all this stuff, put that inside of another div here, and then we'll reference name two and name two. This will be handle change two.

3:49 Now we can say Kent 1 and Kent 2. Those states are managed independently of one another because React keeps track of the order in which these are going to be called, allowing you to use state as much as you need for your component.

4:05 The values in here can be anything. You can make it a Boolean or you can make it a number or you can make it an object or an array -- whatever makes sense for the state that you're trying to manage.

