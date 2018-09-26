# Javascript基础 - React

## 目录
- [What is React](#what-is-react)
- [JSX must return a single element](#jsx-must-return-a-single-element)
- [Use the Syntax {/* */} to Add Comments in JSX](#use-the-syntax---to-add-comments-in-jsx)
- [Render HTML Elements to the DOM](#render-html-elements-to-the-dom)
- [Define an HTML Class in JSX](#define-an-html-class-in-jsx)
- [Self-Closing JSX Tags](#self-closing-jsx-tags)
- [Create a Stateless Functional Component](#create-a-stateless-functional-component)
- [Create a React Component](#create-a-react-component)
- [Create a Component with Composition](#create-a-component-with-composition)
- [Use React to Render Nested Components](#use-react-to-render-nested-components)
- [Pass Props to a Stateless Functional Component](#pass-props-to-a-stateless-functional-component)
- [Pass an Array as Props](#pass-an-array-as-props)
- [Use Default Props](#use-default-props)
- [Override Default Props](#override-default-props)
- [Use PropTypes to Define the Props You Expect](#use-proptypes-to-define-the-props-you-expect)
- [Access Props Using this.props](#access-props-using-thisprops)
- [Create a Stateful Component](#create-a-stateful-component)
- [Set State with this.setState](#set-state-with-thissetstate)
- [Create a Controlled Input](#create-a-controlled-input)
- [Create a Controlled Form](#create-a-controlled-form)
- [Pass State as Props to Child Components](#pass-state-as-props-to-child-components)
- [Pass a Callback as Props](#pass-a-callback-as-props)
- [Manage Updates with Lifecycle Methods](#manage-updates-with-lifecycle-methods)
- [Optimize Re-Renders with shouldComponentUpdate](#optimize-re-renders-with-shouldcomponentupdate)
- [Introducing Inline Styles](#introducing-inline-styles)
- [Add Inline Styles in React](#add-inline-styles-in-react)
- [Use && for a More Concise Conditional](#use--for-a-more-concise-conditional)

### What is React

1. React, popularized by Facebook, is an open-source JavaScript library for building user interfaces.
2. It is used to create components, handle state and props, utilize event listeners and certain life cycle methods to update data as it changes.
3. React combines HTML with JavaScript functionality to create its own markup language, JSX.

### JSX must return a single element

```
// Valid JSX:
const JSX = (
    <div>
    <h1>This is a block of JSX</h1>
        <p>Here's a subtitle</p>
    </div>
);

// Invalid JSX:
const JSX = (
    <p>Paragraph One</p>
    <p>Paragraph Two</p>
    <p>Paragraph Three</p>
);
```

### Use the Syntax {/* */} to Add Comments in JSX

```
const JSX = (
    <div>
        {/* this is comments */}
        <h1>This is a block of JSX</h1>
        <p>Here's a subtitle</p>
    </div>
);
```

### Render HTML Elements to the DOM

```
const JSX = (
  <div>
    <h1>Hello World</h1>
    <p>Lets render this to the DOM</p>
  </div>
);
ReactDOM.render(JSX, document.getElementById('changed-node'))
```

### Define an HTML Class in JSX

```
const JSX = (
  <div className="myDiv">
    <h1>Add a class to this div</h1>
  </div>
);
```

### Self-Closing JSX Tags

```
const JSX = (
  <div>
    <h2>Welcome to React!</h2> <br />
    <p>Be sure to close all tags!</p>
    <hr />
  </div>
);
```

### Create a Stateless Functional Component

```
const DemoComponent = function() {
  return (
    <div className='customClass' />
  );
};
```

### Create a React Component

```
class Kitten extends React.Component {
  constructor(props) {
    super(props);
  }
  render() {
    return (
      <h1>Hi react</h1>
    );
  }
}
```

### Create a Component with Composition

```
const ChildComponent = () => {
  return (
    <div>
      <p>I am the child</p>
    </div>
  );
};
class ParentComponent extends React.Component {
  constructor(props) {
    super(props);
  }
  render() {
    return (
      <div>
        <h1>I am the parent</h1>
        <ChildComponent />
      </div>
    );
  }
};
```

### Use React to Render Nested Components

```
const TypesOfFruit = () => {
  return (
    <div>
      <h2>Fruits:</h2>
      <ul>
        <li>Apples</li>
        <li>Blueberries</li>
        <li>Strawberries</li>
        <li>Bananas</li>
      </ul>
    </div>
  );
};
const Fruits = () => {
  return (
    <div>
      <TypesOfFruit />
    </div>
  );
};
class TypesOfFood extends React.Component {
  constructor(props) {
    super(props);
  }
  render() {
    return (
      <div>
        <h1>Types of Food:</h1>
        <Fruits />
      </div>
    );
  }
};
```

### Pass Props to a Stateless Functional Component

```
const CurrentDate = (props) => {
  return (
    <div>
      <p>The current date is: {props.date}</p>
    </div>
  );
};
class Calendar extends React.Component {
  constructor(props) {
    super(props);
  }
  render() {
    return (
      <div>
        <h3>What date is it?</h3>
        <CurrentDate date={Date()}/>
      </div>
    );
  }
};
```

### Pass an Array as Props

```
const List= (props) => {
  return <p>{props.tasks.join(", ")}</p>
};
class ToDo extends React.Component {
  constructor(props) {
    super(props);
  }
  render() {
    return (
      <div>
        <h1>To Do Lists</h1>
        <h2>Today</h2>
        <List tasks={["walk dog", "workout"]}/>
      </div>
    );
  }
};
```

### Use Default Props

```
const ShoppingCart = (props) => {
  return (
    <div>
      <h1>Shopping Cart Component</h1>
    </div>
  )
};
ShoppingCart.defaultProps = {
    items: 0
}
```

### Override Default Props

```
const Items = (props) => {
  return <h1>Current Quantity of Items in Cart: {props.quantity}</h1>
}
Items.defaultProps = {
  quantity: 0
}
class ShoppingCart extends React.Component {
  constructor(props) {
    super(props);
  }
  render() {
    return <Items quantity={10} />
  }
};
```

### Use PropTypes to Define the Props You Expect

```
const Items = (props) => {
  return <h1>Current Quantity of Items in Cart: {props.quantity}</h1>
};

Items.propTypes = {
    quantity: PropTypes.number.isRequired
    // quantity: PropTypes.number.isRequired,
    // handleClick: PropTypes.func.isRequired,
    // isFull: PropTypes.bool.isRequired
}

Items.defaultProps = {
  quantity: 0
};

class ShoppingCart extends React.Component {
  constructor(props) {
    super(props);
  }
  render() {
    return <Items quantity={10}/>
  }
};
```

### Access Props Using this.props

```
class ReturnTempPassword extends React.Component {
  constructor(props) {
    super(props);
  }
  render() {
    return (
        <div>
            <p>Your temporary password is: <strong>{this.props.tempPassword}</strong></p>
        </div>
    );
  }
};
class ResetPassword extends React.Component {
  constructor(props) {
    super(props);
  }
  render() {
    return (
        <div>
          <h2>Reset Password</h2>
          <h3>We've generated a new temporary password for you.</h3>
          <h3>Please reset this password from your account settings ASAP.</h3>
          <ReturnTempPassword tempPassword={'password'} />
        </div>
    );
  }
};
```

### Create a Stateful Component

```
class StatefulComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
        name: 'StatefulComponent',
        message: 'hello world'
    }
  }
  render() {
    const message = this.state.message;
    return (
      <div>
        <h1>{this.state.name} {message}</h1>
      </div>
    );
  }
};
```

### Set State with this.setState

```
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      name: 'Initial State'
    };
    this.handleClick = this.handleClick.bind(this);
  }
  handleClick() {
    this.setState({
        name: 'React Rocks!'
    })
  }
  render() {
    return (
      <div>
        <button onClick={this.handleClick}>Click Me</button>
        <h1>{this.state.name}</h1>
      </div>
    );
  }
};
```

### Create a Controlled Input

```
class ControlledInput extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      input: ''
    };
    this.handleChange = this.handleChange.bind(this);
  }
  handleChange(event) {
      this.setState({
          input: event.target.value
      })
  }
  render() {
    return (
      <div>
        <input value={this.state.input} onChange={this.handleChange} />
        <h4>Controlled Input:</h4>
        <p>{this.state.input}</p>
      </div>
    );
  }
};
```

### Create a Controlled Form

```
class MyForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      input: '',
      submit: ''
    };
    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }
  handleChange(event) {
    this.setState({
      input: event.target.value
    });
  }
  handleSubmit(event) {
    event.preventDefault();
    this.setState({
        submit: this.state.input
    })
  }
  render() {
    return (
      <div>
        <form onSubmit={this.handleSubmit}>
          <input value={this.state.input} onChange={this.handleChange} />
          <button type='submit'>Submit!</button>
        </form>
        <h1>{this.state.submit}</h1>
      </div>
    );
  }
};
```

### Pass State as Props to Child Components

```
class MyApp extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      name: 'CamperBot'
    }
  }
  render() {
    return (
       <div>
         <Navbar name={this.state.name} />
       </div>
    );
  }
};
class Navbar extends React.Component {
  constructor(props) {
    super(props);
  }
  render() {
    return (
    <div>
      <h1>Hello, my name is: {this.props.name} </h1>
    </div>
    );
  }
};
```

### Pass a Callback as Props

```
class MyApp extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      inputValue: ''
    }
    this.handleChange = this.handleChange.bind(this);
  }
  handleChange(event) {
    this.setState({
      inputValue: event.target.value
    });
  }
  render() {
    return (
       <div>
        <GetInput input={this.state.inputValue} handleChange={this.handleChange} />
        <RenderInput input={this.state.inputValue} handleChange={this.handleChange} />
       </div>
    );
  }
};
class GetInput extends React.Component {
  constructor(props) {
    super(props);
  }
  render() {
    return (
      <div>
        <h3>Get Input:</h3>
        <input
          value={this.props.input}
          onChange={this.props.handleChange}/>
      </div>
    );
  }
};
class RenderInput extends React.Component {
  constructor(props) {
    super(props);
  }
  render() {
    return (
      <div>
        <h3>Input Render:</h3>
        <p>{this.props.input}</p>
      </div>
    );
  }
};
```

### Manage Updates with Lifecycle Methods

```
class Dialog extends React.Component {
  constructor(props) {
    super(props);
  }
  componentWillUpdate() {
    console.log('Component is about to update...');
  }
  componentDidUpdate() {
    console.log('Component has updated...');
  }
  componentWillReceiveProps(nextProps) {
      console.log(this.props.message); // First Message
      console.log(nextProps.message); // Second Message
  }

  render() {
    return <h1>{this.props.message}</h1>
  }
};
class Controller extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      message: 'First Message'
    };
    this.changeMessage = this.changeMessage.bind(this);
  }
  changeMessage() {
    this.setState({
      message: 'Second Message'
    });
  }
  render() {
    return (
      <div>
        <button onClick={this.changeMessage}>Update</button>
        <Dialog message={this.state.message}/>
      </div>
    );
  }
};
```

### Optimize Re-Renders with shouldComponentUpdate

```
class OnlyEvens extends React.Component {
  constructor(props) {
    super(props);
  }
  shouldComponentUpdate(nextProps, nextState) {
    return nextProps.value % 2 == 0 ? true : false
  }
  componentWillReceiveProps(nextProps) {
    console.log('Receiving new props...');
  }
  componentDidUpdate() {
    console.log('Component re-rendered.');
  }
  render() {
    return <h1>{this.props.value}</h1>
  }
};
class Controller extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: 0
    };
    this.addValue = this.addValue.bind(this);
  }
  addValue() {
    this.setState({
      value: this.state.value + 1
    });
  }
  render() {
    return (
      <div>
        <button onClick={this.addValue}>Add</button>
        <OnlyEvens value={this.state.value}/>
      </div>
    );
  }
};
```

### Introducing Inline Styles

```
class Colorful extends React.Component {
  render() {
    return (
      <div style={{color: "yellow", fontSize: 16}}>Big Red</div>
    );
  }
};
```

### Add Inline Styles in React

```
const styles = {
    color: "purple",
    fontSize: 40,
    border: "2px solid purple"
}
class Colorful extends React.Component {
  render() {
    return (
      <div style={styles}>Style Me!</div>
    );
  }
};
```

### Use && for a More Concise Conditional

```
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      display: true
    }
    this.toggleDisplay = this.toggleDisplay.bind(this);
  }
  toggleDisplay() {
    this.setState({
      display: !this.state.display
    });
  }
  render() {
    const buttonOne = <button>You May Enter</button>;
    const buttonTwo = <button>You Shall Not Pass</button>;
    return (
       <div>
         <button onClick={this.toggleDisplay}>Toggle Display</button>
         {this.state.display && <h1>Displayed!</h1>}
         {this.state.display ? {buttonOne} : {buttonTwo}}
       </div>
    );
  }
};
```
