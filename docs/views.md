# Views (or Components)

## Combining to React

You bring all the flow together with the Views (or components), actually *the Action generators*.
Use the **`Flux.mixins.storeListener`** mixin to get a component into the Flux system.
Also pass `dispatcher={DispatcherName}` attribute to the *main* or *top level* React component. It will
then pass the dispatcher to all the child component to which you have applied the `storeListener` mixin.

```js
// Child components don't have to have the storeListener.

var TodoItemView = React.createClass({

  render: function (todo) {
    return <li onClick={this.handleClick}>{this.props.todo.text}</li>
  },

  handleClick: function () {
    TodoActionCreator.removeTodo(this.props.todo);
    // or, this.props.dispatcher.removeTodo(this.props.todo);
  }

});

var TodoListView = React.createClass({

  mixins: [Flux.mixins.storeListener],

  render: function () {
    var self = this;
    return <ul>
      {this.getStore('todoStore').todos.map(function (todo) {
        return <TodoItemView todo={todo}></TodoItemView>
      })}
    </ul>
  }

});
```

### `storeDidChange` and `storesDidChange`

These methods are triggered when a store is changed, and all stores are changed. You can use
these methods in your components to perform specific actions after a store changes. Please note
that the **`Flux.mixins.storeListener`** will automatically update your component's `state` and force
a render, so it is not required that you define these methods to peform routine re-renders.

```js
var TodoListView = React.createClass({

  mixins: [Flux.mixins.storeListener],

  // when all stores are updated
  storesDidChange: function () {
    console.log("All stores are now updated.");
  },

  // when a store updates
  storeDidChange: function (storeName) {
    console.log(storeName + " store is now updated.");
  },

  render: function () {
    // ...
  }

});
```

### `getStore(storeName)`

It returns the related store to the component.

```js
...
  return <ul>
    {this.getStore('todoStore').todos.map(function (todo) {
      return <TodoItemView todo={todo}></TodoItemView>
    })}
  </ul>
...
```

## Combining to Flight.js

Since DeLorean.Flux doesn't require React, you can use it everywhere, including **Flight.js**

```javascript
var TodoCountUI = flight.component(function () {

  this.render = function () {
    this.$node.html(TodoListDispatcher.getStore('todoStore').todos.length);
  };

  this.after('initialize', function() {
    // You should listen changes
    TodoListDispatcher.on('change:all', this.render.bind(this));
    this.render();
  });
});
```
