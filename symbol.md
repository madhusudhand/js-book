# Symbol

**symbol** is a new data type (7th) introduced in ES2015 specification.
Symbols are like unique tokens that can generally be used as object properties. A symbol value as property makes the object properties anonymous.

A symbol can be created with a class-like Factory function `Symbol`.
Optionally it takes a string parameter which lets you describe the symbol and is for debugging purpose only.
It can't be used as constructor. Calling it with **new** throws error.

Every time you call the factory function, a new symbol is created and is unique across the instance.

```js
let s1 = Symbol(); // new unique symbol
let s2 = Symbol(); // new unique symbol

console.log(s1 === s2); // false

s1 = Symbol('foo');
s2 = Symbol('foo');

console.log(s1 === s2); // false
// though the descriptions of s1 and s2 are same, symbols are still unique.
// descriptions are just debugging purpose only
```

[WORK IN PROGRESS]

