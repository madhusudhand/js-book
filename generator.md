# Generator

As the name says, a generator is something which generates data and on demand.
It is **pausable** function which can be iterated to generate one value at a time.

By syntax a generator is a function with `*`.

```js
// a simple generator function
function* genFunc() {
  return 'hello world!!';
}
```

Note: A generator can't be created with Arrow functions.

The generator can be called just like function. It takes arguments and it can be called in the context of other object.
However it can't be used called as constructor.

```js
// valid
genFunc();
getFunc(1, 2); // it can take params and `arguments` is available in the generator
genFunc.call(obj, 1, 2); // can be called in the context of another object.

// not valid
new genFunc(); // TypeError: genFunc is not a constructor
```

The difference is, unlike a function which returns value, 
generator returns an Iterable which can be iterated to fetch multiple values.

```js
const itr = genFunc(); // returns Iterator

const first = itr.next(); // get one value from the Iterator
console.log(first); // { value: 'hello world!!', done: true }
```

Iterator returns object with keys *value* and *done*. `done: true` indicates end of iteraton.
However calling `next` method later time doesn't throw error. Instead it returns object *{ value: undefined, done: true }*.

But wait. So far we are returning only one value. How do we return multiple values from the generator function? Obviously not multiple return statements.
It can be done with the help of `yield`.

## yield

With yield a generator produces multiple values. There can be as many yields in a generator which gives one vlaue at a time.
*yield* not a replacement for *return*. *yield* is used to produce values and *return* is used to terminate the generator.

Everytime it yields a value, it gets paused. When the next method is called, it will continue the execution from previous point until next yield.

```js
function* foo() {
    yield 'a';
    yield 'b';
    return 'c';
    
    yield 'd'; // unreachable
}

const itr = foo();

console.log(itr.next()); // { value: 'a', done: false }
console.log(itr.next()); // { value: 'b', done: false }
console.log(itr.next()); // { value: 'c', done: true }

// generator would never yield 'd' because it terminates before reaching the final yield statement.
```

Just like any function, a generator can take arguments and the `arguments` object is available for it.

```js
function* getQuestions(difficulty) {
	difficulty = difficulty || 'easy';
  const questions = {
		easy: [
			'What is 2x2 ?',
      'Where do you live?',
    ],
    difficult: [
			"Explain einstein's theory of relativity.",
      'Where do you live? (Tell us the coordinates)',
    ],
  };

  for(let q of questions[difficulty]) {
		yield q;
  }
}

const easy = getQuestions('easy'); // gets iterator of easy questions
const question1 = easy.next(); // gets the first easy question
```

## Iterating over a generator

Since the generator returns an Iterator object, it can directly be used with *for-of* loop and *spread operator*.

```js
for (let question of getQuestions('easy')) {
	console.log(question);
}

let difficultQuestions = [...getQuestions('difficult')];

let [q1, q2] = getQuestions('easy');
```

Note: The generator instance gets terminated either at the end of loop or even if it is conditionally breaking in between (or because of an exception).
It can't be resumed.

```js
const easyQ = getQuestions('easy');
for (let question of easyQ) {
	console.log(question);
  break; // breaks after first question
}

// easyQ can't be continued, though the loop isn't done with all the values.
for (let question of easyQ) {
	console.log(question); // this wouldn't be executed
}

// A fresh generator call to get new iterator
const easyQ2 = getQuestions('easy'); // returns new iterator
for (let question of easyQ2) {
	console.log(question); // logs all the questions
}
```

If not with a for loop, a generator can be completed at any time by calling a return method from outside.

```js
const easyQ = getQuestions('easy');
easyQ.next(); // returns first question

easyQ.return(); // { value: undefined, done: true }

// doesn't return second question anymore as its completed.
easyQ.next(); // { value: undefined, done: true }
```

## Sending values to the generator with next

So far we have created generator and used *next()* method to get the next value from the generator.
The generator can also receive values with next().

```js
function* greet() {
	const name = yield 'What is your name?';
  yield 'Hello ' + name;
}

const msg = greet();
msg.next(); // { value: 'What is your name?, done: false }
msg.next('Madhu'); // { value: 'Hello Madhu', done: false }
```

When a value is suplied to the generator via next method, the previous yield (the point where the generator paused) gets replaced with the given value and continues execution.
In this example the second next method is called with param, it replaces the previous yield thus the variable *name* will be the given string.

**Note:** There is nothing wrong in calling first next with value. However it doesn't makes any sense as there is no previous yield statement to replace. It would simply be ignored.

## yield another generator with yield*

Just like functions, generators can be called to yield within another generator with the help of the operator **yield***

Let us rewrite the above *getQuestions* Generator as multiple generators and call one within another.

```js
function* getEasyQuestions() {
	const questions = [
		'What is 2x2 ?',
		'Where do you live?',
	];
  
  for(let q of questions) {
		yield q;
  }
}

function* getDifficultyQuestions() {
	const questions = [
		"Explain einstein's theory of relativity.",
		'Where do you live? (Tell us the coordinates)',
	];
  
  for(let q of questions) {
		yield q;
  }
}

function* getQuestions(difficulty) {
	if (difficulty === 'difficult') {
		yield* getDifficultQuestions();
  } else {
		yield* getEasyQuestions();
  }
}

const q = getQuestions('easy');
q.next(); // gets fist question from the getEasyQuestions generator
```

**yield*** here acts like a loop to iterator over the given iterator (return value of a generator call) and returning one value at a time.
Hence it can also work with arrays or objects with [Symbol.iterator] definition.

```js
function* foo() {
	yield 1;
	yield* [2, 3];
	yield 4;
}

// this is same as
function* foo() {
	yield 1;
	for (let value of [1, 2]) {
		yield value;
	}
	yield 4;
}
```



