# API

## context(contexts, mapContextToProps)

```js
import { context } from 'react-contextual'
```

`context` can be used as a functionwrapper or decorator, it generally works with any Context, it isn't bound to contextuals store model.

Example 1: Mapping a single context value as a prop.

```js
context(ThemeContext, theme => ({ theme }))(Component)
```

Example 2: mapContextToProps behaves similar to Reduxes mapStateToProps, the components own props can always be used as well.

```js
context(UsersContext, (users, props) => ({ user: users[props.id] }))(Component)
```

Example 3: Mapping several contexts is also possible, just wrap them into an array.

```js
context([ThemeContext, CountContext], ([theme, count]) => ({ theme, count }))(Component)
```

## connectStore(mapContextToProps)

```js
import { connectStore } from 'react-contextual'
```

`connectStore` is sugar for `connect`. You don't need to worry about the actual context in that case, but you could use `connect` if you supply it, then you could even mix it with other contexts:

```js
import { context, StoreContext } from 'react-contextual'

context(StoreContext, ({ state, actions }) => ({ ... }))(Component)
```

## StoreProvider

```js
import { StoreProvider } from 'react-contextual'
```

Perhaps the worlds smallest Redux-like store. It pulls it off by letting React handle context distribution and state diffing. Declare the initial state with the `initialState` prop, and actions with the `actions` prop. That's it! The Provider will distribute `{ state, actions }` to listening consumers, either using Reacts API directly or contextuals `connect` HOC. There is an additional prop `renderOnce` that is `true` by default, it will prevent the sub-tree from rendering on state-changes so that you can safely wrap your app into the provider.

StoreProvider will only render once to prevent sub-tree re-rendering on every occuring change. Children otherwise behave normally of course. Any change to the store caused by an action will trigger consuming components.

Actions are made of a collection of functions which return an object that is going to be merged back into the state using regular `setState` semantics.

They can be simple ...

```js
{
    setName: name => ({ name }),
    setAge: age => ({ age }),
}
```

Or slightly more complex when you pass functions instead, which allow you to access the stores state, useful for computed/derived props, composition, deep-merging or memoization:

```js
{
    setName: name => state => ({ name: `${state.title} ${state.surname}` },
    setAge: age => state => ({ age: state.age + 1 }),
}
```