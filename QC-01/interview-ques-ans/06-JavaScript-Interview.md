# JavaScript — Interview Questions & Answers

> **How to use this:** Read each question, try to answer it yourself first, then check the answer. Answers are written exactly how you should speak in an interview — clear, confident, plain English.

---

## 1. What is JavaScript? How is it different from Java?

**Answer:**
JavaScript is a **lightweight, interpreted, dynamically-typed** programming language primarily used for making web pages interactive. It runs in the browser (client-side) and can also run on servers via **Node.js**.

Despite the name, JavaScript has **nothing to do with Java**. The name was a marketing decision in 1995. Key differences:
- Java is compiled, statically typed, class-based OOP. JavaScript is interpreted, dynamically typed, prototype-based.
- Java runs on the JVM. JavaScript runs in the browser's JS engine (V8 in Chrome, SpiderMonkey in Firefox).
- Java is used for backend, Android, enterprise. JavaScript is used for frontend, full-stack (with Node.js), and increasingly everywhere.

---

## 2. What are the differences between `var`, `let`, and `const`?

**Answer:**
- **`var`** — function-scoped, hoisted (initialized as `undefined`), can be re-declared. It's the old way, and I avoid it because it causes scoping bugs.

- **`let`** — block-scoped (respects `{}`), hoisted but NOT initialized (accessing before declaration throws a ReferenceError — this is the **Temporal Dead Zone**). Can be reassigned but not re-declared in the same scope.

- **`const`** — same as `let` but CANNOT be reassigned. For objects and arrays, the reference is constant, but the contents CAN be modified (`const arr = [1]; arr.push(2)` is fine).

My rule: **use `const` by default, `let` when I need to reassign, never use `var`**. This makes code predictable and reduces bugs.

---

## 3. What are the data types in JavaScript?

**Answer:**
JavaScript has **7 primitive types** and **1 reference type**:

**Primitives** (stored directly on the stack, immutable):
1. `string` — `"hello"`
2. `number` — `42`, `3.14` (no separate int/float)
3. `boolean` — `true` / `false`
4. `undefined` — variable declared but not assigned
5. `null` — intentional absence of value
6. `bigint` — for very large integers: `123n`
7. `symbol` — unique identifier

**Reference type** (stored on the heap, accessed by reference):
- `object` — includes plain objects, arrays, functions, dates, RegExp, etc.

I use `typeof` to check types: `typeof "hi"` → `"string"`. Note the quirk: `typeof null` returns `"object"` — that's a known bug in JavaScript since day one.

---

## 4. What is hoisting in JavaScript?

**Answer:**
Hoisting is JavaScript's behavior of moving **declarations** to the top of their scope during the compilation phase — before the code actually runs.

- **`var`** declarations are hoisted and initialized as `undefined`:
```javascript
console.log(x); // undefined (not an error)
var x = 5;
```

- **`let`/`const`** declarations are hoisted but NOT initialized — accessing them before the declaration throws a `ReferenceError`. The zone between the start of the scope and the declaration is called the **Temporal Dead Zone (TDZ)**.

- **Function declarations** are fully hoisted — I can call them before they're defined:
```javascript
greet(); // Works!
function greet() { console.log("Hi"); }
```

- **Function expressions** and **arrow functions** assigned to `let`/`const` are NOT hoisted.

---

## 5. What is the difference between `==` and `===`?

**Answer:**
- **`==` (loose equality)** — compares values after **type coercion**. JavaScript tries to convert both sides to the same type before comparing. `"5" == 5` is `true` because the string is converted to a number.

- **`===` (strict equality)** — compares values WITHOUT type conversion. Both the value AND the type must match. `"5" === 5` is `false`.

I **always use `===`** in my code. Loose equality has bizarre edge cases: `"" == 0` is `true`, `null == undefined` is `true`, `[] == false` is `true`. These are hard to predict and lead to bugs.

The same applies to `!=` vs `!==` — always use `!==`.

---

## 6. Explain the `this` keyword in JavaScript.

**Answer:**
`this` refers to the **object that is currently executing the code**. Its value changes depending on HOW a function is called — not where it's defined.

The rules, in order of priority:
1. **`new` keyword** — `this` is the newly created object.
2. **Explicit binding** — `call()`, `apply()`, `bind()` explicitly set `this`.
3. **Method call** — `obj.method()` → `this` is `obj`.
4. **Regular function call** — `func()` → `this` is `window` (or `undefined` in strict mode).
5. **Arrow functions** — `this` is inherited from the **enclosing scope** (lexical `this`). Arrow functions DON'T have their own `this`.

This is why arrow functions are great for callbacks — they don't lose the context:
```javascript
class User {
    constructor(name) { this.name = name; }
    greetLater() {
        setTimeout(() => {
            console.log(this.name); // Works! Arrow function inherits 'this'
        }, 1000);
    }
}
```

---

## 7. What are closures? Give a practical example.

**Answer:**
A closure is a function that **remembers and accesses variables from its outer scope**, even after the outer function has finished executing. The inner function "closes over" the outer variables.

```javascript
function createCounter() {
    let count = 0;    // This variable is "enclosed"
    return {
        increment: () => ++count,
        getCount: () => count
    };
}

const counter = createCounter();
counter.increment(); // 1
counter.increment(); // 2
counter.getCount();  // 2
// 'count' is not accessible directly — it's private!
```

The `count` variable is private — it can only be accessed through the returned methods. This is the **module pattern** for data encapsulation.

Practical uses: event handlers, callbacks, data privacy, function factories, memoization, and debounce/throttle functions.

---

## 8. What is the difference between arrow functions and regular functions?

**Answer:**
Arrow functions (`() => {}`) are not just shorter syntax — they behave differently:

1. **`this` binding** — Arrow functions don't have their own `this`. They inherit it from the enclosing scope. Regular functions have their own `this` determined by how they're called.

2. **`arguments` object** — Arrow functions don't have `arguments`. Regular functions do.

3. **Constructors** — Arrow functions CANNOT be used with `new`. Regular functions can.

4. **`prototype`** — Arrow functions don't have a `prototype` property.

5. **Implicit return** — `const double = x => x * 2;` — single expressions can omit `return` and braces.

My rule: use **arrow functions for callbacks and short functions**. Use **regular functions for methods and constructors** where `this` matters.

---

## 9. Explain the event loop in JavaScript.

**Answer:**
JavaScript is **single-threaded** — it executes one thing at a time. The event loop is the mechanism that handles asynchronous operations.

How it works:
1. **Call Stack** — where synchronous code executes. Functions are pushed on and popped off.
2. When an async operation (setTimeout, fetch, event listener) is encountered, it's handed off to the **Web APIs** (browser) or **libuv** (Node.js).
3. When the async operation completes, its callback is placed in the **Callback Queue** (or **Microtask Queue** for Promises).
4. The **Event Loop** constantly checks: "Is the Call Stack empty?" If yes, it takes the next callback from the queue and pushes it onto the stack.

**Microtasks** (Promise callbacks, `queueMicrotask`) have priority over **macrotasks** (setTimeout, setInterval, DOM events). All microtasks are processed before the next macrotask.

That's why `setTimeout(fn, 0)` doesn't run immediately — it waits for the call stack to be empty.

---

## 10. What is the difference between synchronous and asynchronous JavaScript?

**Answer:**
- **Synchronous** — code runs line by line, each line waits for the previous one to finish. If one operation takes 5 seconds, everything else is blocked.

- **Asynchronous** — long-running operations (API calls, file reads, timers) run in the background and don't block the main thread. When they complete, a callback is executed.

Async mechanisms in JavaScript:
1. **Callbacks** — the original way. Pass a function to run when the operation completes. Can lead to "callback hell" (deeply nested callbacks).
2. **Promises** — represent a future value. Cleaner chaining with `.then()` and `.catch()`.
3. **Async/Await** — syntactic sugar over Promises. Makes async code look synchronous:

```javascript
async function getUser() {
    try {
        const response = await fetch("/api/user");
        const user = await response.json();
        console.log(user);
    } catch (error) {
        console.error(error);
    }
}
```

---

## 11. What are Promises? Explain the three states.

**Answer:**
A Promise is an object representing the **eventual completion or failure** of an asynchronous operation. It's like a receipt at a restaurant — you don't have the food yet, but you have a guarantee that it'll come (or you'll be told it can't).

Three states:
1. **Pending** — initial state, operation still in progress
2. **Fulfilled (Resolved)** — operation completed successfully, result is available
3. **Rejected** — operation failed, error is available

```javascript
const promise = new Promise((resolve, reject) => {
    // async operation
    if (success) resolve(data);
    else reject(error);
});

promise
    .then(data => console.log(data))     // on success
    .catch(error => console.error(error)) // on failure
    .finally(() => console.log("Done")); // always runs
```

Key methods:
- `Promise.all([p1, p2])` — waits for ALL to resolve (fails if ANY reject)
- `Promise.race([p1, p2])` — resolves/rejects with the FIRST one to settle
- `Promise.allSettled([p1, p2])` — waits for all, gives results regardless of success/failure

---

## 12. Explain `async/await`. Why is it preferred over `.then()`?

**Answer:**
`async/await` is syntactic sugar built on top of Promises. An `async` function always returns a Promise. `await` pauses execution until the Promise resolves.

It's preferred because:
1. **Readability** — code looks synchronous, making it easier to understand flow
2. **Error handling** — use familiar `try/catch` instead of `.catch()` chains
3. **Debugging** — step-through debugging works naturally
4. **Complex flows** — sequential operations, loops with async calls are much cleaner

```javascript
// With .then() — can get messy
fetch("/api/user")
    .then(res => res.json())
    .then(user => fetch(`/api/orders/${user.id}`))
    .then(res => res.json())
    .then(orders => console.log(orders))
    .catch(err => console.error(err));

// With async/await — clean and readable
async function getUserOrders() {
    try {
        const res = await fetch("/api/user");
        const user = await res.json();
        const ordersRes = await fetch(`/api/orders/${user.id}`);
        const orders = await ordersRes.json();
        console.log(orders);
    } catch (err) {
        console.error(err);
    }
}
```

---

## 13. What is the Fetch API?

**Answer:**
The Fetch API is the modern, built-in way to make HTTP requests in JavaScript. It replaced XMLHttpRequest and returns **Promises**.

```javascript
// GET request
const response = await fetch("https://api.example.com/users");
const data = await response.json();

// POST request
const response = await fetch("https://api.example.com/users", {
    method: "POST",
    headers: { "Content-Type": "application/json" },
    body: JSON.stringify({ name: "Kunal", email: "kunal@test.com" })
});
```

Important gotcha: `fetch` does NOT reject on HTTP error statuses (404, 500). It only rejects on network failures. I always check `response.ok`:

```javascript
if (!response.ok) {
    throw new Error(`HTTP error: ${response.status}`);
}
```

---

## 14. What are array methods? Name the most important ones.

**Answer:**
JavaScript arrays have powerful built-in methods:

**Iteration (don't modify original):**
- `forEach(fn)` — loop through each item (no return value)
- `map(fn)` — transform each item, returns a NEW array
- `filter(fn)` — keep items that pass a test, returns a NEW array
- `reduce(fn, initial)` — accumulate values into one result
- `find(fn)` — return the FIRST matching item
- `some(fn)` — returns `true` if ANY item passes the test
- `every(fn)` — returns `true` if ALL items pass the test

**Mutation (modify original):**
- `push()` / `pop()` — add/remove from end
- `unshift()` / `shift()` — add/remove from start
- `splice(index, count, ...items)` — add/remove at specific position
- `sort()` — sorts in place (careful: sorts as strings by default!)
- `reverse()` — reverses in place

**Other useful:**
- `includes(value)` — check if array contains a value
- `indexOf(value)` — find index of a value
- `flat()` — flatten nested arrays
- `slice(start, end)` — extract a portion (doesn't modify original)

I use `map`, `filter`, and `reduce` daily — they're the foundation of functional programming in JavaScript.

---

## 15. What is destructuring in JavaScript?

**Answer:**
Destructuring lets me **extract values** from arrays or properties from objects into distinct variables — in a single, clean statement.

**Object destructuring:**
```javascript
const user = { name: "Kunal", age: 25, city: "NYC" };
const { name, age, city } = user;
// name = "Kunal", age = 25, city = "NYC"

// Renaming
const { name: userName } = user;  // userName = "Kunal"

// Default values
const { role = "user" } = user;   // role = "user" (doesn't exist on object)
```

**Array destructuring:**
```javascript
const colors = ["red", "green", "blue"];
const [first, second] = colors;
// first = "red", second = "green"

// Skip items
const [, , third] = colors;  // third = "blue"

// Swap variables
let a = 1, b = 2;
[a, b] = [b, a];  // a = 2, b = 1
```

I use destructuring in function parameters, API response handling, and React hooks (`const [state, setState] = useState()`).

---

## 16. What is the spread operator and rest parameter?

**Answer:**
Both use `...` syntax but serve opposite purposes:

**Spread (`...`)** — expands/spreads an iterable into individual elements:
```javascript
const arr1 = [1, 2, 3];
const arr2 = [...arr1, 4, 5];      // [1, 2, 3, 4, 5]

const obj1 = { a: 1, b: 2 };
const obj2 = { ...obj1, c: 3 };    // { a: 1, b: 2, c: 3 }

Math.max(...arr1);                   // 3
```

**Rest (`...`)** — collects remaining elements into an array:
```javascript
function sum(...numbers) {
    return numbers.reduce((a, b) => a + b, 0);
}
sum(1, 2, 3, 4); // 10

const [first, ...rest] = [1, 2, 3, 4];
// first = 1, rest = [2, 3, 4]
```

Rule of thumb: spread is used in **function calls and literals** (expanding), rest is used in **function definitions and destructuring** (collecting).

---

## 17. What is DOM manipulation? How do you select and modify elements?

**Answer:**
The DOM (Document Object Model) is a tree representation of the HTML document. JavaScript can read and modify this tree to dynamically change the page.

**Selecting elements:**
```javascript
document.getElementById("title");              // by ID
document.querySelector(".card");               // first match (CSS selector)
document.querySelectorAll(".card");            // all matches (NodeList)
document.getElementsByClassName("card");       // live HTMLCollection
```

**Modifying elements:**
```javascript
element.textContent = "New text";              // change text
element.innerHTML = "<strong>Bold</strong>";   // change HTML
element.style.color = "red";                   // change CSS
element.classList.add("active");               // add class
element.setAttribute("href", "/new");          // set attribute
```

**Creating/removing elements:**
```javascript
const div = document.createElement("div");
div.textContent = "Hello";
document.body.appendChild(div);                // add to DOM
div.remove();                                  // remove from DOM
```

---

## 18. What is event delegation? Why is it useful?

**Answer:**
Event delegation is a technique where I attach a single event listener to a **parent element** instead of individual listeners to each child. When a child is clicked, the event **bubbles up** to the parent, where I handle it.

```javascript
// Instead of adding a listener to every <li>:
document.querySelector("ul").addEventListener("click", function(e) {
    if (e.target.tagName === "LI") {
        console.log("Clicked:", e.target.textContent);
    }
});
```

Why it's useful:
1. **Performance** — one listener instead of hundreds (if there are many items).
2. **Dynamic elements** — works for elements added AFTER the listener is set up. If I add a new `<li>` later, it automatically works.
3. **Memory** — fewer listeners means less memory usage.

It works because of **event bubbling** — events propagate from the target element up through all ancestors to the document root.

---

## 19. What are truthy and falsy values?

**Answer:**
In JavaScript, every value is either **truthy** or **falsy** — meaning it's treated as `true` or `false` in a boolean context (like `if` statements).

**Falsy values** (only 8):
- `false`
- `0` and `-0`
- `""` (empty string)
- `null`
- `undefined`
- `NaN`
- `0n` (BigInt zero)
- `document.all` (legacy quirk)

**Everything else is truthy**, including:
- `"0"` (string with zero)
- `" "` (string with space)
- `[]` (empty array)
- `{}` (empty object)
- `function(){}` (empty function)

This is why `if (value)` works as a null/undefined check. But be careful — `if (count)` fails when `count` is `0` because `0` is falsy. In that case, use explicit checks: `if (count !== undefined)`.

---

## 20. What is the difference between `null` and `undefined`?

**Answer:**
- **`undefined`** — means a variable has been declared but **not assigned a value**. JavaScript sets this automatically. It's the default for: uninitialized variables, missing function parameters, missing object properties, and functions that don't explicitly return.

- **`null`** — means **intentionally empty**. I set this explicitly to indicate "no value on purpose." It's a deliberate assignment.

```javascript
let x;           // undefined (automatic)
let y = null;    // null (intentional)
```

Quirks:
- `typeof undefined` → `"undefined"`
- `typeof null` → `"object"` (known bug)
- `null == undefined` → `true` (loose equality)
- `null === undefined` → `false` (strict equality)

My practice: I use `null` to explicitly clear a value. I never assign `undefined` manually — it's JavaScript's job to set that.

---

## 21. What is an IIFE (Immediately Invoked Function Expression)?

**Answer:**
An IIFE is a function that **runs immediately** after it's defined. It creates a private scope so variables don't pollute the global namespace.

```javascript
(function() {
    const secret = "hidden";
    console.log("IIFE executed!");
})();
// 'secret' is not accessible here
```

Before ES6 modules and `let`/`const`, IIFEs were the primary way to create private scope and avoid global variable conflicts. Libraries like jQuery were wrapped in IIFEs.

Now with ES6 modules, `let`/`const`, and block scoping, IIFEs are less common. But I still see them in legacy code and in situations where I need immediate execution with encapsulation.

---

## 22. What is `localStorage` vs `sessionStorage` vs cookies?

**Answer:**
| Feature | localStorage | sessionStorage | Cookies |
|---------|-------------|----------------|---------|
| Capacity | ~5MB | ~5MB | ~4KB |
| Expiry | Never (manual) | When tab closes | Custom expiry date |
| Sent to server | No | No | Yes (with every HTTP request) |
| Scope | All tabs (same origin) | Single tab | All tabs (same domain) |
| API | Simple JS API | Simple JS API | Document.cookie (messy) |

I use `localStorage` for user preferences (theme, language). `sessionStorage` for temporary data (form progress). Cookies for authentication tokens that the server needs to see, or when I need server-side access.

---

## 23. How does `setTimeout` and `setInterval` work?

**Answer:**
- **`setTimeout(fn, delay)`** — executes the function ONCE after the specified delay (in milliseconds).
- **`setInterval(fn, delay)`** — executes the function REPEATEDLY at every interval.

```javascript
// Run once after 2 seconds
const timerId = setTimeout(() => console.log("Hello!"), 2000);
clearTimeout(timerId);     // Cancel it

// Run every 1 second
const intervalId = setInterval(() => console.log("Tick"), 1000);
clearInterval(intervalId); // Stop it
```

Important: the delay is NOT guaranteed. `setTimeout(fn, 0)` doesn't run at 0ms — it runs after the current call stack is clear. The delay is a **minimum**, not exact.

Also, `setInterval` can cause stacking if the function takes longer than the interval. For reliable intervals, I use recursive `setTimeout` instead.

---

## 24. What is optional chaining (`?.`) and nullish coalescing (`??`)?

**Answer:**
**Optional chaining (`?.`)** — safely access deeply nested properties without checking each level for null/undefined:

```javascript
// Without optional chaining
const city = user && user.address && user.address.city;

// With optional chaining
const city = user?.address?.city;    // Returns undefined if any part is null/undefined
user?.getProfile?.();                // Safe method call
arr?.[0];                            // Safe array access
```

**Nullish coalescing (`??`)** — provides a default value only for `null` or `undefined` (NOT for `0`, `""`, or `false`):

```javascript
const count = userCount ?? 10;      // 10 only if userCount is null/undefined
const count = userCount || 10;      // 10 if userCount is 0, "", false, null, or undefined
```

I use `??` over `||` when `0`, `""`, or `false` are valid values that I don't want to override.

---

## 25. What is event bubbling and capturing?

**Answer:**
When an event occurs on a nested element, it goes through two phases:

1. **Capturing (trickling down)** — the event travels from the `document` root DOWN to the target element, passing through each ancestor.

2. **Bubbling (bubbling up)** — the event travels from the target element BACK UP to the `document` root.

By default, event listeners fire during the **bubbling** phase. To listen during capturing, pass `true` as the third argument:
```javascript
element.addEventListener("click", handler, true);  // Capturing phase
element.addEventListener("click", handler);         // Bubbling phase (default)
```

I can stop propagation with:
- `e.stopPropagation()` — prevents the event from bubbling further
- `e.preventDefault()` — prevents the default browser action (like following a link)

Event delegation works because of bubbling — clicks on children bubble up to the parent where we handle them.
