Instructor: [00:00] We have a bunch of CSS in our HTML document, and we want to style this div that contains our text with that same CSS. To apply that CSS, we're going to add a `className` of `box` to get that box around this small, light-blue box. Because we want it to be small, let's also add a `box--small`, and we'll save that.

```html
<div>
  <div className="box box--small">small lightblue box</div>
</div>
```

[00:26] Then, I want it to be light blue, but we don't have any CSS for light blue. We could add it, but I want to show you the `style` prop which accepts an object rather than a string of styles. We're going to pass this object, and it will be the same property names that you get if you use 'getComputedStyle' for this element, that is, CSS properties that are camel-cased instead of kebab-cased.

[00:51] For us to get a background color, we're going to say `backgroundColor`, and then we want `lightblue`. 

```html
<div>
  <div className="box box--small" style={{backgroundColor: 'lightblue'}}>small lightblue box</div>
</div>
```

With that, we get a small, light-blue box. Let's also add a `fontStyle` of `italic`.. Now we have a small, light-blue box in italics.

```html
<div>
  <div className="box box--small" style={{fontStyle: 'italic', backgroundColor: 'lightblue'}}>small lightblue box</div>
</div>
```

[01:10] We also have CSS for large and medium boxes, so let's go ahead and create some more of these boxes. We'll make the second one be medium, and using our multiple cursors here, we'll use large for the third one. Instead of light blue for our medium, let's do `pink`. Instead of the light blue of our large, we'll use `orange`. We'll save that, and we have this stack of boxes.

```html
<div>
  <div 
    className="box box--small" 
    style={{fontStyle: 'italic', backgroundColor: 'lightblue'}}>small lightblue box
  </div>
  <div 
    className="box box--medium" 
    style={{fontStyle: 'italic', backgroundColor: 'pink'}}>medium lightblue box
  </div>
  <div 
    className="box box--large" 
    style={{fontStyle: 'italic', backgroundColor: 'orange'}}>large lightblue box
  </div>
</div>
```

[01:35] There's a fair amount of duplication in these, so what I'm going to do is make a new `function` component called `Box`. This is going to take some `props`, and then will return a `div`, and we'll spread all the props. Right now, this Box component basically functions as a div. We could replace all of these `div`'s, with multiple cursors here, with a box. We save that, and we get the exact same result.

```html
<script type="text/babel">
  function Box(props) {
    return (<div {...props} />)
  }
  const element = (
    <div>
      <Box 
        className="box box--small" 
        style={{fontStyle: 'italic', backgroundColor: 'lightblue'}}>small lightblue box
      </Box>
      <Box 
        className="box box--medium" 
        style={{fontStyle: 'italic', backgroundColor: 'pink'}}>medium lightblue box
      </Box>
      <Box 
        className="box box--large" 
        style={{fontStyle: 'italic', backgroundColor: 'orange'}}>large lightblue box
      </Box>
    </div>
  )
</script>
```

[02:02] Now, let's make this box a little bit more useful by taking the repetition from each one of these and putting it within the Box component. First off, we have a className, and then it has a box--, but then the size is different. Let's go ahead and just take the className Box. We'll put `className="box"`, we'll save that.

```html
<script type="text/babel">
  function Box(props) {
    return (<div className="box" {...props} />)
  }
  const element = (
    <div>
      <Box 
        className="box--small" 
        style={{fontStyle: 'italic', backgroundColor: 'lightblue'}}>small lightblue box
      </Box>
      <Box 
        className="box--medium" 
        style={{fontStyle: 'italic', backgroundColor: 'pink'}}>medium lightblue box
      </Box>
      <Box 
        className="box--large" 
        style={{fontStyle: 'italic', backgroundColor: 'orange'}}>large lightblue box
      </Box>
    </div>
  )
</script>
```

[02:26] Once we save that -- and we can get rid of box in each of our `Box`es -- then we'll notice that the Box className is no longer being applied because we're not getting that border. We can verify that by inspecting this, looking our application. We see class Box small, but we don't see the `box` className here. Think for a moment why that might be.

[02:48] The reason might be a little more clear if I take this spread of props, `{...props}`, and put it on the other side of the className. 

```js
function Box(props) {
  return (<div {...props} className="box" />)
}
```

We save that, and now we get the opposite problem where we have the border the className Box provides, but we don't have the small, medium, or large classNames applies.

[03:05] The reason is, because of this spread of props, we're getting an override of the className prop that's being provided to the Box component. What we can do is combine the classNames into a single className that will work for all of our boxes. Instead of just taking the props object as it is, I'm going to destructure it in-place, and we'll take the className out of here.

[03:28] Then I'll put a `...rest` to say that these are the rest of the props. Then we'll remove this, and we'll put the spread at the end. Then we'll manually combine the className that we want to provide for boxes in general with the className that's provided to us through the props.

[03:46] We'll make a template literal here. We'll say `box`, and then we'll put `className`. 

```js
function Box({className, ...rest}) {
  return (<div className={`box ${className}`} {...rest} />)
}
```

We'll save that, and now everything's working as it should be. One problem here is that if I take off the className on one of these boxes, then I'm going to get `class="box undefined"`

[04:03] That's probably not a really big issue, but it's pretty simple to fix by adding a default value for this className to be an empty string. Now we get Box with a space. I'm not as worried about the space. If you were, then you could add a `.trim`, and that would get rid of our space. 

```js
function Box({className = '', ...rest}) {
  return (<div className={`box ${className}`.trim()} {...rest} />)
}
```

I don't think that's a big deal, so we'll get rid of that. Let's restore this small box to its former glory.

```js
function Box({className = '', ...rest}) {
  return (<div className={`box ${className}`} {...rest} />)
}
```

[04:26] The other thing that's common across all these boxes is the font style italic. Let's have that be provided here. We'll say style `fontStyle='italic'`. With that, we can now remove the font style from all of our boxes. 

```html
<script type="text/babel">
  function Box({className = '', ...rest}) {
    return (
      <div 
        className={`box ${className}`} 
        style={{fontStyle: 'italic'}}
        {...rest} 
      />
    )
  }
  const element = (
    <div>
      <Box 
        className="box--small" 
        style={{backgroundColor: 'lightblue'}}>small lightblue box
      </Box>
      <Box 
        className="box--medium" 
        style={{backgroundColor: 'pink'}}>medium lightblue box
      </Box>
      <Box 
        className="box--large" 
        style={{backgroundColor: 'orange'}}>large lightblue box
      </Box>
    </div>
  )
</script>
```

We'll save that, and we've made things a lot more terse for users of this box component.

[04:47] You might notice we no longer have that font style of italic. The reason that's happening is the exact same as with the className. If I move rest above our font styling, now we're going to get the italic, but we won't get the custom styles that the user of our box component is trying to specify.

[05:04] Again, we do need to compose these manually. I'm going to pluck off the `style` prop from our props here, and we'll combine the style prop from those with the style prop that we specify by using object spread syntax. Then I'm going to put rest at the bottom here again.

```js
function Box({className = '', style, ...rest}) {
  return (
    <div 
      className={`box ${className}`} 
      style={{fontStyle: 'italic', ...style}}
      {...rest} 
    />
  )
}
```

[05:23] We'll save that, and we get our italics and we get our special color, which is exactly what we're looking for. Now anyone that wants to have a box can use this component, and it will have the Box className and italics applied automatically for them.

[05:39] One useful feature of creating components like this is we can use it to separate our user code from the styling concerns of our specific component. Rather than a className, it would actually be better to accept a `size` prop. We could say the size is small, medium, or large.

[05:57] Let's make that work. I'll remove the className here. 

```js
<div>
  <Box 
    size="small" 
    style={{backgroundColor: 'lightblue'}}>small lightblue box
  </Box>
  <Box 
    className="box--medium" 
    style={{backgroundColor: 'pink'}}>medium lightblue box
  </Box>
  <Box 
    className="box--large" 
    style={{backgroundColor: 'orange'}}>large lightblue box
  </Box>
</div>
```

We'll save this. Let's scroll up to the top on our browser, and we'll see that our small, light-blue box is no longer small. Now I'm going to take the size prop, and we're going to generate a `sizeClassName`. If the size prop is specified, then we'll say box--size. Otherwise, we'll do an empty string.

[06:24] Then we can put that size className inside of our className list here. 

```js
function Box({className = '', style, size, ...rest}) {
  const sizeClassName = size ? `box--${size}` : ''
  return (
    <div 
      className={`box ${className} ${sizeClassName}`} 
      style={{fontStyle: 'italic', ...style}}
      {...rest} 
    />
  )
}
```

If we save that, then we get a small, light-blue box, and users of our box component no longer need to concern themselves with the className that needs to be applied to create a small box.

[06:41] This Box component can deal with the implementation details of what it takes to make a small box. This means that we could change the classNames that we use. We could use a third-party CSS library, and we could change that CSS library. Any component that's using the box just needs to follow the API that the box creates for it.

[06:59] We can separate our components from the classNames used to style them. That said, we can still apply our own classNames if we want to, and we can apply our styles if we want to because those are going to be combined with whatever is happening in the box. Let's go ahead and update the classNames for both of these because I like the size prop better.

```js
<div>
  <Box 
    size="small" 
    style={{backgroundColor: 'lightblue'}}>small lightblue box
  </Box>
  <Box 
    size="medium" 
    style={{backgroundColor: 'pink'}}>medium lightblue box
  </Box>
  <Box 
    size="large"
    style={{backgroundColor: 'orange'}}>large lightblue box
  </Box>
</div>
```

[07:20] We'll save that, and now we have a small, light-blue box, a medium pink box, and a large orange box with a reusable Box component that merges the className, the style prop, forwards along all of the props, like the children prop that we're passing for each one of these, and accepts a special size prop so that you can size the box appropriately.

[07:43] One thing that I want to note here about the className prop in HTML. We would normally use `class` instead of `className`. className is the dom property that you use to access the class attribute on dom nodes. If we go to the terminal in our browser and say `$0` which will access the element that we have currently selected in our `elements` tab, then we can say `.className`, and that will give us that className list.

```
$0.className
"box box--small"
```

[08:09] In addition, if we were to take all of our props and make a props object in our Box function, we just move all of our props in there, convert it to JavaScript rather than JSX syntax, and then spread those props. That will work just as well. 

```js
function Box({className = '', style, size, ...rest}) {
  const sizeClassName = size ? `box--${size}` : ''
  const props = { 
    className: `box ${className} ${sizeClassName}`,
    style: {fontStyle: 'italic', ...style},
    ...rest,
  }
  return <div {...props} />
}
```

Then let's say that this were `class` instead of `className`.

[08:29] If we wanted to extract this and use shorthand syntax, then we'd make it class variable. This wouldn't work because class is a reserved word. 

```js
function Box({className = '', style, size, ...rest}) {
  const sizeClassName = size ? `box--${size}` : ''
  const class = `box ${className} ${sizeClassName}`
  const props = { 
    class,
    style: {fontStyle: 'italic', ...style},
    ...rest,
  }
  return <div {...props} />
}
```

In addition, if we wanted to leave this here and then destructure the class property from the props, then this also wouldn't work because class is a reserved keyword as well. 

```js
function Box({className = '', style, size, ...rest}) {
  const sizeClassName = size ? `box--${size}` : ''
  const props = { 
    className: `box ${className} ${sizeClassName}`,
    style: {fontStyle: 'italic', ...style},
    ...rest,
  }
  const {class} = props
  return <div {...props} />
}
```

For that reason and others, it is the className prop rather than the class prop.

[08:56] One thing that I really like about this style prop being an object of styles rather than a string of styles is because it's a lot easier to combine objects of styles. With className, we have a string. I don't mind that because the order in which I apply these classNames has no bearing on which one of these is going to be applied.

[09:13] However, the order in which I apply these styles does have a bearing in what's going to be applied. If I were to apply `{...style}` first, then there'd be no way for me, as a user of the box component, to override that to control what styles are actually going to be applied.

[09:27] Let's put everything back where it was and review. 

```js
function Box({className = '', style, size, ...rest}) {
  const sizeClassName = size ? `box--${size}` : ''
  const props = { 
    className: `box ${className} ${sizeClassName}`,
    style: {fontStyle: 'italic', ...style},
    ...rest,
  }
  return <div {...props} />
}
```

We had a bunch of CSS that we wanted to apply to our divs here to make a small, light-blue box, a medium pink box, and a large orange box. We did that manually by making divs for each one of these and applying the appropriate classNames and style value.

[09:45] Then, in an effort to reduce duplication, we created this function component called Box, which accepts a className, combines that with the className necessary to make a box, accepts a style prop, and combines that with the style necessary to make a box with a font style of italic.

[10:02] Also accepting a size prop to separate the code for the users of the Box component from the code necessary to make a box of that specified style. Then we take the rest of the props, and spread that on the underlying div so that users can provide additional props like ID is root as well as the children prop for the contents of the box.

[10:24] One last thing that I want to show you is [Tailwind CSS](https://tailwindcss.com/), which I recommend you take a look at for building consistent user interfaces. Definitely give this a look for building and styling your applications. You can apply the same principles that we learned to building custom components that utilize these classNames.