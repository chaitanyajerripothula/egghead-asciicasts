Kent: 0:00 Here, we have an `App` that's managing some items. We can add items and remove items. We have a fixed set of items that can be added or removed. We have a button to add additional items, which is disabled when we've added all of our items. Then we iterate over the items that we have and render a list item for each of those.

```js
const allItems = [
  { id: 'a', value: 'apple' },
  { id: 'o', value: 'orange' },
  { id: 'g', value: 'grape' },
  { id: 'p', value: 'pear' }
];

function App() {
  const [items, setItems] = React.useState(allItems);

  function addItem() {
    setItems([...items, allItems.find(i => !items.includes(i))]);
  }

  function removeItem(item) {
    setItems(items.filter(i => i !== item));
  }

  return (
    <div>
      <button disabled={items.length >= allItems.length} onClick={addItem}>
        add item
      </button>
      <ul style={{ listStyle: 'none', paddingLeft: 0 }}>
        {items.map(item => (
          <li>
            <button onClick={() => removeItem(item)}>remove</button>{' '}
            <label htmlFor={`${item.value}-input`}>{item.value}</label>{' '}
            <input id={`${item.value}-input`} defaultValue={item.value} />
          </li>
        ))}
      </ul>
    </div>
  );
}
```

0:22 Here, we have remove buttons for each one of these and then the item name itself and an `input` with that item in it.

0:30 When we render this, we're going to get this warning that says `each child in a list should have a unique key prop`. What that's talking about is any time you render an array of React elements -- in our case, we're rendering these `li`'s -- each one of those React elements must have a key prop `key={}` associated with it so that React can track these appropriately over time.

0:50 Don't be fooled by the syntax. There's nothing really magic going on here. What we're doing is we're taking an array of `strings`. We're mapping over that array of `strings` and turning that array of `strings` into an array of React elements. Specifically, in our case, these are `li` elements.

```js
<ul style={{ listStyle: 'none', paddingLeft: 0 }}>
  {items.map(item => (
    <li>
      <button onClick={() => removeItem(item)}>remove</button>{' '}
      <label htmlFor={`${item.value}-input`}>{item.value}</label>{' '}
      <input id={`${item.value}-input`} defaultValue={item.value} />
    </li>
  ))}
</ul>
```

1:05 That's the case where you need to have a key prop for every React element in the array, and we're seeing this warning because we don't.

1:12 **To fix this warning** is actually pretty simple. We look at the items that we're iterating over. That is this array right here. Each one of those items has an `ID` that uniquely identifies the item. We're going to use that `ID` as the key prop.

```js
const allItems = [
  { id: 'a', value: 'apple' },
  { id: 'o', value: 'orange' },
  { id: 'g', value: 'grape' },
  { id: 'p', value: 'pear' }
];
```

1:29 Here we'll say, `key={item.id}` If we save this, we don't get that warning anymore.

1:36 I'm not a huge fan of changing my code just to make warnings go away. I like to understand why that warning is there in the first place. That's why we have this contrived example for me to show you.

1:47 Here, we have a list of each one of these items, where the item itself is the label and then the default `value` for the `input` is the item as well. Then we can remove these and add them.

1:58 As I removed them, if you watched carefully, you might have noticed an interesting bug.

2:02 If I click on remove here and remove and remove and remove, everything works out just fine. If I remove from the beginning, now we're having orange being associated to the apple. Grape is associated to orange. Pear is associated to grape. That's interesting.

2:18 If I remove from the middle here, now we have pear is associated to orange. Orange is associated to apple. Things are all kinds of messed up. **This is the bug that the key prop helps you to avoid.**

2:29 If you consider what happens when we click this remove button or this add button, then this strange behavior will make sense. When we click on remove item, for example, if we try to remove the grape, then that goes into this code right here, which calls `setItems`. That sets the items to the same list of items that we had before, except we're filtering out the item that you were trying to remove.

```js
function removeItem(item) {
  setItems(items.filter(i => i !== item));
}
```

2:52 This call to `setItems` is going to trigger a re-render of our `App`. The `App` is going to come down here. We're going to create these React elements. We're going to iterate over this list of items, which now has one less item than it had before. We're going to hand that off to React so that it can update the DOM appropriately.

3:10 The way React updates the DOM is it has a reference to the JSX `elements` that you gave it the last time it rendered this `App` component. It compares those React `elements` with the new React `elements` that you just returned it this time. Then it updates the DOM accordingly.

3:24 When you're giving it an array of React `elements`, unless React has some sort of identifier to know which element is which, it doesn't know whether you removed an `element` or maybe you added three and removed four or maybe you changed the order and just removed the first one.

3:39 It doesn't have any insight into what it is that you did to this array of React `elements` between the last time it rendered and this time. Anytime you're rendering an array of React `elements`, you need to give it a `key` so that it can determine whether `elements` were removed, added, or modified. That's why providing a unique `key` for each one of these items is going to fix this problem.

4:02 Now, if we click remove on the grape, all of the inputs and their labels are correct.

4:08 You'll notice also that if you try to provide some `key` where it's the same `key` for each one of these, you're going to get a warning there because it encountered `two children with the same key`.

4:20 `Keys need to be unique so that components maintain their identity across updates. Non-unique keys may cause children to be duplicated and/or omitted. The behavior is unsupported and could change in a future version`.

4:31 So, hard-coding a specific `key` that's duplicated across these elements could lead to some very unexpected behavior. We still experience that bug that we had before.

4:43 The `key` that you provide for each `element` of this array of React `elements` needs to be unique to the item that you're rendering. Typically, that's going to be some sort of `ID`, as in our case.

4:56 Another mistake that I see people make sometimes is they try to use the index as the `key`. While you get rid of the warning, you do not get rid of the bugs. That's because as React is comparing the previous version with the new version, what you're saying is the `element` that was at index four is actually now at index three.

5:16 React doesn't know that. It maintains the state for this `input` to be the same as the one that was at index three the last time rather than being the one that was in index four last time. It's really important that you keep the `key` as something that's unique to the item that this `element` in the array is representing.

5:36 Here's another little demo that I have at the bottom of this file. I'm just going to uncomment that. We'll save this. We'll get a refresh.

```jsx
ReactDOM.render(<FocusDemo />, document.getElementById('root'));
```

In this example, we have those items. Every 1,000 milliseconds, we have this interval that shuffles those items and triggers a re-render with that shuffled version of the items so we randomize the items every time.

```js
React.useEffect(() => {
  const interval = setInterval(() => {
    setItems(shuffle(items));
  }, 1000);
  return () => clearInterval(interval);
}, []);
```

5:57 We have three versions of this, one that renders those items as `inputs` without a `key`, another that renders those items with inputs with a `key` of index, and then the last that renders those items with an appropriate `key`.

```jsx
<div>
  <div>
    <h1>Without Key</h1>
    {items.map(item => (
      <input value={item.value} />
    ))}
  </div>
  <div>
    <h1>With Key as Index</h1>
    {items.map((item, index) => (
      <input key={index} value={item.value} />
    ))}
  </div>
  <div>
    <h1>With Key</h1>
    {items.map(item => (
      <input key={item.id} value={item.value} />
    ))}
  </div>
</div>
```

6:11 You'll notice that they're all updating correctly, meaning that they're all jumping around the screen as they should, but the `focus` is not updating correctly.

6:22 I'm focused on apple right now. When that one moves, my `focus` doesn't go around with apple. Also, if I try to `highlight` one of these, then my `highlight` goes away as well.

6:32 You'll notice that the with `key` as index suffers from the exact same problem. Even though it's not getting the warning in the console, you're still not fixing this bug. Only when you have a `key` in there will your `focus` travel around with the `input` that it's actually associated with because React is able to determine where to move the `focus` as your component updates.

6:55 In review, it's really common in React to take an array and map that array to an array of `elements` and render that directly in your JSX. When you do that, it's really important that you add a `key` to the root React `element` of each `element` in the array so that React can track changes over time and make sure that it preserves the state of each `element` in the array and its descendants.
