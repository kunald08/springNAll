# JavaScript — From Basics to Expert

---

## 1. Introduction to JavaScript

### What is JavaScript?

JavaScript is a **high-level, interpreted, dynamically-typed** programming language. It's the language of the web — the only language that runs natively in browsers.

### Under the Hood: How JavaScript Executes

```
┌──────────────────────────────────────────────────────┐
│                 JavaScript Engine                    │
│              (V8 in Chrome/Node.js)                  │
│                                                      │
│  ┌─────────────────┐   ┌───────────────────────────┐ │
│  │    Call Stack   │   │       Memory Heap         │ │
│  │  (Execution)    │   │  (Object/Variable storage)│ │
│  │                 │   │                           │ │
│  │  ┌───────────┐  │   │   ┌──────┐  ┌──────┐      │ |
│  │  │ function()│  │   │   │ obj1 │  │ obj2 │      │ |
│  │  ├───────────┤  │   │   └──────┘  └──────┘      │ |
│  │  │ function()│  │   │                           │ │
│  │  ├───────────┤  │   │                           │ │
│  │  │  global() │  │   │                           │ │
│  │  └───────────┘  │   └───────────────────────────┘ │
│  └─────────────────┘                                 │
│                                                      │
│  ┌───────────────────────────────────────────────┐   │
│  │              Web APIs / Event Loop            │   │
│  │  setTimeout, fetch, DOM events, etc.          │   │
│  │                                               │   │
│  │  Callback Queue ← Event Loop → Call Stack     │   │
│  └───────────────────────────────────────────────┘   │
└──────────────────────────────────────────────────────┘

JavaScript is SINGLE-THREADED — only one thing executes at a time.
Asynchronous operations (setTimeout, fetch, etc.) are handled by Web APIs,
and their callbacks are queued and processed by the Event Loop.
```

---

## 2. Variables: `var`, `let`, `const`

Variables are **named containers** that store data. In JavaScript, you have three ways to create variables: `var` (the old way, avoid it), `let` (for values that will change), and `const` (for values that won't change). The rule of thumb is: **use `const` by default**, and only switch to `let` when you know the value needs to change later. Never use `var` — it has tricky scoping behavior that leads to bugs.

The big difference between them is **scoping**: `let` and `const` are **block-scoped** (they only exist inside the `{ }` block where they're defined), while `var` is **function-scoped** (it leaks out of blocks like `if` and `for`, which causes unexpected behavior).

```javascript
// ─── var (ES5 — avoid in modern code) ───
var name = "Kunal";
var name = "John";    // Re-declaration allowed (bug-prone!)
name = "Alex";        // Re-assignment allowed

// var is FUNCTION-scoped (not block-scoped!)
if (true) {
    var x = 10;
}
console.log(x);       // 10 — var leaked out of the block!

// ─── let (ES6 — use for mutable variables) ───
let age = 25;
// let age = 30;      // ❌ Error: Cannot re-declare
age = 30;             // ✅ Re-assignment allowed

// let is BLOCK-scoped
if (true) {
    let y = 20;
}
// console.log(y);    // ❌ Error: y is not defined

// ─── const (ES6 — use by default) ───
const PI = 3.14159;
// PI = 3.0;          // ❌ Error: Assignment to constant variable

// const with objects — reference is constant, properties are NOT
const user = { name: "Kunal", age: 25 };
user.age = 26;           // ✅ Works — modifying property
// user = {};            // ❌ Error — can't reassign reference

const colors = ["red", "green"];
colors.push("blue");     // ✅ Works — modifying array content
// colors = [];          // ❌ Error — can't reassign
```

### Under the Hood: Hoisting

```javascript
// JavaScript moves declarations (not assignments) to the top of their scope

// What you write:
console.log(a);     // undefined (var is hoisted with value = undefined)
var a = 5;

console.log(b);     // ❌ ReferenceError (let is hoisted but NOT initialized)
let b = 10;         // "Temporal Dead Zone" — can't use before declaration

// What the engine sees:
var a;              // Declaration hoisted
console.log(a);     // undefined
a = 5;              // Assignment stays in place

// Functions are FULLY hoisted
greet();            // Works!
function greet() {
    console.log("Hello");
}

// Function expressions are NOT fully hoisted
// sayHi();         // ❌ Error: sayHi is not a function
var sayHi = function() {
    console.log("Hi");
};
```

### Best Practice

```javascript
// ✅ Use const by default
const MAX_SIZE = 100;
const user = { name: "Kunal" };
const items = [1, 2, 3];

// ✅ Use let only when you NEED to reassign
let counter = 0;
counter++;

// ❌ Never use var (use let/const instead)
```

---

## 3. Data Types

JavaScript has two categories of data types:

**Primitive types** are simple, single values stored directly in memory. There are 7 of them: `string` (text), `number` (integers and decimals share the same type), `boolean` (true/false), `null` (intentionally empty), `undefined` (declared but not assigned), `symbol` (unique identifiers), and `bigint` (very large numbers). When you copy a primitive, you get an independent copy — changing one doesn't affect the other.

**Reference types** are complex values like objects, arrays, and functions. They are stored in memory and your variable holds a **reference** (pointer) to that memory location. When you copy a reference type, both variables point to the **same data** — changing one affects the other.

Understanding the difference between **primitive (by value)** and **reference (by pointer)** is crucial for avoiding bugs when working with objects and arrays.

```javascript
// ─── Primitive Types (immutable, stored by VALUE) ───
let str = "Hello";          // String
let num = 42;               // Number (integer and float share same type)
let big = 9007199254740991n; // BigInt (for very large integers)
let bool = true;            // Boolean
let undef = undefined;      // Undefined (declared but not assigned)
let nul = null;             // Null (intentional "no value")
let sym = Symbol("id");     // Symbol (unique identifier)

// ─── Reference Types (stored by REFERENCE) ───
let obj = { name: "Kunal" };  // Object
let arr = [1, 2, 3];          // Array (is actually an Object)
let func = function() {};      // Function (is actually an Object)
let date = new Date();         // Date object
let regex = /pattern/g;        // RegExp

// typeof operator
typeof "hello"      // "string"
typeof 42           // "number"
typeof true         // "boolean"
typeof undefined    // "undefined"
typeof null         // "object"  ← Known bug in JS (since 1995)
typeof {}           // "object"
typeof []           // "object"  ← Use Array.isArray() instead
typeof function(){} // "function"

// Type conversion
String(42)          // "42"
Number("42")        // 42
Number("hello")     // NaN (Not a Number)
Boolean(0)          // false
Boolean("")         // false
Boolean(null)       // false
Boolean(undefined)  // false
Boolean("hello")    // true (any non-empty string is truthy)
Boolean(42)         // true (any non-zero number is truthy)

// Falsy values: false, 0, -0, "", null, undefined, NaN
// Everything else is truthy (including empty arrays [] and objects {})
```

---

## 4. JavaScript Operators

Operators are symbols that perform operations on values. JavaScript has: **arithmetic operators** (`+`, `-`, `*`, `/`, `%` for remainder, `**` for power), **comparison operators** (for checking if values are equal, greater, or less), **logical operators** (`&&` for AND, `||` for OR, `!` for NOT), and more.

The most critical thing to know is the difference between `==` (loose equality) and `===` (strict equality). Loose equality does **type coercion** — it converts types before comparing, so `5 == "5"` is `true` (which is usually a bug). Strict equality compares both value AND type, so `5 === "5"` is `false`. **Always use `===` and `!==`** to avoid subtle bugs.

Other important operators include the **spread operator** (`...`) for copying/merging arrays and objects, **destructuring** for pulling values out of arrays/objects into variables, **optional chaining** (`?.`) for safely accessing nested properties, and the **ternary operator** (`condition ? a : b`) for quick inline decisions.

```javascript
// ─── Arithmetic ───
5 + 3       // 8
10 - 4      // 6
3 * 7       // 21
15 / 4      // 3.75
15 % 4      // 3 (modulus — remainder)
2 ** 3      // 8 (exponentiation)

// ─── Assignment ───
let x = 10;
x += 5;     // x = x + 5 → 15
x -= 3;     // x = x - 3 → 12
x *= 2;     // x = x * 2 → 24
x /= 4;     // x = x / 4 → 6
x %= 4;     // x = x % 4 → 2
x++;        // Post-increment: use x, then add 1
++x;        // Pre-increment: add 1, then use x

// ─── Comparison ───
5 == "5"    // true  (loose equality — type coercion!)
5 === "5"   // false (strict equality — no coercion) ✅ ALWAYS USE THIS
5 != "5"    // false
5 !== "5"   // true  ✅ ALWAYS USE THIS
5 > 3       // true
5 >= 5      // true
5 < 10      // true

// ─── Logical ───
true && false   // false (AND — both must be true)
true || false   // true  (OR — at least one must be true)
!true           // false (NOT — invert)

// Short-circuit evaluation
const name = user && user.name;           // If user exists, get name
const value = input || "default";          // Use input or fallback
const result = value ?? "default";         // Nullish coalescing (only null/undefined trigger fallback)

// ─── Ternary ───
const status = age >= 18 ? "adult" : "minor";

// ─── Spread & Rest ───
const arr1 = [1, 2, 3];
const arr2 = [...arr1, 4, 5];    // Spread: [1, 2, 3, 4, 5]

const obj1 = { a: 1, b: 2 };
const obj2 = { ...obj1, c: 3 };  // { a: 1, b: 2, c: 3 }

function sum(...numbers) {        // Rest: collect arguments into array
    return numbers.reduce((a, b) => a + b, 0);
}
sum(1, 2, 3, 4);                 // 10

// ─── Destructuring ───
const [a, b, c] = [1, 2, 3];
const { name: userName, age: userAge } = { name: "Kunal", age: 25 };

// Optional chaining
const city = user?.address?.city;        // undefined if any part is null/undefined
const first = arr?.[0];                  // Safe array access
const result2 = obj?.method?.();          // Safe method call
```

---

## 5. The `this` Keyword

`this` is one of the most confusing parts of JavaScript. It refers to the **object that is currently running the code**, but what it actually points to depends on **how** the function is called:

- In a **regular function**, `this` refers to the object that called the function (or `window`/`undefined` if called directly)
- In an **object method**, `this` refers to the object containing the method
- In an **arrow function**, `this` does NOT have its own value — it inherits `this` from the surrounding code where the arrow function was written
- You can **manually set** `this` using `.call()`, `.apply()`, or `.bind()`

The reason arrow functions don't have their own `this` is actually a feature, not a bug — it makes them ideal for callbacks and event handlers where you want to keep the parent's `this` value.

```javascript
// 'this' refers to the object that is currently executing the code

// ─── In global scope ───
console.log(this);    // Window (browser) or global (Node.js)

// ─── In an object method ───
const person = {
    name: "Kunal",
    greet() {
        console.log(this.name);    // "Kunal" — this = person object
    }
};

// ─── In a regular function ───
function show() {
    console.log(this);    // Window (non-strict) / undefined (strict mode)
}

// ─── In an arrow function ───
const obj = {
    name: "Kunal",
    greet: () => {
        console.log(this.name);    // undefined! Arrow functions DON'T have their own 'this'
        // They inherit 'this' from the enclosing lexical scope
    },
    greetCorrect() {
        const inner = () => {
            console.log(this.name);  // "Kunal" — arrow inherits from greetCorrect's 'this'
        };
        inner();
    }
};

// ─── Explicit binding ───
function greet(greeting) {
    console.log(`${greeting}, ${this.name}`);
}

const user = { name: "Kunal" };

greet.call(user, "Hello");       // "Hello, Kunal" — call with args
greet.apply(user, ["Hello"]);    // "Hello, Kunal" — apply with array of args
const boundGreet = greet.bind(user);  // Returns new function with 'this' bound
boundGreet("Hi");                 // "Hi, Kunal"

// ─── In a class ───
class Animal {
    constructor(name) {
        this.name = name;    // this = the new instance
    }
    speak() {
        console.log(`${this.name} speaks`);
    }
}
```

---

## 6. JavaScript Arrays

An array is an **ordered list of values**, accessed by their position (index). Arrays start counting at 0, so the first element is at index `[0]`. Unlike Java, JavaScript arrays can hold **mixed types** — you can mix numbers, strings, objects, and even other arrays in the same array. Arrays are one of the most-used data structures in JavaScript — you'll work with them constantly when dealing with lists of users, products, search results, database rows, API responses, etc.

```javascript
// Creating arrays
const arr = [1, 2, 3, 4, 5];
const arr2 = new Array(5);         // [empty × 5]
const arr3 = Array.from("hello");  // ['h', 'e', 'l', 'l', 'o']
const arr4 = Array.of(1, 2, 3);    // [1, 2, 3]

// Access
arr[0]           // 1 (first element)
arr[arr.length - 1]  // 5 (last element)
arr.at(-1)       // 5 (last element — modern way)

// Check
Array.isArray(arr)     // true
arr.includes(3)        // true
arr.indexOf(3)         // 2
arr.lastIndexOf(3)     // 2
```

---

## 7. Array Methods (Essential)

Array methods are the functions you call on arrays to manipulate them. They're divided into two categories:

**Mutating methods** change the original array itself (like `push`, `pop`, `sort`, `reverse`). After calling them, your original array is different.

**Non-mutating methods** leave the original array unchanged and return a new result (like `map`, `filter`, `slice`, `concat`). This is important for avoiding side effects.

The **higher-order methods** (`map`, `filter`, `reduce`, `find`, `forEach`, `some`, `every`) are the most important ones to master. They take a function as an argument and apply it to each element. These let you write clean, readable code for common tasks like transforming data, filtering lists, calculating totals, and searching. Modern JavaScript code uses these methods extensively instead of traditional `for` loops.

### Mutating Methods (change original array)

```javascript
const arr = [1, 2, 3];

arr.push(4);           // Add to end → [1, 2, 3, 4], returns new length
arr.pop();             // Remove from end → [1, 2, 3], returns removed element
arr.unshift(0);        // Add to beginning → [0, 1, 2, 3]
arr.shift();           // Remove from beginning → [1, 2, 3]
arr.splice(1, 1);      // Remove 1 element at index 1 → [1, 3]
arr.splice(1, 0, 2);   // Insert 2 at index 1 → [1, 2, 3]
arr.reverse();         // Reverse in place → [3, 2, 1]
arr.sort();            // Sort in place (alphabetically by default)
arr.sort((a, b) => a - b);  // Sort numerically ascending
arr.sort((a, b) => b - a);  // Sort numerically descending
arr.fill(0);           // Fill all with 0 → [0, 0, 0]
```

### Non-Mutating Methods (return new array/value)

```javascript
const arr = [1, 2, 3, 4, 5];

arr.concat([6, 7]);           // [1, 2, 3, 4, 5, 6, 7]
arr.slice(1, 3);              // [2, 3] (start inclusive, end exclusive)
arr.join(", ");               // "1, 2, 3, 4, 5"
arr.flat();                   // Flatten nested arrays: [[1,2],[3,4]] → [1,2,3,4]
arr.toString();               // "1,2,3,4,5"
```

### Higher-Order Array Methods (Most Important!)

A Higher-Order Function is a function that takes another function as an argument or returns a function. These methods allow you to work with arrays in a functional programming style, making your code more concise and expressive. 

```javascript
const numbers = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];

// ─── forEach: Execute function for each element (no return value) ───
numbers.forEach((num, index) => {
    console.log(`Index ${index}: ${num}`);
});

// ─── map: Transform each element → returns NEW array ───
const doubled = numbers.map(num => num * 2);
// [2, 4, 6, 8, 10, 12, 14, 16, 18, 20]

const users = [
    { name: "Kunal", age: 25 },
    { name: "Priya", age: 30 }
];
const names = users.map(user => user.name);
// ["Kunal", "Priya"]

// ─── filter: Keep elements that pass a test → returns NEW array ───
const evens = numbers.filter(num => num % 2 === 0);
// [2, 4, 6, 8, 10]

const adults = users.filter(user => user.age >= 18);

// ─── reduce: Accumulate all elements into a single value ───
const sum = numbers.reduce((accumulator, current) => {
    return accumulator + current;
}, 0);  // 0 is the initial value
// 55

// Max value
const max = numbers.reduce((max, num) => num > max ? num : max, numbers[0]);
// 10

// Group by
const people = [
    { name: "A", dept: "Engineering" },
    { name: "B", dept: "Design" },
    { name: "C", dept: "Engineering" },
];
const grouped = people.reduce((acc, person) => {
    const key = person.dept;
    acc[key] = acc[key] || [];
    acc[key].push(person);
    return acc;
}, {});
// { Engineering: [{...}, {...}], Design: [{...}] }

// ─── find: Return FIRST element that passes test ───
const found = numbers.find(num => num > 5);    // 6

// ─── findIndex: Return INDEX of first match ───
const idx = numbers.findIndex(num => num > 5); // 5

// ─── some: At least ONE element passes test → boolean ───
numbers.some(num => num > 8);    // true

// ─── every: ALL elements pass test → boolean ───
numbers.every(num => num > 0);   // true

// ─── Chaining methods ───
const result = numbers
    .filter(n => n % 2 === 0)       // [2, 4, 6, 8, 10]
    .map(n => n * 3)                // [6, 12, 18, 24, 30]
    .reduce((sum, n) => sum + n, 0); // 90
```

---

## 8. Objects

Objects are collections of **key-value pairs** — think of them like a dictionary where each word (key) has a definition (value). They are the fundamental building block for organizing data in JavaScript. Almost everything in JavaScript is an object under the hood — arrays, functions, dates, and even errors are all objects.

Objects are used to represent real-world things: a user has a `name`, `email`, and `age`; a product has a `title`, `price`, and `description`. You can nest objects inside objects (a user's `address` can be its own object with `city`, `state`, `zip`), creating complex data structures.

**Destructuring** is a shorthand for extracting values from objects into individual variables. Instead of writing `const name = user.name; const age = user.age;`, you write `const { name, age } = user;` — much cleaner.

```javascript
// ─── Object Creation ───
// Literal (most common)
const user = {
    name: "Kunal",
    age: 25,
    email: "kunal@example.com",
    address: {
        city: "Mumbai",
        country: "India"
    },
    skills: ["Java", "JavaScript"],
    greet() {
        return `Hi, I'm ${this.name}`;
    }
};

// ─── Accessing Properties ───
user.name                    // "Kunal" (dot notation)
user["name"]                 // "Kunal" (bracket notation)
user.address.city            // "Mumbai"
user?.address?.zip           // undefined (safe access)

// ─── Modifying ───
user.age = 26;               // Update
user.phone = "1234567890";   // Add new property
delete user.email;           // Delete property

// ─── Object Methods ───
Object.keys(user)            // ["name", "age", "address", "skills", "greet"]
Object.values(user)          // ["Kunal", 26, {...}, [...], ƒ]
Object.entries(user)         // [["name","Kunal"], ["age",26], ...]
Object.assign({}, user)      // Shallow copy
{ ...user }                  // Shallow copy (spread)

// Check property existence
"name" in user               // true
user.hasOwnProperty("name")  // true

// Freeze/Seal
Object.freeze(user);         // Can't add, remove, or modify properties
Object.seal(user);           // Can't add or remove, but CAN modify existing

// ─── Iteration ───
for (const key in user) {
    console.log(`${key}: ${user[key]}`);
}

for (const [key, value] of Object.entries(user)) {
    console.log(`${key}: ${value}`);
}

// ─── Destructuring ───
const { name, age, address: { city } } = user;
console.log(name, age, city);  // "Kunal" 26 "Mumbai"

// With renaming and defaults
const { name: userName, phone = "N/A" } = user;

// ─── Computed Property Names ───
const key = "color";
const obj = {
    [key]: "blue",                    // { color: "blue" }
    [`${key}Code`]: "#0000FF"         // { colorCode: "#0000FF" }
};
```

---

## 9. DOM Manipulation

DOM (Document Object Model) is a tree-like structure representing the HTML document. JavaScript can interact with the DOM to dynamically change content, styles, and structure of web pages. 
and respond to user interactions. 


### Selecting Elements

```javascript
// By ID
const header = document.getElementById("header");

// By class name (returns HTMLCollection — live)
const cards = document.getElementsByClassName("card");

// By tag name (returns HTMLCollection — live)
const paragraphs = document.getElementsByTagName("p");

// Query selector (returns FIRST match)
const firstCard = document.querySelector(".card");
const nav = document.querySelector("#nav > ul");

// Query selector all (returns NodeList — static)
const allCards = document.querySelectorAll(".card");
const links = document.querySelectorAll("a[href^='https']");

// Traversing
const parent = element.parentElement;
const children = element.children;
const firstChild = element.firstElementChild;
const lastChild = element.lastElementChild;
const nextSibling = element.nextElementSibling;
const prevSibling = element.previousElementSibling;
const closest = element.closest(".container");  // Find nearest ancestor matching selector
```

### Modifying Content

```javascript
// Text content
element.textContent = "New text";          // Plain text (safer)
element.innerText = "New text";            // Visible text only
element.innerHTML = "<strong>Bold</strong>"; // HTML (⚠️ XSS risk)

// Attributes
element.getAttribute("href");
element.setAttribute("href", "https://example.com");
element.removeAttribute("disabled");
element.hasAttribute("disabled");

// Data attributes
// <div data-user-id="42" data-role="admin">
element.dataset.userId;        // "42"
element.dataset.role;          // "admin"
element.dataset.newProp = "value"; // Set data-new-prop

// Classes
element.classList.add("active");
element.classList.remove("hidden");
element.classList.toggle("dark-mode");
element.classList.contains("active");       // true/false
element.classList.replace("old", "new");

// Styles
element.style.color = "red";
element.style.backgroundColor = "#f0f0f0";  // camelCase!
element.style.cssText = "color: red; font-size: 16px;";

// Get computed style (actual rendered style)
const computed = getComputedStyle(element);
computed.color;    // "rgb(255, 0, 0)"

// Creating and adding elements
const div = document.createElement("div");
div.textContent = "Hello";
div.classList.add("card");
document.body.appendChild(div);                  // Add as last child
parent.insertBefore(newElement, referenceElement); // Insert before
parent.removeChild(child);                        // Remove
element.remove();                                 // Remove self

// Modern insertion
parent.prepend(element);                // First child
parent.append(element);                 // Last child
element.before(newElement);             // Before element
element.after(newElement);              // After element
element.replaceWith(newElement);        // Replace
```

---

## 10. Event Handling

Event Handling is the mechanism by which JavaScript responds to user interactions (like clicks, keyboard input) and other events (like timers, network responses). When an event occurs, JavaScript can execute a function (called an event handler or callback) to perform some action in response.

So the event loop is what allows JavaScript to handle asynchronous events (like user interactions, network responses, timers) without blocking the main thread. When an event occurs, its callback function is added to the callback queue. The event loop continuously checks if the call stack is empty and, if so, it takes the first callback from the queue and pushes it onto the call stack for execution. This way, JavaScript can respond to events while still executing other code.

```javascript
// ─── Adding Events ───
// Method 1: addEventListener (recommended)
button.addEventListener("click", function(event) {
    console.log("Clicked!", event);
});

// Method 2: Arrow function
button.addEventListener("click", (e) => {
    console.log("Clicked!", e.target);
});

// Method 3: Named function (can be removed)
function handleClick(e) {
    console.log("Clicked!");
}
button.addEventListener("click", handleClick);
button.removeEventListener("click", handleClick);

// ─── Common Events ───
// Mouse: click, dblclick, mouseenter, mouseleave, mousemove, mousedown, mouseup
// Keyboard: keydown, keyup, keypress (deprecated)
// Form: submit, change, input, focus, blur
// Window: load, resize, scroll, DOMContentLoaded
// Touch: touchstart, touchmove, touchend

// ─── Event Object ───
element.addEventListener("click", (e) => {
    e.target;              // Element that triggered the event
    e.currentTarget;       // Element the listener is attached to
    e.type;                // "click"
    e.clientX;             // Mouse X position (viewport)
    e.clientY;             // Mouse Y position (viewport)
    e.preventDefault();    // Prevent default behavior (e.g., form submit)
    e.stopPropagation();   // Stop event from bubbling up
});

// Keyboard events
document.addEventListener("keydown", (e) => {
    console.log(e.key);       // "Enter", "a", "ArrowUp"
    console.log(e.code);      // "Enter", "KeyA", "ArrowUp"
    console.log(e.ctrlKey);   // true if Ctrl was held
    console.log(e.shiftKey);  // true if Shift was held
});

// ─── Event Delegation (performance optimization) ───
// Instead of adding listeners to each item, add ONE to the parent
document.getElementById("list").addEventListener("click", (e) => {
    if (e.target.tagName === "LI") {
        console.log("Clicked item:", e.target.textContent);
    }
});

// ─── Under the Hood: Event Propagation ───
// Events propagate in three phases:
// 1. Capturing phase: Window → Document → html → body → ... → target
// 2. Target phase: Event reaches the target element
// 3. Bubbling phase: target → ... → body → html → Document → Window

// By default, listeners fire in BUBBLING phase
// Use third argument to capture:
element.addEventListener("click", handler, true);  // Capture phase
element.addEventListener("click", handler, { capture: true, once: true }); // Options
```

---

## 11. Control Flow

Control flow determines **the order in which code runs**. By default, JavaScript executes code from top to bottom. But with **conditional statements** (`if/else`, `switch`) you can make decisions, and with **loops** (`for`, `while`, `do-while`, `for...of`, `for...in`) you can repeat actions. These are the basic building blocks of any program's logic.

`for...of` and `for...in` are special loops that deserve explanation: `for...of` iterates over **values** in iterable collections (arrays, strings), while `for...in` iterates over **keys** (property names) of objects. Don't mix them up — using `for...in` on an array gives you the indices as strings, not the values.

```javascript
// ─── if-else ───
if (score >= 90) {
    grade = "A";
} else if (score >= 80) {
    grade = "B";
} else {
    grade = "C";
}

// ─── switch ───
switch (day) {
    case "Monday":
    case "Tuesday":
    case "Wednesday":
    case "Thursday":
    case "Friday":
        console.log("Workday");
        break;
    case "Saturday":
    case "Sunday":
        console.log("Weekend");
        break;
    default:
        console.log("Invalid day");
}

// ─── Loops ───
// for loop
for (let i = 0; i < 10; i++) {
    console.log(i);
}

// for...of (iterate VALUES — arrays, strings, iterables)
for (const item of ["a", "b", "c"]) {
    console.log(item);    // "a", "b", "c"
}

// for...in (iterate KEYS — objects)
const obj = { a: 1, b: 2, c: 3 };
for (const key in obj) {
    console.log(`${key}: ${obj[key]}`);
}

// while
let count = 0;
while (count < 5) {
    console.log(count);
    count++;
}

// do-while (executes at least once)
do {
    console.log(count);
    count++;
} while (count < 5);

// break and continue
for (let i = 0; i < 10; i++) {
    if (i === 3) continue;    // Skip 3
    if (i === 7) break;       // Stop at 7
    console.log(i);           // 0, 1, 2, 4, 5, 6
}
```

---

## 12. Functions

Functions are reusable blocks of code that perform a specific task. They can take parameters, return values, and be assigned to variables or passed as arguments. Functions can be defined in several ways in JavaScript, each with its own characteristics and use cases. 


```javascript
// ─── Function Declaration (hoisted) ───
function add(a, b) {
    return a + b;
}

// ─── Function Expression (NOT hoisted) ───
const multiply = function(a, b) {
    return a * b;
};

// ─── Arrow Function (ES6) ───
const subtract = (a, b) => a - b;           // Implicit return
const square = x => x * x;                   // Single param: no parens needed
const greet = () => "Hello";                  // No params
const getUser = () => ({ name: "Kunal" });    // Return object: wrap in ()

// Multi-line arrow function
const calculate = (a, b, operation) => {
    if (operation === "add") return a + b;
    if (operation === "subtract") return a - b;
    return 0;
};

// ─── Default Parameters ───
function createUser(name, role = "user", active = true) {
    return { name, role, active };
}
createUser("Kunal");                  // { name: "Kunal", role: "user", active: true }
createUser("Admin", "admin");         // { name: "Admin", role: "admin", active: true }

// ─── Rest Parameters ───
function sum(...numbers) {
    return numbers.reduce((total, n) => total + n, 0);
}
sum(1, 2, 3, 4);    // 10

// ─── Arrow Functions vs Regular Functions ───
// Arrow functions:
//   - No own 'this' (inherits from parent scope)
//   - No 'arguments' object
//   - Cannot be used as constructors (no 'new')
//   - Cannot be used as methods in objects (this won't work)
//   - Shorter syntax
```

---

## 13. Asynchronous JavaScript

Asynchronous JavaScript allows you to perform tasks without blocking the main thread, enabling your application to remain responsive while waiting for operations like network requests, timers, or user interactions. JavaScript handles asynchronous operations using callbacks, Promises, and async/await syntax. A common example of asynchronous behavior is fetching data from an API, where you don't want to freeze the UI while waiting for the response. 

### Callbacks

```javascript
// A callback is a function passed as an argument to another function

function fetchData(url, callback) {
    setTimeout(() => {
        const data = { id: 1, name: "Kunal" };
        callback(null, data);
    }, 1000);
}

fetchData("/api/user", (error, data) => {
    if (error) {
        console.error("Error:", error);
    } else {
        console.log("Data:", data);
    }
});

// ⚠️ Callback Hell (Pyramid of Doom)
getUser(id, (user) => {
    getOrders(user.id, (orders) => {
        getDetails(orders[0].id, (details) => {
            getShipping(details.trackingId, (shipping) => {
                // Deeply nested — hard to read and maintain!
            });
        });
    });
});
```

### Promises

Promises are a modern way to handle asynchronous operations, providing a cleaner and more manageable syntax compared to callbacks. A Promise represents a value that may be available now, in the future, or never. It can be in one of three states: pending, fulfilled, or rejected. Promises allow you to chain asynchronous operations and handle errors more gracefully.

```javascript
// A Promise represents a future value (pending → fulfilled or rejected)

// Creating a Promise
const myPromise = new Promise((resolve, reject) => {
    const success = true;
    
    setTimeout(() => {
        if (success) {
            resolve({ id: 1, name: "Kunal" });   // Fulfilled
        } else {
            reject(new Error("Failed to fetch"));  // Rejected
        }
    }, 1000);
});

// Consuming a Promise
myPromise
    .then(data => {
        console.log("Success:", data);
        return data.id;                    // Can chain!
    })
    .then(id => {
        console.log("ID:", id);
    })
    .catch(error => {
        console.error("Error:", error.message);
    })
    .finally(() => {
        console.log("Done (always runs)");
    });

// Promise utility methods
Promise.all([p1, p2, p3])         // Wait for ALL to resolve (fails if ANY fails)
Promise.allSettled([p1, p2, p3])  // Wait for ALL to settle (never rejects)
Promise.race([p1, p2, p3])       // First one to settle (resolve or reject) wins
Promise.any([p1, p2, p3])        // First one to RESOLVE wins

// Example: Fetch multiple APIs in parallel
Promise.all([
    fetch("/api/users"),
    fetch("/api/orders"),
    fetch("/api/products")
])
.then(responses => Promise.all(responses.map(r => r.json())))
.then(([users, orders, products]) => {
    console.log(users, orders, products);
});
```

### Async/Await (Modern Way)

Async/await is syntactic sugar built on top of Promises, introduced in ES2017. It allows you to write asynchronous code that looks and behaves like synchronous code, making it easier to read and maintain. The `async` keyword is used to declare an asynchronous function, which returns a Promise. The `await` keyword can only be used inside async functions and pauses the execution until the Promise is resolved or rejected.
async/await also provides a more straightforward way to handle errors using try/catch blocks, improving the readability of error handling in asynchronous code.

```javascript
// async/await is syntactic sugar over Promises — makes async code look synchronous

async function fetchUser(id) {
    try {
        const response = await fetch(`/api/users/${id}`);
        
        if (!response.ok) {
            throw new Error(`HTTP error! Status: ${response.status}`);
        }
        
        const user = await response.json();
        console.log("User:", user);
        return user;
        
    } catch (error) {
        console.error("Failed to fetch user:", error.message);
        throw error;    // Re-throw if caller needs to handle
    }
}

// Call it
const user = await fetchUser(1);

// Parallel async operations
async function loadDashboard() {
    const [users, orders, stats] = await Promise.all([
        fetch("/api/users").then(r => r.json()),
        fetch("/api/orders").then(r => r.json()),
        fetch("/api/stats").then(r => r.json())
    ]);
    
    return { users, orders, stats };
}
```

### Fetch API

Fetch API is a modern interface for making HTTP requests in JavaScript. It returns Promises and provides a more powerful and flexible way to interact with servers compared to the older XMLHttpRequest. Fetch allows you to make GET, POST, PUT, DELETE, and other types of requests, and it supports features like headers, body content, and response handling.
And it also has built-in support for handling JSON data, making it easier to work with APIs that return JSON responses. The Fetch API is widely supported in modern browsers and is the recommended way to perform network requests in JavaScript.

```javascript
// ─── GET Request ───
const response = await fetch("https://api.example.com/users");
const data = await response.json();

// ─── POST Request ───
const newUser = await fetch("https://api.example.com/users", {
    method: "POST",
    headers: {
        "Content-Type": "application/json",
        "Authorization": "Bearer token123"
    },
    body: JSON.stringify({
        name: "Kunal",
        email: "kunal@example.com"
    })
});
const created = await newUser.json();

// ─── PUT Request ───
await fetch("https://api.example.com/users/1", {
    method: "PUT",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ name: "Updated Name" })
});

// ─── DELETE Request ───
await fetch("https://api.example.com/users/1", {
    method: "DELETE"
});

// ─── Error Handling ───
async function safeFetch(url) {
    try {
        const response = await fetch(url);
        if (!response.ok) {
            throw new Error(`HTTP ${response.status}: ${response.statusText}`);
        }
        return await response.json();
    } catch (error) {
        if (error.name === "TypeError") {
            console.error("Network error — are you online?");
        } else {
            console.error("Fetch error:", error.message);
        }
        return null;
    }
}
```

---

## 14. Closures, IIFE, and Scope

### Closures

A closure is when a function "remembers" the variables from the place where it was created, even after that place no longer exists. This is one of JavaScript's most powerful features, and it happens naturally — whenever you write a function inside another function, the inner function automatically has access to the outer function's variables.

Why does this matter? Closures let you create **private data** (variables that can only be accessed through specific functions — like a password-protected safe), **factory functions** (functions that create customized functions), and **stateful functions** (functions that remember things between calls, like a counter). Understanding closures is also essential for understanding how callbacks, event handlers, and module patterns work.

```javascript
// A closure is a function that remembers variables from its outer scope,
// even after the outer function has returned.

function createCounter() {
    let count = 0;                    // This variable is "enclosed"
    
    return {
        increment: () => ++count,
        decrement: () => --count,
        getCount: () => count
    };
}

const counter = createCounter();
counter.increment();    // 1
counter.increment();    // 2
counter.decrement();    // 1
counter.getCount();     // 1
// 'count' is private — can't be accessed directly!

// Under the Hood: Why Closures Work
// When createCounter() returns, its execution context is removed from the call stack.
// But the returned functions still hold a REFERENCE to the outer scope's variables.
// JavaScript's garbage collector keeps 'count' alive because it's still referenced.

// Practical example: Event handler with state
function createLogger(prefix) {
    return function(message) {
        console.log(`[${prefix}] ${message}`);
    };
}

const errorLog = createLogger("ERROR");
const infoLog = createLogger("INFO");

errorLog("Something went wrong");    // [ERROR] Something went wrong
infoLog("Server started");           // [INFO] Server started
```

### IIFE (Immediately Invoked Function Expression)
IIFE is a function that is defined and executed immediately. It creates a new scope, which helps to avoid polluting the global namespace and can be used to create private variables.

```javascript
// IIFE executes immediately and creates its own scope

(function() {
    const secret = "hidden";
    console.log("IIFE executed!");
    // Variables here don't pollute global scope
})();

// console.log(secret);  // ❌ Error: secret is not defined

// With parameters
(function(name) {
    console.log(`Hello, ${name}`);
})("Kunal");

// Arrow function IIFE
(() => {
    console.log("Arrow IIFE");
})();

// Module pattern (before ES6 modules)
const myModule = (function() {
    let private_var = 0;
    
    function privateMethod() {
        return ++private_var;
    }
    
    return {
        publicMethod: () => privateMethod(),
        getValue: () => private_var
    };
})();

myModule.publicMethod();    // 1
myModule.getValue();        // 1
```

---

## 15. Quick Reference

```
Variables:     const (default) → let (if reassigning) → never var
Types:         string, number, boolean, null, undefined, symbol, bigint, object
Comparison:    Always use === and !==
Arrays:        .map() .filter() .reduce() .find() .forEach()
Async:         async/await (modern) → Promises → Callbacks (legacy)
DOM:           querySelector/All → addEventListener → classList
Scope:         Block scope (let/const) → Function scope (var) → Global
this:          Regular function = caller | Arrow function = lexical parent
Closures:      Inner function remembers outer function's variables
```
