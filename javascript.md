# 🟡 JavaScript Deep Dive & Interview Questions

**Status:** 🚧 - **Last Updated:** 23th March 2026

## Table of Contents

- [🟡 JavaScript Deep Dive \& Interview Questions](#-javascript-deep-dive--interview-questions)
  - [Table of Contents](#table-of-contents)
  - [JavaScript Fundamentals](#javascript-fundamentals)
    - [The Modern Mode: "use strict"](#the-modern-mode-use-strict)
    - [Variables](#variables)
    - [Data Types](#data-types)
    - [Type Conversions](#type-conversions)

## [JavaScript Fundamentals](https://javascript.info/first-steps)

### The Modern Mode: "use strict"

✅ **1. What is Strict Mode in JavaScript and why should we use it?**

Strict Mode is a way to opt into a restricted version of JavaScript by adding:

```js
"use strict";
```
at the top of a file or function.

It helps developers write safer, cleaner, and more predictable code by:

- Preventing accidental global variables
- Throwing errors for silent bugs (e.g., assigning to non-writable properties)
- Disallowing duplicate parameter names
- Making `this` behavior more predictable
- Restricting some unsafe or deprecated features

👉 **Example:**

```js
"use strict";

x = 10; // ❌ ReferenceError (instead of creating global variable)
```

Without strict mode, `x` would silently become a global variable — which is dangerous.

📌 **Why it matters (senior-level insight):**
Strict mode helps engines optimize code better and avoids legacy JS pitfalls, making it essential for large-scale applications.

---

✅ **2. How does `this` behave differently in Strict Mode?**

In Strict Mode, the value of `this` is not automatically bound to the global object.

👉 **Example:**

```js
"use strict";

function test() {
  console.log(this);
}

test(); // ✅ ❌ undefined
```

Without strict mode:

```js
function test() {
  console.log(this);
}

test(); // ✅ ✅ window (in browser)
```

📝 **Key difference:**
- Non-strict mode: `this` defaults to `window` (or global object)
- Strict mode: `this` is `undefined` unless explicitly set

📌 **Why this is important:**
- Prevents accidental access to the global object
- Makes function behavior more predictable
- Avoids bugs in large codebases

### Variables

✅ **1. What is the difference between `var`, `let`, and `const`?**

| Feature | `var` | `let` | `const` |
| :--- | :--- | :--- | :--- |
| **Scope** | Function-scoped | Block-scoped | Block-scoped |
| **Hoisting** | Hoisted (initialized as `undefined`) | Hoisted (but in TDZ) | Hoisted (but in TDZ) |
| **Temporal Dead Zone (TDZ)** | ❌ No | ✅ Yes | ✅ Yes |
| **Redeclaration** | ✅ Allowed | ❌ Not allowed | ❌ Not allowed |
| **Reassignment** | ✅ Allowed | ✅ Allowed | ❌ Not allowed |
| **Must initialize when declared** | ❌ No | ❌ No | ✅ Yes |
| **Attached to `window` (global)** | ✅ Yes | ❌ No | ❌ No |
| **Use in loops (`for`)** | ❌ Problematic (closure issues) | ✅ Safe | ✅ Safe |
| **Best use case** | Legacy code only | Variables that change | Constants / immutable bindings |

---

📝 **Key explanations (what makes you stand out)**

**1. Scope difference**
```js
{
  var a = 1;
  let b = 2;
}
console.log(a); // ✅ 1
console.log(b); // ❌ ReferenceError
```

**2. Temporal Dead Zone (TDZ)**
```js
console.log(x); // ❌ ReferenceError
let x = 10;
```
`let` and `const` exist in a TDZ from the start of the block until declaration.

**3. `const` is NOT immutable (important trick question)**
```js
const obj = { name: "A" };
obj.name = "B"; // ✅ allowed
```
👉 `const` prevents reassignment, not mutation of objects.

**4. Classic `var` bug (closure issue)**
```js
for (var i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// ❌ 3 3 3

// Because `var` is function-scoped, not block-scoped. By the time the callbacks run, the loop has finished and `i` is 3. All callbacks share the same `i` variable, so they all print 3.

for (let i = 0; i < 3; i++) {
  setTimeout(() => console.log(i), 100);
}
// ✅ 0 1 2
```

### Data Types

✅ **1. How many data types in JS?**

There are 8 basic data types in JavaScript.

**Seven primitive data types:**
- `number` for numbers of any kind: integer or floating-point, integers are limited by ±(253-1).
- `bigint` for integer numbers of arbitrary length.
- `string` for strings. A string may have zero or more characters, there’s no separate single-character type.
- `boolean` for true/false.
- `null` for unknown values – a standalone type that has a single value null.
- `undefined` for unassigned values – a standalone type that has a single value undefined.
- `symbol` for unique identifiers.

**one non-primitive data type:**
- `object` for more complex data structures.

✅ **2. How to see which type is stored in variable?**

The `typeof` operator allows us to see which type is stored in a variable.

- Returns a string with the name of the type, like "string".
- For null returns "object" – this is an error in the language, it’s not actually an object.

✅ **3. Number**

- The `number` type represents both integer and floating point numbers.

- `Infinity` represents the mathematical Infinity ∞. It is a special value that’s greater than any number
```js
alert( 1 / 0 ); // Infinity
alert( Infinity ); // Infinity
```

- `NaN` represents a computational error
```js
alert( "not a number" / 2 ); // NaN, such division is erroneous
alert( NaN + 1 ); // NaN
alert( 3 * NaN ); // NaN
```

✅ **4. String**

- A string in JavaScript must be surrounded by quotes.

- In JavaScript, there are 3 types of quotes.
  - Double quotes: "Hello".
  - Single quotes: 'Hello'.
  - Backticks: `Hello`.

### Type Conversions

✅ **1. String Conversion**

```js
let value = true;
alert(typeof value); // 'boolean'

value = String(value); // now value is a string "true"
alert(typeof value); // 'string'
```

