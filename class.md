# Class

JavaScript is a Object-based language. Everything (except premitives) is an object or derived from an object.
Even the `function` is an object. And as we learned, inheritance in JavaScript is also between objects but not classes.
And so there is no need for a class. Where as a `class` syntax has been introduced to JavaScript with ES2015 specification.
So what it means to JavaScript?

A class in JavaScript is not to be confused with class in other object-oriented languages.
It is just a syntactic sugar over existing constructor functions and prototype based inheritance.

Writing code with class syntax keeps the code clean and simplifies the inheritance. Under the hood, Class is a function and inheritance is prototype based.

```js
class Foo {
	constructor() {
		// constructor initializations
  }
}
// typeof for a class returns function
console.log(typeof Foo); // function

// create an object for a class
// you do it just like ES5 constructor function even for the class.
let obj = new Foo();
````

The above class is same as

```js
function Foo() {
	// constructor initializations
}
console.log(typeof Foo); // function

let obj = new Foo();
```

## Arguments, Instance properties

Similar to a function constructor, where all the initializations to instance properties are done inside the function,
class has a `constructor` method which will be called with **new**.

```js
function Foo(name) {
	this.name = name;
  console.log(arguments); // arguments object
}

// is now

class Foo {
	constructor(name) {
		this.name = name;
    console.log(arguments); // arguments object
  }
}

let obj = new Foo('Rose');
```

## Prototype and Static Methods

Class syntax simplifies the way we add prototype and static methods to any object.
They can simply be added as follows.

```js
class Foo {
	// constructor method is optional.

	// Prototype method. (Foo.prototype.bar)
	bar () {
		console.log('bar called');
  }
  
  // static method (Foo.baz)
  static baz() {
		console.log('baz called');
  }
}

let obj = new Foo();
obj.bar(); // bar called
obj.baz(); // TypeError: obj.baz is not a function

Foo.baz(); // baz called
```

As **baz** is a static method, it is available on Foo but not on any instantiated objects.

**Note:** A class body can only contain methods, but not data properties like other languages. They need to be initialized inside constructor.

Other thing to note is by writing a class, its not required to add methods within the class declaration always.
You can do it just like you do it for a function declaration.

```js
class Foo {}

// prototype method
Foo.prototype.bar = function() {
	console.log('bar called');
};

// static method
Foo.baz = function() {
	console.log('baz called');
}
```

Though its not a recomended practice, a class declaration isn't restricted from methods being added to it at later time.

## Inheritance

Inheritance is one of the simplified syntax with Classes. It lets you create a subclass of an existing constructor with the `extends` clause.
Though the syntax appears to be class inheritance just like any other language, it is still a `prototypal inheritance`. Remember, Class is a function.

```js
class Parent {
	parentMethod() {}
}

class Child extends Parent {
	constructor() {
		super(); // call parent custructor
  }
  
  childMethod() {}
}
```

Class like inheritance simplifies the prototype linking, call to parent constructor any many.
If not the above code, we would have written as follows.

```js
function Parent() {}
Parent.prototype.parentMethod = function() {};

function Child() {
	Parent.call(this);
}
Child.prototype.childMethod = function() {};

Child.prototype = Object.create(Parent.prototype);
Child.prototype.constructor = Child;
```

**Note:** `extends` need not always be between class declarations. it lets you create a subclass of any existing constructor (which may or may not have been defined as a class)

```js
function Foo() {}

// class can extend a function constructor
class Bar extends Foo {}
```

## super constructor & super object

**super** is somewhat similar to **this**. *this* in JavaScript refers to the current context, whereas **super** refers to the object's prototype.

```
let parentObj = {
	print() {
		console.log('Hello world');
	}
};

let obj = {
	print() {
		super.print(); // super now refers to obj.__proto__
	}
};

obj.print(); // Throws error as print method doesn't exist on obj.__proto__

// link parentObj to obj's prototype
Object.setPrototypeOf(obj, parentObj); // sets obj.__proto__ = parentObj;

obj.print(); // Hello world
```

**super** when used with a class, it can either call parent class constructor or its methods.

```
class Foo {
	constructor() {
		console.log('Foo constructor called');
  }
  
  print(str) {
		console.log(str);
  }
}

class Bar extends Foo {
	constructor() {
		console.log('Bar constructor called');
		super();
		this.name = 'Bar';
  }
  
  print() {
		super.print('Hello ' + this.name);
  }
}

let b = new Bar(); // Foo constructor called
									 // Bar constructor called

b.print(); // Hello Bar
```

A super constructor must be called before *this* in the child constructor. Otherwise it throws ReferenceError.

## getters and setters

A class can have getters and setters inside it. They are like any other method with `get` and `set` before them.
Only difference is that *get* methods doesn't take any parameters and *set* methods takes exactly one parameter.

```js
class Book {
  constructor(name) {
    this.name = name;
    this._pages = 0;
  }

  get pages() {
    return this._pages;
  }

  set pages(count) {
		if (typeof count === 'number') { // validation
			this._pages = count;
		}
  }
}

let book = new Book('JavaScript');
book.pages = 100; // setter called
console.log(book.pages); // getter method called and returns 100
```

## Class and Function - there are differences

Though Class and Function are similar mostly, there are some minor differences between the two declarations.
Some safety checks have been taken care in Class.

#### Class can't be called

Unlike function which can either be called or can be used with **new** to create an object, Class can't be called. It can only be used as constructor function.
It will thow TypeError if you try to call it.

```js
class Foo {}

let obj = new Foo(); // ok

Foo(); // throws TypeError
```

#### Multiple class declarations aren't allowed

Just like **let**, multiple class declarations with same name aren't allowed in the same scope.

```js
class Foo {}

class Foo {} // Not allowed as the Identifier 'Foo' has already been declared
```

#### Class declarations are Hoisted

Another difference between functions declarations and class declarations is that
functions can be accessed before its declarations where as Classes can't be accessed before declaration.

```js
new Foo(); // throws ReferenceError

class Foo { }
```

*Does this mean the class declaration is not hoisted?*

Though it appears to be not hoisted, it is actually hoisted. 
But it can't be accessed before its declaration. This behavior is because of the **TDZ (Temporal Dead Zone)** which doesn't let you use before its declaration, just like **let and const** (discussed in Scope section).

Any of ES2015+ specifications are not deviated from Hoisting. They are hoisted and fall into **TDZ**.

What if they doesn't get hoisted. There it brings a new problem. It will resolve declarations from outer lexical scope which is not supposed be happening.

```js
function Foo() {
	console.log('Foo from global scope');
}

function wrapper() {
	new Foo(); // throws ReferenceError though Foo is available in global scope
  
  // if 'class Foo' is not hoisted in the current scope,
  // it would have resolved to 'Foo' from global scope
  // which is not intended

	class Foo {
		constructor() {
			console.log('Foo from wrapper function scope');
		}
	}
}

wrapper();
```

If 'class Foo' is not hoisted in the current scope of *wrapper*, it would have resolved to 'Foo' from global scope because of lexical scoping. Which breaks the code.

