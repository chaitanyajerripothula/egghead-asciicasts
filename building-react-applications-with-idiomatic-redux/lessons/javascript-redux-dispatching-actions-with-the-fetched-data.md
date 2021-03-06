I can extract the common code between the **lifecycle hooks** into a separate method. I will call it `fetchData`. It will fetch the data for the current **props**.


**VisibleTodoList.js**
```javascript
fetchData() {
  fetchTodos(this.props.filter).then(todos =>
    console.log(this.props.filter, todos)
  );
}
```
In this case, the data we want to fetch only depends on the `filter`. I'm calling this method from the `componentDidMount` hook, to fetch the initial data, and I also call it whenever the `filter` changes inside the `componentDidUpdate`, lifecycle hook.


**VisibleTodoList.js**
```javascript
componentDidMount() {
  this.fetchData();
}

componentDidUpdate(prevProps) {
  if (this.props.filter !== prevProps.filter) {
    this.fetchData();
  }
}
```
We want the fetched `todos` to become a part of the **Redux Store state**. The only way to get something into the state is to **dispatch an action**. I'm going to call the callback prop called `receiveTodos`, whether to `todos` I just fetched.


**VisibleTodoList.js**
```javascript
fetchData() {
  fetchTodos(this.props.filter).then(todos =>
    this.props.receiveTodos(todos);
  );
}
```
To make it available inside the component, I need to pass a function called `receiveTodos` that would be an **action creator** inside the second argument to `connect`. 


**VisibleTodoList.js**
```javascript
VisibleTodoList = withRouter(connect(
  mapStateToProps,
  { onTodoClick: toggleTodo, reveiveTodos }
  )(VisibleTodoList))
```
Since the name of the function will match the name of the callback prop, I can use the shorter ES6 property notation.

I will import `receiveTodos` from the file where I define all other action creators. 


**VisibleTodoList.js**
```javascript
import { toggleTodo, receiveTodos } from '../actions';
```
Now, I can open that file with the action creators and actually implement it.

I'm creating a new `export const` function called `receiveTodos` that takes the server response as an argument and returns an object with the type of `receiveTodos` and the response as a field.

The **reducer** is handling this **action**. We'll need to know which filter the `response` corresponds to so I'm adding a `filter` as an argument to the `receiveTodos` action creator and I'm passing it as part of the action object.


**index.js**
```javascript
export const receiveTodos = (filter, response) => ({
  type: 'RECEIVE_TODOS',
  filter,
  response,
});
```
I'm going back to my component so I can pass the `filter` through the action creator. I'm destructering the `filter` and `receiveTodos` from props using [ES6 destructering syntax](https://egghead.io/lessons/ecmascript-6-destructuring-assignment?course=learn-es6-ecmascript-2015). It's important that I destructer the `filter` right away, because by the time the callback fires `this.props.filter` might have changed because they use it and might have navigated away.


**VisibleTodoList.js**
```javascript
fetchData() {
  const { filter, receiveTodos } = this.props;
  fetchTodos(this.props.filter).then(todos =>
    receiveTodos(filter, todos)
  );
}
```
If around the app now, I will see Redux actions being dispatched, ready to be handled by the reducers. They include all the necessary information, such as the `filter` and the server response.

![Dispatched Action](https://res.cloudinary.com/dg3gyk0gu/image/upload/v1553542112/transcript-images/javascript-redux-dispatching-actions-with-the-fetched-data-dispatched-action.jpg)

As I navigate through the app, the component fetches data in lifecycle hooks and dispatches Redux actions when the data is ready. As a final touch, I like to write less boilerplate code when importing action creators.

I'm replacing name to imports with the namespace `import` so that any function exported from the actions file will be in the object called `actions`, which I will pass as a second argument to connect.


**VisibleTodoList.js**
```javascript
import * as actions from '../reducers';

VisibleTodoList = withRouter(connect(
  mapStateToProps,
  actions
)(VisibleTodoList));
```
I'm destructuring the props, because the `toggleTodo` action creator needs to be passed under `onTodoClick` callback prop name, but the rest of the props can be passed as the other.


**VisibleTodoList**
```javascript
render() {
  const { toggleTodo, ...rest } this.props;
  return <TodoList {...this.props} />;
}
```
The `rest` object now continues all the props other than to `toggleTodo`, so I will pass them through. I will also pass `toggleTodo` as `onTodoClick` prop, because that's what `TodoList` component expect.


**VisibleTodoList**
```javascript
render() {
  const { toggleTodo, ...rest } this.props;
  return <TodoList 
          {...rest} 
          onTodoClick={toggleTodo} 
          />;
}
```
Finally, I'm splitting it over multiple lines for a little better readability. Let's recap how we fetch asynchronously in response to route changes. In the file where I define action creators, I add a new action creator called `receiveTodos` that accepts the `filter` and the `response` as arguments.

**index.js**
```javascript
export const receiveTodos = (filter, response) => ({
  type: 'RECEIVE_TODOS',
  filter,
  response,
});
```
It produces a regular Redux action object that can be dispatched after the request of the server has completed. I created a new react class component so that I can use lifecycles hooks, such as `componentDidMount` and `componentDidUpdate` to fetch the data.

**VisibleTodoList.js**
```javascript
componentDidMount() {
  this.fetchData();
}

componentDidUpdate(prevProps) {
  if (this.props.filter !== prevProps.filter) {
    this.fetchData();
  }
}
```
I'm fetching the data when the component mounts for the first time and if the `filter` prop, provided by react router changes, which means that user has navigated in the app. The `VisibleTodoList` is the container component that enhances a **presentational component** called `TodoList` when the data fetch an object.

However, the class we wrote does not subscribe to the store itself so we wrap it in a `connect()` call so that the containers generated by react Redux connect subscribes to the store and passes the props through the `VisibleTodoList` component we wrote.

**VisibleTodoList.js**
```javascript
VisibleTodoList = withRouter(connect(
  mapStateToProps,
  actions
)(VisibleTodoList));
```
Finally, the component generated by `withRouter` subscribes to the router changes so we have access to the router `params` in `mapStateToProps`. The components we want to export are the result of colon disrupters so I reassign `VisibleTodoList` and export it.

**VisibleTodoList.js**
```javascript
const mapStateToProps = (state, { parama }) => {
  const filter = params.filter || 'all';
  return {
    todos: getVisibleTodos(state, filter),
    filter,
  };
};
```
Inside the `mapStateToProps`, I read the `filter` value from the parameters supply direct order or use the default value. I used to only use `filter` for calculating the `getVisibleTodos`, but now, I also want to pass it as a prop so that it's available inside the component.

Having access to the `filter` prop, let's determine whether I should fetch the data and pass it to the function that fetches todos. I import this function from the file where I define our fake API, which can be replaced by rest API client.

**VisibleTodoList.js**
```javascript
fetchData() {
  const { filter, receiveTodos } = this.props;
  fetchTodos(filter).then(todos =>
    receiveTodos(filter, todos)
  );
}
```
We fetch data when the component mounts and when the `filter` prop changes. `fetchTodos` return the promise so when it resolves, I use the response, to dispatch an action `receiveTodos` with the `filter` and the server response.

The `receiveTodos` function available on the props dispatches the action returned by `receiveTodos` action creator automatically. This happens because I pass at this part of the actions object to connect.

**VisibleTodoList.js**
```javascript
VisibleTodoList = withRouter(connect(
  mapStateToProps,
  actions
)(VisibleTodoList));
```
The actions object contains all the exported action creators defined in the `actions.js` file, because I use the ES6 namespace `import` syntax. The `receiveTodos` action creator does not automatically dispatch an action, but the `receiveTodos` prop injected by connect does.

When I call it, it will call my action creator with the same arguments and dispatch the resultant action to the Redux Store. Inside the `render()` method, I render to the presentational `TodoList` component.

**VisibleTodoList.js**
```javascript
render() {
  const { toggleTodo, ...rest } this.props;
  return <TodoList 
          {...rest} 
          onTodoClick={toggleTodo} 
          />;
}
```
Now that all action creators are injected as props with the same names, I have to grab `toggleTodo` specifically and pass it as `onTodoClick` called by prop that `TodoList` expects, but the rest props can be passed with their existing names.