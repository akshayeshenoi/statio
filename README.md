Simple State Manager
====================

Philosophy
----------
The library essentially tries to implement Facebook's [Flux](https://facebook.github.io/flux/docs/in-depth-overview.html#content) application architecture. It maintains a global immutable state object that can be accessed by registered callbacks across the application. While [Redux](https://redux.js.org/) does an amazing job at predictable state management and __should be used for large applications, this library can be used for simple state management for lightweight applications__.

The API is super tiny and the concepts are very straightforward and easy to grasp.

Concepts
--------
There are three principles that need to be understood:
- Stores
- Actions
- Dispatch & Subscribe

__Stores__ are repositories where you can store data. These are part of the global state object maintained by the library. _Stores should not be mutated directly._ 

__Actions__, denoted by a string, are functions that you create to tell the library how to mutate the state. They might take data objects as parameters and need to be _dispatched_ using the API.

__Dispatching__ and __subscribing__ are mechanisms that allow your elements to mutate or react to a change in state.

Putting it all together, every _store_ created should be associated with _actions_, _dispatchers_ and _subscribers_.

Library in action
-----------------
The following example creates a store and an action associated with it:
```js
// creating a store
const testStore = $store.createStore('testStore');

// creating an action
testStore.createAction('test-action-1', (data) => {
  testStore.foo = data;
});
```

At any point, a reference to the store can be obtained by the `getStore()` API
```js
// getting a store refernce
testStore = $store.getStore('testStore')
testStore.createAction('test-action-2', () => {
  // action w/o data param but returns an object
  testStore.foo += ' append this';
  return testStore.foo;
});
```

To 'dispatch' an action is to modify the store (or not) and notify all 'subscribers' of the change.
```js
// get a reference to the testStore
const testStore = $store.getStore('testStore');

// subscribe to an action
const testAction2 = testStore.subscribe('test-action-2');
testAction2.then((foo) => {
  console.log(foo);
});

// dispatch an action
testStore.dispatch('test-action-2', 'fooString');
```

API
---
- `createStore(string: 'storeName'): store`  
  -> Creates a new store.
- `getStore(string: 'storeName'): store`  
  -> Returns store object.
- `store.createAction(string: 'action-name', function: actionFn): void`  
  -> Creates an action. Takes as parameter the action name and the function describing what it's supposed to do.
- `store.dispatch(string: 'action-name', Object|undefined: data): void` 
  -> Dispatch an action with optional data. The library executes the function registered and then notifies subscribers of the action.
- `store.subcribe(string: 'action-name'): actionPromise`  
  -> Subcribe to action and use the `then()` Promise API to process it.
