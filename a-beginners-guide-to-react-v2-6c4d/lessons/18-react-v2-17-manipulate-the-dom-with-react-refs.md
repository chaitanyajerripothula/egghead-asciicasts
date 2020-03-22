Instructor: 00:00 We have a function component called `Tilt`. 

```jsx
function Tilt({children}) {
  return (
    <div className="tilt-root">
      <div className="tilt-child">{children}</div>
    </div>
  )
}
```

Thanks to some handy classNames and some handier CSS, we have it styled in this fancy-looking way. We can make it do something fancy by using a library that I've included called [vanilla-tilt](https://www.npmjs.com/package/vanilla-tilt). Vanilla-tilt takes a dom node and makes it react when the users mouses over that dom node.

00:19 The dom node we want to give it is the dom node that's created for this element, the `tilt-root`. Remember, this is a React element, not a dom node. React takes that React element and renders it to the dom, so we need React to give us the dom node that it creates for this particular React element so we can wire-up vanilla-tilt to it.

00:39 To do this, we're going to use a `ref` prop. We need to pass a ref, which is an object that has a mutable current property. Let's go ahead and use the `react.useRef` hook. From that, we'll get our `tiltRef`. Then we can copy that, paste it here to our ref.

```jsx
function Tilt({children}) {
  const tiltRef = React.useRef()
  return (
    <div ref={tiltRef} className="tilt-root">
      <div className="tilt-child">{children}</div>
    </div>
  )
}
```

00:58 Then the `tiltRef` is an object that has a current property, and that current property is the current value for this ref object. In our case, because we're passing this ref to a div with the ref prop, that current property will be the dom node that React creates for this div.

01:13 If we `console.log(tiltRef.current)`, and we save that, then we should be seeing the dom node, but we actually see `undefined`. The reason we see that is because, at the time that this function runs, React has not created the dom node for this div, and so the `tiltRef.current` is currently undefined. In fact, you can initialize that current value by passing an argument to the useRef hook, `here`.

```jsx
function Tilt({children}) {
  const tiltRef = React.useRef('here')
  console.log(tiltRef.current)
  return (
    <div ref={tiltRef} className="tilt-root">
      <div className="tilt-child">{children}</div>
    </div>
  )
}
```

01:40 How do we get the dom node so we can initialize vanilla-tilt on it? We need to have some code that runs after React has updated the dom and set our `tiltRef.current` property. Interacting with the dom is a side effect, so the logical place for this would be in a `React.useEffect` hook. If we move that console.log in here and we save that, we'll get a refresh, and now we are getting the dom node.

```jsx
function Tilt({children}) {
  const tiltRef = React.useRef()
  React.useEffect(() => {
    console.log(tiltRef.current)
  })
  return (
    <div ref={tiltRef} className="tilt-root">
      <div className="tilt-child">{children}</div>
    </div>
  )
}
```

02:02 From here, we can get our `tiltNode` from `tiltRef.current`. Then let's go ahead and make some `vanillaTiltOptions` here so we can specify how we want vanilla-tilt to treat this dom node. We'll say `max: 25`, `speed: 400`, `glare: true`, and `max-glare: 0.5`. With that now, we can say `VanillaTilt.init` on that `tiltNode`, and with the `vanillaTiltOptions`.

```jsx
React.useEffect(() => {
  const tiltNode = tiltRef.current
  const vanillatiltOptions = {
    max: 25,
    speed: 400,
    glare: true,
    'max-glare': 0.5
  }
  VanillaTilt.init(tiltNode, vanillaTiltOptions)
})
```

02:37 If we save that, we'll get a refresh. This code runs after React has rendered to the dom and updated the Tilt ref.current property so that we can get access to the Tilt node and initialize vanilla-tilt. Now, if we hover over our block in our browser, we get that really cool effect.

02:52 If we unclick the show tilt checkbox, that will unmount the Tilt component from the page, removing the dom node from the page. However, there's still a bunch of event handlers on that dom node and several references to that dom node from within the vanilla-tilt library.

03:07 That means that the dom node itself may not exist on the page, but it does still exist in memory because there are references to it in vanilla-tilt. Unfortunately, this could lead to a memory leak. If we keep on mounting and unmounting this over and over and over again, then we're going to have a bunch of dom nodes sitting around in memory that aren't needed by the user anymore.

03:28 To combat this problem, we can `return` a function which will be called for every update of our Tilt component. In this function, we can say `tiltNode.vanillaTilt`, which is a property that vanilla-tilt is adding to our dom node, `.destroy`.

```jsx
React.useEffect(() => {
  const tiltNode = tiltRef.current
  const vanillatiltOptions = {
    max: 25,
    speed: 400,
    glare: true,
    'max-glare': 0.5
  }
  VanillaTilt.init(tiltNode, vanillaTiltOptions)
  return () => {
    tiltNode.vanillaTilt.destroy()
  }
})
```

03:48 This will remove all references of our dom node from vanilla-tilt and remove all event handlers so that we can avoid memory leaks with our Tilt component. What you can't see is that that dom node has been garbage-collected properly so we don't need to worry about memory leaks anymore.

04:04 Another thing that we should do here is recognize that useEffect is going to be called on every re-render of our Tilt component. That's not going to lead to any bugs, but it is suboptimal. What that means is that we'll call a destroy with vanilla-tilt, and then we'll call init with vanilla-tilt between every render of our component.

04:21 We don't need to do that because none of the things in this function can change with re-renders of our Tilt component. We're going to add a dependency array with all the dependencies of our function. Because none of the variables in our useEffect callback can change, we don't need to list any dependencies here.

```jsx
React.useEffect(() => {
  const tiltNode = tiltRef.current
  const vanillatiltOptions = {
    max: 25,
    speed: 400,
    glare: true,
    'max-glare': 0.5
  }
  VanillaTilt.init(tiltNode, vanillaTiltOptions)
  return () => {
    tiltNode.vanillaTilt.destroy()
  }
}, [])
```

04:37 What this is effectively saying is I want to run this when the component is initially mounted to the page, and then I want to run `tiltNode.vanillaTilt.destroy()` when the component is unmounted from the page. That's another useful optimization we can make for this case.

04:49 In review, the thing that we wanted to do was make this dom node do some fancy stuff. We included vanilla-tilt on the page so that we could do that. We also included some CSS from the vanilla-tilt author to make our dom nodes look really fancy.

05:05 To get access to the dom node so we can initialize vanilla-tilt on it, we're using this ref prop on the div. We passed the thing that we get back from a React useRef call. That ref has a current property that we can use to access the current value of this object.

05:19 The reason that useRef is an object that has a current property is so that we can mutate the current property to be whatever we want without triggering a re-render of our component. We can use refs for more than just dom nodes like we're using here. We can use it for any value that we want to keep track of and mutate over time, without triggering a re-render of our component.

05:39 After our component has been mounted, our useEffect callback is going to be called, so we get the Tilt node from our Tilt ref.current property. We create some vanilla-tilt options and pass those to the initialization for our Tilt node with vanilla-tilt.

05:54 Then we return a clean-up function so that we can remove all references of our dom node in vanilla-tilt and remove all the event listeners that vanilla-tilt put on our dom node.