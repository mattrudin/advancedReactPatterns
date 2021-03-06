# Advanced React Patterns
Cloned from Kent C. Dodds FrontendMasters course.


## Index

[1. Instructions](#1-instructions/) 

[2. Lessons learned](#2-lessons-learned)



## 1. Instructions
Origin: [advanced-react-patterns-v2](https://github.com/kentcdodds/advanced-react-patterns-v2/tree/frontend-masters)


## 2. Lessons learned
### Lesson 1: Build Toggle
#### React state: Toggle a boolean 
To toggle a boolean located in the state, the following version seems intuitive:
```javascript
toggleBoolean() {
    this.setState({
        isOn: !this.state.isOn
    })
}
```
But with this version some disadvantages rises. For example: A few updates on state will be batched together. If this is the case, isOn will not be updated on the invocation of toggleBoolean() immediately, but some time in the future with other batched updates on state.
To prevent this issue, an update function inside the setState function should be called, like the following example shows:
```javascript
toggleBoolean() {
    this.setState(
        currentState => {
            return {isOn: !currentState.on}
        }
    );
}
```
This function in the setState function does return a object with the toggled boolean.
The following approach is also valid:
```javascript
toggleBoolean() {
    const newBoolean = !this.state.isOn;
    this.setState({
        isOn: newBoolean
    });
}
```
This will first declare a const newBoolean with the opposite of the current state value of isOn. After that, setState does his job by setting the state with the new boolean.
In continuation to the previous example:
```javascript
toggleBoolean() {
    const currentBoolean = this.state.isOn;
    this.setState({
        isOn: !currentBoolean
    });
}
```
The const currentBoolean contains the current boolean and after that, the state will be set with the opposite of the current boolean.

### Lesson 2: Compound Components
#### static
[MDN: static](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes/static)  
[Medium: Understanding static in JS](https://medium.com/front-end-hacking/understanding-static-in-javascript-10782149993)  
[Youtube: Static Methods in JavaScript Tutorial](https://www.youtube.com/watch?v=10b6K9fORI4)  
Examples:
* Utility methods can be static (best practice; see youtube)
* only the class has the static methods, instances have not (see medium)

#### this.props.children: React.Children.map & React.cloneElement
[mxstbr: A deep dive into children in React](https://mxstbr.blog/2017/02/react-children-deepdive/)  
[learn.co: this.props.children](https://learn.co/lessons/react-this-props-children)  
```
*React.Children.map* has two parameters: the first one is the children
themselves, and the second one is a function that transforms the value of
the child. In this case, we're adding an extra prop. We do that using 
*React.cloneElement*. As the first argument we pass in the child 
component itself, and as the second argument, we pass in any additional 
props. Those additional props get merged with the child's existing props,
overwriting any props with the same key.
```
[Hashrocket: Mapping Over One Or Many Children In React](https://til.hashrocket.com/posts/yb1ee3dhxp-mapping-over-one-or-many-children-in-react)  
[Github: Dynamically Add Props To A Child Component](https://github.com/jbranchaud/til/blob/master/react/dynamically-add-props-to-a-child-component.md)  
[ReactJS: React.Children.map](https://reactjs.org/docs/react-api.html#reactchildrenmap)  
[ReactJS: cloneElement](https://reactjs.org/docs/react-api.html#cloneelement)  

#### Ternary with &&
```javascript
static On = (props) => (props.on && props.children);
```
Caution with lists:
```javascript
static On = (props) => (props.list.length && props.children);
```
If the list has 0 elements, react displays 0. To fix this issue, use Boolean(), like the following example:
```javascript
static On = (props) => (Boolean(props.list.length) && props.children);
```

### Lesson 3: Flexible Compound Components with context
#### React Context API
[ReactJS: Context](https://reactjs.org/docs/context.html)  
[Medium: Reacts new context API](https://medium.com/dailyjs/reacts-%EF%B8%8F-new-context-api-70c9fe01596b)  
[Medium: Learn the React Context API with a Practical Example You Can Bring to Your Apps](https://itnext.io/understanding-the-react-context-api-through-building-a-shared-snackbar-for-in-app-notifications-6c199446b80c)  
[Medium: How to use the new React context API](https://hackernoon.com/how-to-use-the-new-react-context-api-fce011e7d87)  
[Alligator.io: Understanding the React Context API](https://alligator.io/react/context-api/)  

#### constructor
In general, constructors won't be needed anymore. Methods can be declared with the arrow function, so they don't have to bind "this" in the constructor.
 There is an alternative pattern instead of constructors, as shown below.
```javascript
//with constructor
constructor(...args) {
    super(...args);
    this.toggle = () => {
      this.setState(
      ({on}) => ({on: !on}),
      () => this.props.onToggle(this.state.on)
      )
    }
    this.state = {on: false, toggle: this.toggle}
  
  }
  render() {
      return //something
  }

```

```javascript
//without constructor
 toggle = () => {
    this.setState(
      ({on}) => ({on: !on}),
      () => this.props.onToggle(this.state.on),
      )
  }
  state = {on: false, toggle: this.toggle}

  render() {
      return //something
  }

```
As seen above, less code has to be written with the same output in readability and function.

### Lesson 4: Render props
Seperates state from render. Eg.
```javascript
// state/logic
class Toggle extends React.Component {
  state = {on: false}
  toggle = () =>
    this.setState(
      ({on}) => ({on: !on}),
      () => {
        this.props.onToggle(this.state.on)
      },
    )
  
  render() {
    return this.props.children({on: this.state.on, toggle: this.toggle});
  }
}

// render/UI
    <Toggle onToggle={onToggle}>
      {({on, toggle}) => (
        <div>
          {on ? 'The button is on' : 'The button is off'}
          <Switch on={on} onClick={toggle} />
          <hr />
          <button aria-label="custom-button" onClick={toggle}>
            {on ? 'on' : 'off'}
          </button>
        </div>
      )}
    </Toggle>
```
[ReactJS: Render props](https://reactjs.org/docs/render-props.html)  
[Medium: Learn render props by example](https://engineering.dollarshaveclub.com/learn-render-props-by-example-da3e2524dd2e)  
[Medium: Compose render Props](https://blog.kentcdodds.com/compose-render-props-46cf491e9d19)[Medium: Answers to common questions about render props](https://blog.kentcdodds.com/answers-to-common-questions-about-render-props-a9f84bb12d5d)  
[Medium: When not to use render props](https://blog.kentcdodds.com/when-to-not-use-render-props-5397bbeff746)  

### Lesson 5: Prop collections
As the name implies: A collection of props for a given render prop.
If inside the render prop more components share the same props, those props can be collect to a collection of the same props.

### Lesson 6: Prop getters
Gives full control to the render part of the component. The user can give the component the props he wishes to use and the component will take care of the state.
Example:
```javascript
//utility function
const callAll = (...fns) => (...args) => fns.forEach(fn => fn && fn(...args))

//component with the props collector getStateAndHelpers and the props getter getTogglerProps
class Toggle extends React.Component {
  state = {on: false}
  toggle = () =>
    this.setState(
      ({on}) => ({on: !on}),
      () => this.props.onToggle(this.state.on),
    )

  getTogglerProps = ({onClick, ...props}) => ({
      onClick: callAll(this.toggle, onClick),
      'aria-expanded': this.state.on,
      ...props,
    })


  getStateAndHelpers() {
    return {
      on: this.state.on,
      toggle: this.toggle,
      togglerProps: this.getTogglerProps,
      },
    }
  
  render() {
    return this.props.children(this.getStateAndHelpers())
  }
}
```

### Lesson 7: State Initializers
Define an initial state and set the state to this initial state.
```javascript
class Toggle extends React.Component {
  initialState = {on: this.props.initialOn};
  state = this.initialState;
  //other code
}
```

### Lesson 8: State reducer
The state reducer allows people to control how the state is managed. State reducer hooks into every setState call to modify how the state is updated in the component.
[Medium: The state reducer pattern](https://blog.kentcdodds.com/the-state-reducer-pattern-%EF%B8%8F-b40316cfac57)  
[Medium: Discovering the State Reducer Pattern](https://medium.com/@ivanmontiel/discovering-the-state-reducer-pattern-3f324bb1a4c4)  


```javascript
class Toggle extends React.Component {
  initialState = {on: this.props.initialOn}
  state = this.initialState
  internalSetState = (changes, callback) => { //define the internalSetState method
    this.setState(currentState => {
      const changesObject = typeof changes === 'function' ? changes(currentState) : changes;
      const reducedChanges = this.props.stateReducer(currentState, changesObject);
      return reducedChanges;
    }, callback)
  }

  reset = () =>
    this.internalSetState(this.initialState, () => //every setState has to be changed to this.internalSetState
      this.props.onReset(this.state.on),
    )
```

### Lesson 9: State reducers with types
Either change the type of the input in the arguments...

```javascript
toggle = ({type = 'toggle'}) =>
  this.internalSetState(
    ({on}) => ({type, on: !on}),
    () => this.props.onToggle(this.state.on),
  )
```
...or in the internatSetState...

```javascript
reset = () =>
  this.internalSetState({type: 'reset', ...this.initialState}, () =>
    this.props.onReset(this.state.on),
  )
```

...and finally, ignore the type in the internalSetState to prevent a rerender of the component

```javascript
internalSetState(changes, callback) {
    this.setState(state => {
      const {type: ignoredType, ...remainingChanges} = reducedChanges;
```

#### ENUMS
[Stijn de Witt: Enums in JavaScript](https://stijndewitt.com/2014/01/26/enums-in-javascript/)  
[Medium: Enumeration Objects in JavaScript](https://medium.com/techtrument/enumeration-objects-in-javascript-ec06a83f39f2)  
[David Strauss: Cleaner JavaScript code with Enums](https://www.stravid.com/en/cleaner-javascript-code-with-enums/)  

### Lesson 10: Control props (synchronizing two components)
Check if the component is controlled by the user or the state itself
```javascript
  isOnControlled() {
    return this.props.on !== undefined;
  }
```
Get the right source: either props (if user control) or state
```javascript

  getState() {
    return {
      on: this.isOnControlled() ? this.props.on : this.state.on
    }
  }
```
Use getState in the following methods to get the right source
```javascript  
  toggle = () => {
    if (this.isOnControlled()) {
      this.props.onToggle(!this.getState().on) //use props
    } else {
      this.setState(
        ({on}) => ({on: !on}),
        () => {
          this.props.onToggle(this.getState().on) //use own state
        },
      )
    }
  }
```

### Lesson 11: Provider pattern
#### No-op
[code.i-harness: What is the JavaScript convention for no operation?](https://code.i-harness.com/en/q/14a1f46)  
[Medium: To no-op or Not To no-op](https://medium.com/front-end-hacking/slapdash-refactoring-d549e952706a)  
[Stackoverflow: What is the JavaScript convention for no operation?](https://stackoverflow.com/questions/21634886/what-is-the-javascript-convention-for-no-operation)  

#### Context API
See Lesson 3 for further information.

### Lesson 12: Higher Order Components
#### React.createRef()
[Medium: Introduction to Refs in React 16.3](https://medium.com/@sgroff04/introduction-to-refs-in-react-16-3-d94505b45a73)  
[css-tricks: Working with refs in React](https://css-tricks.com/working-with-refs-in-react/)  
[Medium: How to use React createRef](https://blog.logrocket.com/how-to-use-react-createref-ea014ad09dba)  

#### React.forwardRef()
[React JS: Forwarding refs](https://reactjs.org/docs/forwarding-refs.html)  
[Fullstack React: Fullstack React's Guide to using Refs in React Components](https://www.fullstackreact.com/articles/using-refs-in-react/)  

#### Component.displayName
If a component does not have a name, it will be displayed as "unknown". To give it a name, use YOUR_COMPONENT.displayName = 'YOUR_DISPLAY_NAME'.
[Today I learnt: Alter The Display Name Of A React Component](https://til.hashrocket.com/posts/xr5tbbybkd-alter-the-display-name-of-a-react-component)  

#### hoistNonReactStatics
[GitHub: hoist-non-react-statics](https://github.com/mridgway/hoist-non-react-statics)  

### Lesson 13: Rendux: Redux as a render prop
Setup of the component render prop (see excercise 13 for the redux part):
```javascript
render() {
    const {children} = this.props;
    const ui = typeof children === 'function' ? children(this.state) : children;
    return(
      <RenduxContext.Provider value={this.state} >
        {ui}
      </RenduxContext.Provider>
    )
  }
```