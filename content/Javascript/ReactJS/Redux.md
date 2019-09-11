---
title: Getting Started with Redux
---

Redux gives you a store, and lets you keep state in it, and get state out, and respond when the state changes. But that’s all it does.

It’s actually react-redux that lets you connect pieces of the state to React components.

> The state is the data, and the store is where it’s kept

## Redux Store


## Redux Reducer

Reducer's job is to take the current `state` and `action` and return the new state. It has another job, too. It should return the **initial state** the first time it's called.

**Reducer Rule # 1** = Never return undefined from a reducer
**Reduce Rule # 2** = Reduces must be a pure functions (They can't modify their arguments, and they can't have side effects)

## Redux Actions

An action is Redux-speak for a plain object with a property called `type`. In order to keep things sane and maintainable, we Redux users usually give our actions types that are plain strings, and often uppercased, to signify that they’re meant to be constant values.

An action object describes a change you want to make (like “please increment the counter”) or an event that happenend (like “the request to the server failed with this error”).

In order to make an action DO something, you need to dispatch it.

### Redux Dispatch

The store we created earlier has a built-in function called dispatch. Call it with an action, and Redux will call your reducer with that action (and then replace the state with whatever your reducer returned).

### References
