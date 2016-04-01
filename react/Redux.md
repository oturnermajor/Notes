1. **First principle of redux** is that (regardless of app's complexity) the whole state of the application is represented as a single javascript object. 
 * All changes to state are explicit.
2. **Second principle of redux** is that the state tree is read-only. When you want to change the state, you must dispatch an action. Actions are also sing javascript objects.
3. **Pure vs. Impure functions**: pure functions are predictable. They do not modify what is passed to them, and given the same input, you will always get the same output. Impure functions may override what is passed to them, and may have side-effects.
4. **The Reducer**: takes the previous state and the action being dispatched, and returns the next state. In every redux app, there is a function which does this - the reducer. It is important that the reducer does not modify the state given to it. It is a pure function. However, this doesn't mean that you can't keep references to parts of the previous state in the next state.
5. This makes testing very easy
6. **Store Methods**: We can create our state store with the createStore method from Redux:
    ```javascript
    const { createStore } = Redux;
    const store = createStore(counter); // counter is your reducer method

    ```
    * The state within the store can then be accessed and changed like so:
    ```javascript
    store.getState(); // gets the state..
    store.dispatch({type: 'INCREMENT'}); // dispatches an action (the parameter)
    store.subscribe(() => { // registers a callback to be called any time an
      document.body.innerText = store.getState(); // action has been dispatched
    })
    ```
7. **Implementing Store from Scratch**: What does the `createStore` method actually do? Let's build it from scratch.
    ```javascript
    const createStore = (reducer) => {
      let state;
      let listeners = [];

      const getState = () => state;

      const dispatch = (action) => {
        state = reducer(state, action);
        listeners.forEach(listener => listener());
      };

      const subscribe = (listener) => {
        listeners.push(listener);
        return () => {
          listeners = listeners.filter(l => l !== listener);
        }
      }; // so you could call store.subscribe(listener)(); to remove a listener.

      dispatch({});

      return {getState, dispatch, subscribe};
    }
    ```
8. Counter with React. Link the JSX to the store by setting a prop to `{store.getState()}` and onclick functions calling `store.dispatch()` . Also put `store.subscribe(render);` at the end so that the render function is called every time the state changes.
9. **NO MUTATIONS**: This is a central theme in Redux. 
 * Instead of changing arrays with `array.push`, we should use `array.concat`, which doesn't modify the array. We can also use spread operators to return a new array more concisely i.e. `return [...oldArray, newElement]`
 * Likewise, don't use `array.splice`. Instead, use `array.slice` (possibly with `array.concat`) - again, you can use spread operators here: `return [...list.slice(0, index), ...list.slice(index + 1)]` to remove an element.
 * To change an element in an array, you could do the same, but insert `list[index] + 1` between the two `...list.slice` calls (to increment by 1).
10. **NO MUTATIONS IN OBJECTS EITHER** - use `Object.assign` instead:
    ```javascript
    const toggleTodo = (todo) => {   // todo is an object for an item on the todo list 
      return Object.assign({}, todo, {  //first argument: 
        completed: !todo.completed
      });
    };
    ```
* This is a new ES6 method. Only use it with babel or with a polyfill. Or use the object spread operator - change the above function to:
    ```javascript
    return {
      ...todo,
      completed: !todo.completed
    }
    ```
11.