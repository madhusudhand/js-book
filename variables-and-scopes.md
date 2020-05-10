# Variables and Scopes

Just like any other language, one of the fundamental things to understand in JavaScript is a **Variable**.

## Variable

A variable in JavaScript is a reference of a value in the memory.
In other words its a **symbolic name** for a **value**. 
It has no type. Instead the value it holding has type.

```js
// values have types
console.log(typeof 10); // number
console.log(typeof 'JavaScript'); // string

var foo = 10; // foo holds reference of number 10
console.log(typeof foo); // number

foo = 'JavaScript'; // foo now holds the reference of string 'JavaScript'
console.log(typeof foo); // string
```

When a variable is given a new value, memory will be allocated for the value and the variable reference updated to new value. (old value will be garbage collected).

A variable can hold any type of data during its existance; which makes JavaScript a `Dynamically Typed Language`.

### *Data Types*

The *ECMAScript* standard defines seven data types. Of which

* Six are primitive types (value types)
	* **Boolean** - True, False
	* **Number** -- 10, 10.23, -1, 0, binary, hex, octal
	* **String** - 'JavaScript'
	* **Undefined** - undefined
	* **Null** - null
  * **Symbol** - Symbol()
* and an **Object** type (reference type)

**Note:** More about these data types will be discussed in later chapters.

So, where do these variables live?
How does the program find them when it needs them?

Let's understand by looking the code in Engine's perspective.

## Engine's perspective

To run a JavaScript code, the Engine does the following

1. Resolve scopes
2. Execute the statements

To resolve scopes, Engine first looks for `Variables` and `Function` declarations in the code.
This is done at the time of lexing (aka static time), before executing any statements. And its called as Lexical Scoping.

Once scopes are resolved, it starts executing the statements.

### Lexical scoping for Variable declarations

```js
var x = 1;
var y = 2;
var z = x + y; // 3
```

Engine first looks for variable declarations of the current scope (in this case variables are in global scope).
Then resolves  `x`, `y` and `z`.

```
// resolve scopes
window // global scope
  |- x
  |- y
  |- z
```

At this stage varibles doesn't hold any values. And so they are `undefined`.

Then the Engine starts executing statements(second phase).

```js
x = 1; // lookup x in the scope and give it value 1

y = 2; // lookup y in the scope and give it value 2

z = x + y;
// lookup x, y values in the scope and calculate sum (1 + 2 = 3)
// lookup z in the scope and give it result value 3
```

This is the simplest scenario. What if one of the variable **y** not resolved in the scope?

```js
var x = 1;
var z = x + y;
```

```
// Lexical Scope
window
	|- x
  |- z
  
// Execution
x = 1; // lookup x in the scope and give it value 1
z = x + y;
// lookup x in the scope and get value 1
// lookup y in the scope => not found
// And so throws ReferenceError: y is not defined
```

**ReferenceError:**
It is thrown when the reference not found during the scope lookup.
i.e. when the Identifier(variable name) not found in the scope.

### Lexical scoping for Function declarations

So far we have understood variable scopes.
Now let us understand scope resolution of function declarations.

```js
var x = 10;
function foo() {
 // function body
}
console.log(x, y, foo, bar);
function bar() {
 // function body
}
var y = 20;
```

Engine looks for variables and functions to resolve scopes.

```
// Lexical Scope
window
  |- x
  |- foo => #function ref
  |- bar => #function ref
  |- y
```

The difference here is that when engine finds a variable, it is scoped but no value is given.  
Where as when it finds a function declaration, it has to preserve two things *name* and the *function definition*.

The function identifier(name of the function) is scoped, then the function definition is loaded into the memory and its reference is given to its scoped identifier (name of the function).

So, in the above example `x`, `y` holds `undefined` and `foo`, `bar` holds function references after lexical scoping.

After resolving scopes, statements for execution will be

```
x = 10;
console.log(x, y, foo, bar); // 10, undefined, function, function
y = 20;
```

We get `undefined` for `y` but not 20. Because scope lookup for `y` gives `undefined` at the time of execution of *console.log*. The value for `y` is given in later statement.

**So far so good.**
But what if function and variable names are same?

```js
var foo = 10;
function foo() {}
function bar() {}
var bar;
console.log(foo); // would it be 10 or function?
console.log(bar); // would it be undefined or function?
```

Confused? Let us understad by writing lexical scope.

> Quick Recap:  
An identifier in the Lexical Scope is just a **Symbolic name** for a **value**.
It can hold a single reference of any type of value.

Keeping this in mind lets look at Scope at each statement.

| Statement         | Scope Tree                | Comment                                                                        |
|:----------------- |:------------------------- | ------------------------------------------------------------------------------ |
| var foo           | window                    | add identifier **foo** to Scope Tree                                           |
|                   | \|- foo                   |                                                                                |
| function foo() {} | window                    | create function in the memory                                                  |
|                   | \| - foo => #function ref | add identifier **foo** to the Scope Tree. But it already exists. So never mind |
|                   |                           | give the function reference to the identifier **foo**                          | 
| function bar() {} | window                    |                                                                                |
|                   | \|- foo => #function ref  |                                                                                |
|                   | \|- bar => #function ref  | create function in the memory                                                  |
|                   |                           | add identifier **bar** to the Scope Tree                                       |
|                   |                           | give the function reference to the identifier **bar**                          |
| var bar           | window                    | add identifier **bar** to the Scope Tree. But it already exists. So never mind |
|                   | \|- foo => #function ref  |                                                                                |
|                   | \|- bar => #function ref  |                                                                                |

Which would look like

```
// Scope
window
	|- foo => #function ref
	|- bar => #function ref
  
// Execution
foo = 10; // lookup foo in the scope and give it value 10.
					// so, function reference is replaced with a new reference of value 10
console.log(foo); // 10
console.log(bar); // function
									// - bar is still pointing to function reference
                  // - and so its not undefined
```

### Summary

> **Variable**:  
> add the identifier to the Scope **if it doesn't** exist. (if it already exists do nothing)

> **Function**:
> 1. create function in the memory
> 2. add the identifier to the Scope **if it doesn't** exist.
> 3. give the function reference to the identifier in the Scope.

So, when it comes to resolving scopes for a variable and function with same identifier, it always holds function reference.
Order of statements doesn't matter.

Also, because all the variables and function declarations are scoped no mater where they appear in the code, they can be used before their definition.
In a way this scoping process appears to be taking all declarations to top of the current scope. This gives rise to the term `Hoisting`.

## Hoisting

What we learned so far is what we call Hoisting in JavaScript. Variable and function declarations are hoisted, which lets us use them even before their declaration.

## Function Scope

A variable is which is defined in a fuction with `var` keyword is scoped to the function.
It can be in conditional statements, loops or evern after return statement, it will still be scoped to the function.

```js
function foo() {
	var x = 1;
  if (false) {
    var y = 2;
  }
  return;
  var z = 3;
}
foo();
```

```
// Scope
window
	|- foo => #function ref
			|- x
      |- y
      |- z
```

In this example even though the statements `var y = 2` and `var z = 3` are unreachable statements, the variables `y` and `z` still be scoped and be `undefined`.

**Scopes are nested. Very inner level can access all of its outer levels.**

```js
var x = 1;
function foo() {
	var y = 2;
  x = 2;
  console.log(x + y);
}
foo();
console.log(x + y);
```

```
// Scope
window
	|- x
	|- foo => #function ref
			|- y
    
// foo will not have any x reference in its scope because its not a variable declaration.
    
// Execution
x = 1;	// lookup x and set 1
foo();	// lookup foo and call

		// foo is called and current scope is now foo scope
		y = 2;  // lookup y in foo scope and set 2
    x = 2;  // lookup x in foo scope - NOT FOUND
						// lookup x in parent scope (global) - FOUND
            // set it to 2

		console.log(x + y); // 2 + 2
												// lookup x in foo scope - NOT FOUND
                        // so lookup x in parent scope (window) - FOUND - has value 2 not 1.
                        // lookup y in foo scope - FOUND - has value 2
                        // add 2 + 2

console.log(x + y); // ReferenceError: y is not defined
// why ReferenceError?
// because current scope is global and it has no reference for y
```


## Scopes are resolved at Static Time

In JavaScript, scopes are created before executing any statement in the code. i.e. Scoping happens during time when it tokenizes the code.
It is always based on where variables are authored, not based on the execution.

Let's consider the following example where the function `foo` called from the function `bar` which has the variable `x` in its scope.

```js
function foo() {
	console.log(x)
}
function bar() {
	var x = 10;
	foo();
}
bar();
```

Though the caller function `bar` has `x` in its scope, It throws `ReferenceError` for `x` inside the function `foo`.  
But Why? As scopes are nested, it should have accessed it from its parent function.  
By looking at the Lexical scope, lookup for `x` fails as its not found in the Static Scope in context of `foo`.

This is how the Scopes are resolved.

```
// Scope
window
	|- foo => #function ref
  |- bar => #function ref
			|- x
```

## Block Scope

ECMAScript 2015 specification introduced Scoping at block level.
A block is simply the area between the braces `{` and `}`.

## let, const

Both are for variable declaration just like `var`. The difference is that any variable declated with `var` is function scoped where as with `let, const` it is block scoped.

```js
let x = 1;
if (true) {
	let y = 2;
  console.log(x + y);
}
console.log(x + y);
```

Unlike `var`, variables declared with `let` are scoped to the block.

```
// Scope
window
	|- x
  |- {} // if block
		 |- y
     
// Execution
x = 1;
if (true) {
	y = 2;
  console.log(x + y); // 1 + 2
}
console.log(x + y); // neither 1 + 2 not 1 + undefined
// it's a ReferenceError: y is not defined
// as y not available in the scope in the context of window.
```

## Are Let and Const declarations hoisted?

**Yes, They are hoisted.**

Variables declared as `let` or `const` can't be accessed before their declaration.

```js
x = 1;	// allowed
var x;

y = 2;	// ReferenceError: y is not defined
let y;
```

Note: A ReferenceError is thrown when reference isn't available in the scope during lookup.

Which means they are not hoisted. Isn't it?

It appears that let (and const) declarations are not being hoisted, since `y` does not appear to exist before its declaration.  
That is not the case, they are hoisted just like any var or function, but there is a period between entering scope and being declared where they cannot be accessed. It is called **Temporal Dead Zone**.

```js
let y = 1;
{ // block
	// TDZ starts for y
	console.log(y); // throws ReferenceError: y is not defined
  let y = 2; // TDZ ends for y
}
```

With this example we could understand that if `y` is not hoisted, it should have resolved to value `1` from the upper scope, which becomes a bug.

It throws Reference error because `y` stays in **TDZ** before its declaration and it cannot be accessed.

In short, Let and Const are hoisted and TDZ helps avoiding unintentional bugs by preventting usage before declarations.

Similarly all the new ECMA2015+ definitions such as **Class**, **Generator** are also hoisted but can't be accessed before declarations as they stay in **TDZ**.


