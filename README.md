# Mealy

Container for mealy state machines

## API

## Example: Working with a state machine

```js
import { Machine } from 'mealy';

const todosMachine = Machine.create(
  'app',
  {
    name: 'stand by',
    todos: []
  },
  {
  'stand by': {
    'add new todo': function ({ todos }, todo) {
      todos.push(todo);
      return { todos };
    },
    'delete todo': function ({ data }, index) {
      return { data: state.data.splice(index, 1) };
    },
    'fetch todos': function * () {
      yield { name: 'fetching' };

      try {
        const todos = await getTodos('/api/todos');
      } catch (error) {
        return { name: 'fetching failed', todos: [], error };
      }

      return { name: 'stand by', todos };
    }
  },
  'fetching failed': {
    'fetch todos': function * () {
      yield { name: 'stand by', error: null };
      this.fetchTodos();
    }
  }
});

todosMachine.fetchTodos();
```

## Example: React integration

```js
import React from 'react';
import { connect } from 'mealy/react';

class TodoList extends React.Component {
  render() {
    const { todos, error, isFetching, fetchTodos, deleteTodo } = this.props;

    if (isFetching) return <p>Loading</p>;
    if (error) return (
      <div>
        Error fetching todos: { error }<br />
        <button onClick={ fetchTodos }>try again</button>
      </div>
    );

    return (
      <ul>
      { todos.map(({ text}) => <li onClick={ deleteTodo }>{ text }</li>) }
      </ul>
    );
  }
}

export default connect(TodoList)
  .with('app')
  .map(({ name, ...stateData }, machine) => { ...stateData, ...machine });
```

## Misc

* [Mealy state machine](https://en.wikipedia.org/wiki/Mealy_machine)
