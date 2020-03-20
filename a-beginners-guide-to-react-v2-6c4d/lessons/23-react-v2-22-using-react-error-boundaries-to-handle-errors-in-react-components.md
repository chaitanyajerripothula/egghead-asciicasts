Kent C. Dodds: 0:00 As hard as you work on your application, sometimes unexpected things will happen. I don't expect people will be throwing errors in the body of your function, but they could be calling functions that don't actually exist, which will result in an error. Let's take a look at how you can manage these with React.

0:16 Here we have some explode state where we initially that to false. Then we have a button where when you click on it, it changes that explode state to true. Then we all surrender this div where if there is some explode state, then we'll render that bomb, which will trigger this error.

0:33 If we take a look at what things look like right now, we click on the bomb. Our entire application goes away. We get this uncaught error, kaboom, in our console. If we scroll down here a little bit, React actually logs out a component stack trace so we can track down which component threw this error.

0:52 We have our app. That is rendered right here. Then we have this div, which is rendered here. Then we have this div, which is rendered here. Then we have this bomb, which is rendered here. That bomb is the thing that threw the error. Then it's giving us this tip. Consider adding an error boundary to your tree to customize error-handling behavior. That's just what we're going to do.

1:14 Normally, I use a third-party library for this, but we're going to build our own little error boundary. Error boundaries have to be class components. To create a class component with React, we're going to say, "class ErrorBoundary extends React.Component."

1:30 In the body of our React class component, we're going to need a render method. This is what's going to be rendered. It's basically the same thing as the body of our regular function components.

1:42 Here we're actually not going to render anything special with this error boundary. We're simply going to return this.props.children. The React elements we return for this error boundary are going to be the same React elements that are provided to this error boundary as children.

1:57 Let's go ahead and put those right here, inside of this div. We'll say, "ErrorBoundary," paste in what we had before, and then close off that error boundary. If we save this, we get a refresh. When I click on this bomb, we get the exact same behavior that we had before. Now let's make this error boundary handle that error.

2:17 The first thing that we're going to need is some state. We'll say error is null. Then we'll have a static method called getDerivedStateFromError that'll accept an error. Then it'll return the state change that we want to make based on this error. We'll just return an object that has an error property. That's going to be assigned to the error that we're getting for this static method.

2:40 When this happens, we're going to get a re-render. Let's grab that error from our state. Then we can say if there's an error, then we'll return a div that says, "Oh, no." Let's save that. When we click this, we're going to see, "Oh, no."

3:00 We'll still see errors logged to the console for our benefit, but the application won't completely crash. React will simply re-render this error boundary with the error that was thrown. The error boundary gets to control what's being rendered.

3:13 I'm going to go ahead and make a function component here called errorFallback, which is going to be my generic fallback component for this application. We'll make this except a prop called error. Then we'll return a div that has a p tag with something went wrong and then a pre tag with the error message.

3:40 As the user of this error boundary component, I want to be able to provide to the error boundary the fallback component I want it to render when there's an error. I'm going to provide it the prop fallback component. Here I'll provide that component that I just created, this error fallback. Paste that right in there.

4:01 For our error boundary to accept and render that, we're going to have access to that fallback component on this.props. We can say, "this.props.FallbackComponent." We'll provide the prop error as error.

4:18 You may recall that rendering custom components requires that the first character you use is capitalized, but the compiler also has a rule that if there are dots included in the component name, then those will also be treated as custom components, which is why this works.

4:34 We save this. We push the button. We're going to get "Something went wrong. Kaboom." The app is still working. We get all of the information logged to the console as well.

4:45 As I said, I never use class components, even for error boundaries, because I prefer to use a third-party library, which I'm going to include now. That is called React Error Boundary. With the React Error Boundary UMD export, it exposes a global variable called reactErrorBoundary. It has a property on there called errorBoundary. I'm going to simply assign that to errorBoundary.

5:11 You'll be pleased to know that I don't have to change any of my code for this to work. It'll continue to work because we basically built a simple version of the error boundary from this library. The library is doing a fair bit more than what we're doing here, which is why I recommend you do that instead, but the API is exactly the same.

5:30 One other thing I want to demonstrate here is that this error boundary can be rendered anywhere in the tree, but the location of the error boundary has a special significance.

5:38 The error boundary can handle any errors that are thrown by its descendants, but it's also important to note that the error boundary is going to render something in place of all of its descendants when there is an error.

5:50 That means that while we can see the bomb still right here and the rest of our application, if we were to move this error boundary up to encompass our entire app and save that, when we click this button, then the entire app is replaced by our error fallback component.

6:08 This may or may not be desirable based on the specific use case, but it could be useful to have one error boundary rendered up here and then other error boundaries rendered throughout your application with more specific error fallbacks.

6:22 Here we could have "Something went wrong there." That error is going to be handled by the nearest error boundary here. If something went wrong somewhere else in our application, then this top-level error boundary could handle that error. This gives us some fine-grained control over what part of the tree is going to be replaced by what's rendered by our error boundary.

6:43 Another important thing to note is that our error boundary can only handle certain errors, specifically errors that are happening within the React call stack.

6:51 It won't handle errors that are happening in event handlers or if there's an error in an asynchronous callback, like a promise handler. It will only handle errors that happen within a React call stack, like the render method or a React useEffect callback.

7:06 In review, to handle React errors, you need to create an error boundary, or, as I recommend, use React Error Boundary from npm. With this error boundary, you can wrap parts of your code. Any descendants of that error boundary will have its errors handled by the error boundary.

7:23 With the error boundary from the React Error Boundary library, you can provide a fallback component. That fallback component will be rendered in the event of an error allowing you to recover from the error or simply display an error message for the user to read.

