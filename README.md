# Javascript Behind the Scenes Cheatsheet

This document outlines behind the scenes workflow of Javascript with brief informations and side notes. It's not intended to guide the way you code. There are several cheatsheets available out there; I also forked some of them you may consider.

Eventhough this document doesn't have detailed information, I encourage you to cast an eye on it so you can deep dive any concept you wonder.

### _If you don't know an information exists, you can't learn that._

### **Table Of Contents**

- [JS Runtime](#js-runtime)
  - [JS Engine](#js-engine)
  - [Execution Context](#execution-context)
  - [Closure](#closure)
  - [Primitive & Reference Types](#primitive-and-reference-types)
    - [Pass By Reference vs Value](#pass-by-reference-vs-value)
    - [Numbers](#Numbers)
- [Web API](#web-api)
  - [Event Propagation](#event-propagation)
- [Prototypes and ES6 Class](#prototypes-and-es6-class)
- [Event Loop and Asynchronous JavaScript](#event-loop-and-asynchronous-javascript)
- [Credits](#credits)

---

# JS Runtime

_Includes all the pieces necessary to execute JavaScript code_

- JS Engine (Heap + Call Stack)
- Callback Queue (click, timer... - data structure that holds all the ready to be executed callback functions.)
- **If browser:** Web APIs (DOM, Fetch API...) ; **else:** C++ bindings & thread pool

## JS Engine

_Heart of runtime_\
**Call Stack:** Place where execution contexts get stacked on top of each other to keep track of the order of execution. Last-in-first-out.\
**Heap:** Place where objects are stored.

**Compilation:** Entire source code converted into machine code at once. Compiled ahead of time(before execution).\
**Interpretation** _(The way jse used to work)_: Interpreter runs through entire code and executes it line by line.\
**Just-in-time (JIT) Compilation** _(The way modern jse works)_: Compilation during execution(at runtime).

## Execution Context

Only one global execution context.\
A new execution context is created for each function call.

#### Includes:

- Variable Env (declarations, function, arguments)
- Scope chain: Includes references to variables which are located outside of function.
- **this** refers to;\
  Method: owner object.\
  Function: global object(undefined if strict mode)\
  Arrow func: this of surrending function (lexical this)\
  Event listener: DOM element that the handler is attached to.

_Arrow functions don't have the arguments object and 'this' keyword; instead they use the closest regular func parent._\
On regular functions, value of this depends on how the function is invoked.\
No matter how or where being executed, this value inside of an arrow function always equals this value from the outer function.

## Closure

Closure gives acces to an outer function's scope from an inner function.\
A function always has access to the variables and scope of the outer function; even if the outer function finishes executing./
Eventhough the execution context has gone, variable environment lives in heap through closure.

**_A closure makes sure that a function preserves connection to variables which are existed at the place where you define function._**

## Primitive and Reference Types

**Primitives** _(stored in call stack)_: Number, String, Undefined etc..\
**(Reference Types) Objects** _(storec in heap)_: Object literal, arrays, functs etc..

```
let age = 30
let oldAge = age
age = 32
console.log(oldAge) // age: 30
console.log(age) // age: 32
```

```
const me = { name: 'Ethan', age: 25 }
const friend = me
friend.age = 23
console.log(friend) // age: 23
console.log(me) // age: 23
```

Here, both 'friend' and 'me' references to same memory address in heap. But integers are stored in call stack, and they have different addresses.

### Pass By Reference vs Value

When a reference type is passed to a function, a reference to the object in the memory heap is copied. \
Passing a primitive type to a function is same as _creating another_ argument with corresponding value.\
Passing an object is just like _copying_ an object. Whatever you change in copy, will also happen in the original.

### Numbers

`console.log(23 === 23.0) //true`\
There is only one data type for all numbers and they are represented internally in a 64 base 2 format(binary).\
`console.log(0.1 + 0.2 === 0.3) //false`\
eg. **In Base 10:** 1/10 = 0.1 ; 3/10 = 3.3333(to infinity)\
Similar thing applies in binary format of **0.1** so that's why expression above results to **false**\

## Web API

DOM is an interface for interacting browser through JS.

**EventTarget:** Allows nodes to listen events (addEventListener). An abstract parent, unavailable to interact.\
**Node:** Element, Text, Document\
**Document:** A type of node that contains methods that are used to interact.\
**Window:** Global object.

### Event Propagation

Defines how events propagate or travel through the DOM tree.

**Capturing Phase:** The event is first captured by the outermost element and propagated to the inner elements.

**Bubbling Phase:** The event is first captured and handled by the innermost element and then propagated to outer elements.

Most of the time, it's fine handling events in bubbling phase but it can also be necessary to fire them earlier.

_friendly suggestion; see Event Delagation for a useful benefit_

## Prototypes and ES6 Class

For these subjects, there are explanations on [Programming Paradigms Cheatsheet - JavaScript](https://google.com) along with the object oriented programming in general.

## Event Loop and Asynchronous JavaScript

_Essential piece that makes asynchronous behaviour possible. Maestro of the JS Runtime._\
This provides nonblocking concurrency model.\
Sends callbacks (eg. onClick) from callback queue to call stack, then they can get executed.

**Event Loop Tick:** Each time the event loop takes a callback from queue.

**Concurrency Model:** The way of JavaScript handles multiple tasks happenning at the same time.

There is only one thread of execution in JavaScript. So there is **no multitasking**. It can only do one thing at a time. Any asynchronous execution does not happen in the engine, it's the runtime that manages it and the event loop decides which code to be executed next.

If the operation was synchronous, it would be done in the call stack by blocking the execution of the rest of the code. So this is not in the main thread of execution, but in the web APIs (if browser) environment.

```
el.src = 'dog.jpg' // yes img src set is an async event.
el.addEventListener('load', xyz)
```

The time when fetch operation is done in Web APIs env, callback for that particular event is put into callback queue.\
Web APIs environment, the callback queue and the event loop; all together make it possitble that asynchronous code can be executed in a non blocking way even with only one thread of execution in the engine.

> Callbacks related to **promises** are sent into **microtasks queue** (has priority on callback queue).

### Async Functions

Async & Await (another way to consume promise)\
Async function performs code in the _background_. Not blocking the main thread of execution and callstack. Then it automatically returns promise.\
Inside an async function, 'await' can be used. Await will stop execution until promise if fulfilled.

```
const getCountry = async function(country){
  await fetch(url)
}
```

# Credits

I bring these informations together with the help of software community on internet. I do not claim that each sentence and analogy in this document belongs to me.

## I pursue giving what I get.
