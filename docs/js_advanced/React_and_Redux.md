# Javascript高阶 - React and Redux

## 目录
- [how to use Redux with React](#how-to-use-redux-with-react)
- [Use Provider to Connect Redux to React](#use-provider-to-connect-redux-to-react)
- [Connect React to Redux](#Connect-React-to-Redux)
- [Connect Redux to the Messages App](#Connect-Redux-to-the-Messages-App)
- [Example of Writing React and Redux Code](#Example-of-Writing-React-and-Redux-Code)

### how to use Redux with React

1. React is a view library that you provide with data, then it renders the view in an efficient, predictable way.
2. Redux is a state management framework that you can use to simplify the management of your application's state.
3. Use the ```react-redux``` package to pass Redux ```state``` and ```dispatch``` to your React components as ```props```.
4. Use ```react-redux``` to connect the Redux store with your component, thereby extracting the local state into the Redux store.

### Use Provider to Connect Redux to React

1. The ```Provider``` is a wrapper component from React Redux that wraps your React app.
1. This wrapper then allows you to access the Redux ```store``` and ```dispatch``` functions throughout your component tree.
3. ```Provider``` takes two props, the Redux store and the child components of your app. 

```
// Redux Code:
const ADD = 'ADD';
const addMessage = (message) => {
  return {
    type: ADD,
    message
  }
};
const messageReducer = (state = [], action) => {
  switch (action.type) {
    case ADD:
      return [
        ...state,
        action.message
      ];
    default:
      return state;
  }
};
const store = Redux.createStore(messageReducer);

// React Code:
class DisplayMessages extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      input: '',
      messages: []
    }
    this.handleChange = this.handleChange.bind(this);
    this.submitMessage = this.submitMessage.bind(this);
  }
  handleChange(event) {
    this.setState({
      input: event.target.value
    });
  }
  submitMessage() {
    const currentMessage = this.state.input;
    this.setState({
      input: '',
      messages: this.state.messages.concat(currentMessage)
    });
  }
  render() {
    return (
      <div>
        <h2>Type in a new Message:</h2>
        <input
          value={this.state.input}
          onChange={this.handleChange}/><br/>
        <button onClick={this.submitMessage}>Submit</button>
        <ul>
          {this.state.messages.map( (message, idx) => {
              return (
                 <li key={idx}>{message}</li>
              )
            })
          }
        </ul>
      </div>
    );
  }
};
const Provider = ReactRedux.Provider;
class AppWrapper extends React.Component {
  render(){
    return (
      <Provider store={store}>
        <DisplayMessages/>
      </Provider>
    );
  };
};
```

### Connect React to Redux

1. Behind the scenes, React Redux uses the ```store.subscribe()``` to implement ```mapStateToProps()```
2. Behind the scenes, React Redux uses the ```store.dispatch()``` to implement ```mapDispatchToProps()```
3. Each component only has access to the state it needs with ```mapStateToProps()``` and ```mapDispatchToProps()```
4. The ```connect``` method takes two optional arguments, ```mapStateToProps()``` and ```mapDispatchToProps()```, and immediately call the result with your component. e.g.
```connect(mapStateToProps, mapDispatchToProps)(MyComponent)```
5. If you want to omit one of the arguments to the connect method, you pass ```null``` in its place.

```
const addMessage = (message) => {
  return {
    type: 'ADD',
    message: message
  }
};
const mapStateToProps = (state) => {
  return {
    messages: state
  }
};
const mapDispatchToProps = (dispatch) => {
  return {
    submitNewMessage: (message) => {
      dispatch(addMessage(message));
    }
  }
};
class Presentational extends React.Component {
  constructor(props) {
    super(props);
  }
  render() {
    return <h3>This is a Presentational Component</h3>
  }
};
const connect = ReactRedux.connect;
const ConnectedComponent = connect(mapStateToProps, mapDispatchToProps)(Presentational);
```

### Connect Redux to the Messages App

```
// Redux:
const ADD = 'ADD';
const addMessage = (message) => {
  return {
    type: ADD,
    message: message
  }
};
const messageReducer = (state = [], action) => {
  switch (action.type) {
    case ADD:
      return [
        ...state,
        action.message
      ];
    default:
      return state;
  }
};
const store = Redux.createStore(messageReducer);

// React:
class Presentational extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      input: '',
      messages: []
    }
    this.handleChange = this.handleChange.bind(this);
    this.submitMessage = this.submitMessage.bind(this);
  }
  handleChange(event) {
    this.setState({
      input: event.target.value
    });
  }
  submitMessage() {
    const currentMessage = this.state.input;
    this.setState({
      input: '',
      messages: this.state.messages.concat(currentMessage)
    });
  }
  render() {
    return (
      <div>
        <h2>Type in a new Message:</h2>
        <input
          value={this.state.input}
          onChange={this.handleChange}/><br/>
        <button onClick={this.submitMessage}>Submit</button>
        <ul>
          {this.state.messages.map( (message, idx) => {
              return (
                 <li key={idx}>{message}</li>
              )
            })
          }
        </ul>
      </div>
    );
  }
};

// React-Redux:
const mapStateToProps = (state) => {
  return { messages: state }
};
const mapDispatchToProps = (dispatch) => {
  return {
    submitNewMessage: (newMessage) => {
       dispatch(addMessage(newMessage))
    }
  }
};
const Provider = ReactRedux.Provider;
const connect = ReactRedux.connect;

const Container = connect(mapStateToProps, mapDispatchToProps)(Presentational);

class AppWrapper extends React.Component {
  constructor(props) {
    super(props);
  }
  render() {
    return (
      <Provider store={store}>
        <Container />
      </Provider>
    );
  }
};
```

### Example of Writing React and Redux Code

```
import React from 'react'
import ReactDOM from 'react-dom'
import { Provider, connect } from 'react-redux'
import { createStore, combineReducers, applyMiddleware } from 'redux'
import thunk from 'redux-thunk'

import rootReducer from './redux/reducers'
import App from './components/App'

const store = createStore(
  rootReducer,
  applyMiddleware(thunk)
);

ReactDOM.render(
  <Provider store={store}>
    <App/>
  </Provider>,
  document.getElementById('root')
);
```
