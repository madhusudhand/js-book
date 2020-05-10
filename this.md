# This

`this` in JavaScript is one of the confused keywords, which typically appears in functions.
Unlike other class based languages such as Java where it always referes to the instance object, the value of `this` in JavaScript is determined based on how the code is executed.
In other words it is the `execution context object`.

We can understand **this** in a function by asking the following questions.

* Who called it?
* How is it called?
* Is it a constructor call?

## Who called it?

When there are no explict bindings (which will be discussed later), the value of **this** is determined by the context of execution.
In simple terms **this** will be the object using which the function is called.

Let us understand with examples.

Example 1:

```js
getName() {
	return this.name;
}

console.log(getName()); // undefined
```

In this example the function *getName* is available in the global context and called in the global context. i.e the function called in the context of **window** (global for nodejs).
Hence *window* is the calling object which becomes *this*.

Method returns *undefined* as there no *name* property on the *window* object.

**NOTE**: in `strict mode`, when the function called in the global context, **this** will not be the *window*. Instead it will be undefined. So the fucntion call throws *TypeError: Cannot read property 'name' of undefined*

Example 2:

```js
let person = {
	name: 'Madhu',
  getName: function() {
		return this.name;
  }
};

console.log(person.getName()); // Madhu
```

Here in this example, the property *name* and method *getName* are part of the *person* object
and the method *getName* is called using the **person** object ( *person.getName()* ).

**Who called it?** its *person* object and it becomes the current context for the *getName*. So *this = person*.

The return value of the function will be *person.name*

Example 3:

Let us take the same example with a small change in the way method is called.

```js
let person = {
	name: 'Madhu',
  getName: function() {
		return this.name;
  }
};

let getName = person.getName; // instead of calling, assign it to a variable.

console.log(getName()); // undefined
```

Though the code symantically looks same, it wouldn't return the same value as before.

Why? The question **Who called it** will get the answer. It isn't called using **person** object.
Though *getName* is a method of the **person** object, call happened in the global context. Hence **this** will be **window**, but not **person** object.

Remember the question is not *"to whom the function belongs to?"*. We should always be looking at the function call to understand who the caller is.

Now we understood why *getName* returns undefined, but not *person.name*.

Example 4:

```js
let group = {
	name: 'Travelers',
  person: {
		name: 'Madhu',
		getName: function() {
			return this.name;
	  }
  }
};

console.log(group.person.getName()); // is it Madhu or Travellers?
```

**Who called it?** is it group or person object? 

Its *person*. *group* object is used to access the *person* object and the function call heppened in the context of *person* object. So, it returns *Madhu* but not *Travellers*.

What if the *person* object doesn't have a *name* property? Will it return 'Travellers'? Nope. It returns *undefined* still.

## How is it called? (External bindings)

The value of *this* can be set by binding an object externally to the function call. It can be done using **call**, **apply** or **bind**.

#### call and apply

Both methods are used to bind an object during the fuction call. Only difference is that *call* takes comma separated arguments, where as *apply* takes array of arguments.

```js
let person = {
  getName: function() {
		return this.name;
  }
};

let p1 = { name: 'Madhu' };

person.getName.call(p1); // Madhu
```

When the method called with external binding with an object, the bound object will be the **context object (this)**; which is p1 in the above example.

External binding always takes precedence over the calling object.

### bind

Unlike call and apply, *bind* returns a new function with the same scope but *this* bound to the given object regardless of how the function is called at later time.

```js
let person = {
  getName: function() {
		console.log(this.name);
  }
};

let p1 = { name: 'Madhu' };
let getName = person.getName.bind(p1);

getName(); // Madhu

let p2 = { name: 'JavaScript' };
getName.call(p2); // still gives Madhu
```

## Arrow Functions

As the arrow functions doesn't have its own **this** (it will always be enclosing lexical context's this), it doesn't care **who called it** or the **external bindings**.
However its not an error to do external binding during the function call. It Just ignores the bound object.

```js
let getName = () => {
	return this.name;
};

let p1 = { name: 'Madhu' };

getName.call(p1); // undefined
```

Though the function call has external binding, because its an arrow fuction, **this** will be determined by the outer lexical scope which is global in this case, it returns *undefined*.

## DOM event handlers

When **this** is used in a DOM event handler, it will always points to the element on which the handler is registered. (which is the event.currentTarget)

```html
<div id="container">
  <button>Click Me!</button>
</div>
```

```js
let container = document.getElementById('container');

container.addEventListener('click', function(event) {
	console.log(this === event.target);
  // true, when click event happens on container
  // false, if click event happens on button (or any child elements)
  
  console.log(this === event.currentTarget);
  // true, as this and event.currentTarget will aways be "container"
});
```

> **event.target**: element on which the event happened.  
**event.currentTarget**: element on which the event handeler is registered.



