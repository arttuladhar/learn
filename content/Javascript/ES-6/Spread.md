---
title: Spread Syntax
---

Spread Syntax represented by `...` expands an iterable to its individual elements.

### Common use cases:

* Spread in array literal

```javascript
const head = ['eyes','nose','mouth']
const body = ['stomach','hip','legs']
const headBody = [...head, ...body]
console.log(headBody)

// ["eyes", "nose", "mouth", "stomach", "hip", "legs"]
```

* Spread in Object Literals

```javascript
const obj1 = { foo: 'bar', x: 42 };
const obj2 = { foo: 'baz', y: 13 };

const clonedObj = { ...obj1 };
console.log(clonedObj)
// {foo: "bar", x: 42}

const mergedObj = { ...obj1, ...obj2 };
console.log(mergedObj)
// {foo: "baz", x: 42, y: 13}
```