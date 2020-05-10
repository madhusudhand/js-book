# Prototype

Almost every object in JavaScript has a `prototype` property in addition to its own properties.
prototype is also an object and it will have a `prototype` property pointing to another object and so on.
This is called as `prototype chain` which goes upto the Object.prototype which is set to `null`.
Understanding the prototype chain is important to understand JavaScript objects.

### property/medhod lookups

When an object property/method is accessed, lookup happens in the object for a direct property. If its undefined, then lookup happens in its prototype and then its prototype so on until it is found anywhere in the **prototype chain**. Otherwise **undefined** will be returned.

```js
let obj = {};
```

## Prototypal Inheritance

[WORK IN PROGRESS]
