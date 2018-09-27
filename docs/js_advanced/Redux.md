# Javascript基础 - Redux

## 目录
- [What is Redux](#what-is-redux)
- [Get State from the Redux Store](#get-state-from-the-redux-store)
- [Dispatch an Action Event](#dispatch-an-action-event)
- [Register/Subscribe a Store Listener](#registersubscribe-a-store-listener)
- [Combine Multiple Reducers](#combine-multiple-reducers)
- [Send Action Data to the Store](#send-action-data-to-the-store)
- [Use Middleware to Handle Asynchronous Actions](#use-middleware-to-handle-asynchronous-actions)

### What is Redux

1. Redux is a state management framework that can be used with a number of different web technologies, including React.
2. In Redux, there is a single state object that's responsible for the entire state of your application.
3. Any time any piece of your app wants to update state, it must do so through the Redux ```store```.
4. The unidirectional data flow makes it easier to track state management in your app.
5. The Redux ```store``` is an object which holds and manages application ```state```.
6. Reducers in Redux are responsible for the state modifications that take place in response to actions.
7. The reducer function simply takes ```state``` and ```action``` as arguments and always returns a new ```state```.
8. An ```action``` is simply a JavaScript object that contains information about an action event that has occurred. 
9. It must carry a ```type``` property that specifies the 'type' of action that occurred.
10. The reducer function must always return a new copy of state and never modify state directly. ```state``` is read-only.
11. Redux ```Thunk``` middleware to handle asynchronous actions.

### Get State from the Redux Store

```
const store = Redux.createStore(
  (state = 5) => state // reducer function
);
const currentState = store.getState();
```

### Dispatch an Action Event

```
// store.dispatch(actionCreator()) or store.dispatch({type: 'LOGIN'});

const store = Redux.createStore(
  (state = {login: false}) => state
);
const loginAction = () => {
  return {
    type: 'LOGIN'
  }
};
store.dispatch(loginAction());
```

### Register/Subscribe a Store Listener

```
const ADD = 'ADD';
const reducer = (state = 0, action) => {
  switch(action.type) {
    case ADD:
      return state + 1;
    default:
      return state;
  }
};
const store = Redux.createStore(reducer);
// global count variable:
let count = 0;

// when an action is dispatched against the store
// The subscribe listener function will be called 
store.subscribe(function() {
  count = count + 1;
})

store.dispatch({type: ADD});
console.log(count); // 1
store.dispatch({type: ADD});
console.log(count); // 2
```

### Combine Multiple Reducers

```
const INCREMENT = 'INCREMENT';
const DECREMENT = 'DECREMENT';
const counterReducer = (state = 0, action) => {
  switch(action.type) {
    case INCREMENT:
      return state + 1;
    case DECREMENT:
      return state - 1;
    default:
      return state;
  }
};
const LOGIN = 'LOGIN';
const LOGOUT = 'LOGOUT';
const authReducer = (state = {authenticated: false}, action) => {
  switch(action.type) {
    case LOGIN:
      return {
        authenticated: true
      }
    case LOGOUT:
      return {
        authenticated: false
      }
    default:
      return state;
  }
};
const rootReducer = Redux.combineReducers({
  auth: authReducer,
  count: counterReducer
});
const store = Redux.createStore(rootReducer);
```

### Send Action Data to the Store

```
const ADD_NOTE = 'ADD_NOTE';
const notesReducer = (state = 'Initial State', action) => {
  switch(action.type) {
    case ADD_NOTE:
      return action.text;
    default:
      return state;
  }
};
// action creator function that returns an action
const addNoteText = (note) => {
  return {
    type: ADD_NOTE,
    text: note
  }
};
const store = Redux.createStore(notesReducer);
console.log(store.getState());
store.dispatch(addNoteText('Hello!'));
console.log(store.getState());
```

### Use Middleware to Handle Asynchronous Actions

```
const REQUESTING_DATA = 'REQUESTING_DATA'
const RECEIVED_DATA = 'RECEIVED_DATA'

const requestingData = () => { return {type: REQUESTING_DATA} }
const receivedData = (data) => { return {type: RECEIVED_DATA, users: data.users} }

const handleAsync = () => {
  return function(dispatch) {
    dispatch(requestingData());
    setTimeout(function() {
      let data = {
        users: ['Jeff', 'William', 'Alice']
      }
      dispatch(receivedData(data));
    }, 2500);
  }
};
const defaultState = {
  fetching: false,
  users: []
};
const asyncDataReducer = (state = defaultState, action) => {
  switch(action.type) {
    case REQUESTING_DATA:
      return {
        fetching: true,
        users: []
      }
    case RECEIVED_DATA:
      return {
        fetching: false,
        users: action.users
      }
    default:
      return state;
  }
};
const store = Redux.createStore(
  asyncDataReducer,
  Redux.applyMiddleware(ReduxThunk.default)
);
store.dispatch(handleAsync);
```
