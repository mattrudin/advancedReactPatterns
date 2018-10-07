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