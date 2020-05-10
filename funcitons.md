# Functions

JavaScript functions are first-class objects.
They can have properties and methods just like any other object.
The difference is that functions can be called and return a value.

```js
// function declaration
function foo() {
	console.log('foo called');
}

// it can have properties and methods just like any object.
foo.x = 10;
foo.bar = function() {
	console.log('bar called');
}

foo(); // foo called
console.log(foo.x); // 10
foo.bar(); // bar called
```

## arguments

Every function (non-arrow) has `arguments` in its local scope which corresponds to the arguments passed to the function.

```js
foo(1, 2);

function foo(x, y) {
	console.log(x); // 1
  console.log(y); // 2
	console.log(arugments); // [1, 2]
}
```


#### is it an Array?

Though it appears like an Array it isn't. It is an Array-like Object.

* Keys are numbers starting 0.
* It has `length` property.
* It is `Iterable`.

```js
foo(1, 2);

function foo() {
	console.log(Array.isArray(arguments)); // false
	console.log(arguments.length); // 2
  
  // can be updated
  arguments[0] = 3; // [3, 2], length: 2
  
  // can we add new index?
  // Yes, but length will still be 2
  arguments[2] = 10; // [3, 2, 10], length: 2
}
```

Lentgh of the `arguments` object is based on the number of arguments with witch function is called.
But not based on the arguments in function declaration.

## Function Scope

A function creates a lexical scope of all declarations (including params) and can be accessed with in the scope.

```js
function foo(x, y) {
	var z = 10;
	return x + y + z;
}
```

```
// Scope
window
	|- foo => #function ref
			|- x
      |- y
      |- z
```

Hence the variables with same name as params can't be declared with let or const in the function.

```js
function foo(x, y) {
	var x = 10; // Alloweds, as multiple var declarations are allowed.
  let y = 5;  // SyntaxError: Identifier 'y' has already been declared
	return x + y;
}
```

## IIFE (Immediately-invoked function expression)

As name suggests it is the function that runs as soon as it is defined.
This provides a way to execute some JavaScript code in its own scope as every function creates a scope for itself.

```js
var foo = function() {
	// function body
}
// or
function foo() {
	// function body
}

foo();

// these two statements could be simplified as
function() {
	// function body
}();
```

**Does this work?** Idealy it should, but it throws `SyntaxError`.

When Engine finds any statement which starts with `function` keyword, it treats it as function declaration.
Since function declarations needs a name it throws `SyntaxError: Unexpected token (`.

So giving it a name works?

```js
function foo() {
	// function body
}();

// Still throws `SyntaxError` but for a different reason.
```

JavaScript engine parses the above definition as two statements.

```js
function foo() {} // a function declaration
// and
(); // grouping operator which is used as a means to control precedence of evaluation
```

Because grouping operator needs an expression inside it, Engine throws `SyntaxError: Unexpected token )`.
By placing an expression inside it resolves the error, but the function isn't executed either.

To make this work, function declaration must be turned as expression.

```js
// assignement
var result = function(){}();

// everything to the right of a math operator (+,-,*,/)
+function(){}(); 
0-function(){}();

// ternary oparator
0?0:function(){}();

// grouping operator
(function(){}()); // whole statement
(function(){})(); // or just function can be put inside grouping oparator

// even 'new' keyword can be used
new function(){}();
```

Though Engine is happy with any of the above forms of IIFE, 
use the grouping operator brace forms (either are fine)
because they are widely recognized as IIFEs.


```js
(function() {
	// function body
})();

// or

(function() {
	// function body
}());
```

## Arrow functions

ECMA2015 specification introduces a **arrow** syntax for a function, which doesn't have **arguments**, **this** or **super**.
And they can't be used as function declarations or constructors.

```js
let foo = (name) => {
	console.log('Hello ' + name);
}

foo('world'); // Hello world
```

If it takes only one param, the paranthesis become optional.

```js
let foo = name => { console.log('Hello ' + name); };
```

If it has one statement in the body, then it doen't need to be wrapped inside `{` and `}`.

```js
let foo = name => console.log('Hello ' + name);

let bar = name => 'Hello ' + name; // it becomes the return value

console.log(bar('world')); // Hello world
```

