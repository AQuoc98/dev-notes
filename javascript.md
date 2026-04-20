# 🟡 JavaScript Deep Dive & Interview Questions

**Status:** 🚧 - **Last Updated:** 24th March 2026


- [🟡 JavaScript Deep Dive \& Interview Questions](#-javascript-deep-dive--interview-questions)
  - [The Modern Mode: "use strict"](#the-modern-mode-use-strict)
    - [✅ **1. What is Strict Mode in JavaScript and why should we use it?**](#-1-what-is-strict-mode-in-javascript-and-why-should-we-use-it)
    - [✅ **2. How does `this` behave differently in Strict Mode?**](#-2-how-does-this-behave-differently-in-strict-mode)
  - [Variables](#variables)
    - [✅ **1. What is the difference between `var`, `let`, and `const`?**](#-1-what-is-the-difference-between-var-let-and-const)
  - [Data Types](#data-types)
    - [✅ **1. How many data types in JS?**](#-1-how-many-data-types-in-js)
    - [✅ **2. How to see which type is stored in variable?**](#-2-how-to-see-which-type-is-stored-in-variable)
    - [✅ **3. Number**](#-3-number)
    - [✅ **4. String**](#-4-string)
  - [Type Conversions](#type-conversions)
    - [✅ **1. String Conversion**](#-1-string-conversion)
    - [✅ **2. Numberic Conversion**](#-2-numberic-conversion)
    - [✅ **3. Boolean Conversion**](#-3-boolean-conversion)
    - [✅ **4. Numeric conversion, unary +**](#-4-numeric-conversion-unary-)
  - [Operators](#operators)
    - [✅ **1. What is the `unary`, `binary`, and `operand` operator?**](#-1-what-is-the-unary-binary-and-operand-operator)
    - [✅ **2. Maths**](#-2-maths)
    - [✅ **3. String concatenation with binary +**](#-3-string-concatenation-with-binary-)
    - [✅ **4. Increment/Decrement**](#-4-incrementdecrement)
  - [Comparison](#comparison)
    - [✅ **1. Comparison operators**](#-1-comparison-operators)
    - [✅ **2. Comparison of different types**](#-2-comparison-of-different-types)
    - [✅ **3. null vs 0**](#-3-null-vs-0)
    - [✅ **4. undefined vs 0**](#-4-undefined-vs-0)
    - [✅ **5. == vs ===**](#-5--vs-)
  - [Conditional Statements](#conditional-statements)
    - [✅ **1. `if` statement**](#-1-if-statement)
    - [✅ **2. `else if` statement**](#-2-else-if-statement)
    - [✅ **3. `switch` statement**](#-3-switch-statement)
    - [✅ **4. Ternary operator**](#-4-ternary-operator)
  - [Logical Operators](#logical-operators)
    - [✅ **1. Logical AND (\&\&)**](#-1-logical-and-)
    - [✅ **2. Logical OR (||)**](#-2-logical-or-)
    - [✅ **3. Logical NOT (!)**](#-3-logical-not-)
    - [✅ **4. Double NOT (!!)**](#-4-double-not-)
    - [✅ **5. Nullish Coalescing (??)**](#-5-nullish-coalescing-)
  - [Loop](#loop)
    - [✅ **1. `for` loop**](#-1-for-loop)
    - [✅ **2. `while` loop**](#-2-while-loop)
    - [✅ **3. `do...while` loop**](#-3-dowhile-loop)
    - [✅ **4. `for...in` loop**](#-4-forin-loop)
    - [✅ **5. `for...of` loop**](#-5-forof-loop)
    - [✅ **6. `break` and `continue`**](#-6-break-and-continue)
  - [Functions](#functions)
    - [✅ **1. Function Declaration**](#-1-function-declaration)
    - [✅ **2. Function Expression**](#-2-function-expression)
    - [✅ **3. Arrow Function**](#-3-arrow-function)
    - [✅ **4. Local and Global Variables**](#-4-local-and-global-variables)
    - [✅ **5. Callback Functions**](#-5-callback-functions)
  - [Objects](#objects)
    - [✅ **1. The basics**](#-1-the-basics)
    - [✅ **2. Object References and Copies**](#-2-object-references-and-copies)
    - [✅ **3. Comparing Objects**](#-3-comparing-objects)
    - [✅ **4. Cloning and Merging Objects**](#-4-cloning-and-merging-objects)
    - [✅ **5. Nested Cloning**](#-5-nested-cloning)
    - [✅ **6. Object Methods**](#-6-object-methods)
    - [✅ **7. Constructor functions**](#-7-constructor-functions)
    - [✅ **8. Optional Chaining**](#-8-optional-chaining)
  - [`this` Keyword](#this-keyword)
    - [✅ **1. The `this` keyword in a method**](#-1-the-this-keyword-in-a-method)
    - [✅ **2. The `this` keyword outside of a method**](#-2-the-this-keyword-outside-of-a-method)
    - [✅ **3. Arrow Functions and `this`**](#-3-arrow-functions-and-this)



## The Modern Mode: "use strict"

### ✅ **1. What is Strict Mode in JavaScript and why should we use it?**

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

[↑ Back to top](#-javascript-deep-dive--interview-questions)

---

### ✅ **2. How does `this` behave differently in Strict Mode?**

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

[↑ Back to top](#-javascript-deep-dive--interview-questions)

## Variables

### ✅ **1. What is the difference between `var`, `let`, and `const`?**

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

[↑ Back to top](#-javascript-deep-dive--interview-questions)

## Data Types

### ✅ **1. How many data types in JS?**

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

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **2. How to see which type is stored in variable?**

The `typeof` operator allows us to see which type is stored in a variable.

- Returns a string with the name of the type, like "string".
- For null returns "object" – this is an error in the language, it’s not actually an object.

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **3. Number**

- The `number` type represents both integer and floating point numbers.

- `Infinity` represents the mathematical Infinity ∞. It is a special value that’s greater than any number
```js
console.log(1 / 0); // Infinity
console.log(Infinity); // Infinity
```

- `NaN` represents a computational error
```js
console.log("not a number" / 2); // NaN, such division is erroneous
console.log(NaN + 1); // NaN
console.log(3 * NaN); // NaN
```

- The common methods for numbers: [number methods](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number), [math methods](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math)

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **4. String**

- A string in JavaScript must be surrounded by quotes.

- In JavaScript, there are 3 types of quotes.
  - Double quotes: "Hello".
  - Single quotes: 'Hello'.
  - Backticks: `Hello`.

[↑ Back to top](#-javascript-deep-dive--interview-questions)

## Type Conversions

### ✅ **1. String Conversion**

```js
let value = true;
console.log(typeof value); // 'boolean'

value = String(value); // now value is a string "true"
console.log(typeof value); // 'string'
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **2. Numberic Conversion**

Numeric conversion in `mathematical functions` and `expressions` happens automatically.
```js
console.log( "6" / "2" ); // 3
console.log( "6" * "2" ); // 12
console.log( "6" - "2" ); // 4
console.log( "6" % "2" ); // 0
```

Use `Number()` function to explicitly convert a value to a number:
```js
let str = "123";
console.log(typeof str); // 'string'

let num = Number(str); // becomes a number 123
console.log(typeof num); // 'number'
```

Number conversion rules:
<table>
  <thead>
    <tr>
      <th style="min-width:120px; text-align:left;">Value</th>
      <th style="min-width:260px; text-align:left;">Becomes...</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><code>undefined</code></td>
      <td><code>NaN</code></td>
    </tr>
    <tr>
      <td><code>null</code></td>
      <td>0</td>
    </tr>
    <tr>
      <td><code>true</code> &amp; <code>false</code></td>
      <td>1 and 0</td>
    </tr>
    <tr>
      <td><code>string</code></td>
      <td>If the string is empty or contains only spaces, it becomes 0. Otherwise, it becomes a number if it can be parsed as one, or <code>NaN</code> if it cannot.</td>
    </tr>
  </tbody>
</table>

```js
console.log( Number("   123   ") ); // 123
console.log( Number("   ") ); // 0
console.log( Number("123") ); // 123
console.log( Number("123abc") ); // NaN
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **3. Boolean Conversion**

Use `Boolean()` function to explicitly convert a value to a boolean:
```js
console.log( Boolean(1) ); // true
console.log( Boolean(0) ); // false

console.log( Boolean("hello") ); // true
console.log( Boolean("") ); // false
```

The conversion rules:
- `0`, `null`, `undefined`, `NaN`, and `""` (empty string) become `false`.
- All other values become `true`.

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **4. Numeric conversion, unary +**
- If the operand is not a number, the unary plus converts it into a number

```js
// No effect on numbers
let x = 1;
console.log( +x ); // 1

let y = -2;
console.log( +y ); // -2

// Converts non-numbers
console.log( +true ); // 1
console.log( +"" );   // 0


let apples = "2";
let oranges = "3";
console.log( +apples + +oranges ); // 5
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

## Operators

### ✅ **1. What is the `unary`, `binary`, and `operand` operator?**

- **Operand:** An operand is a value or variable on which an operator acts.
  - In the expression `5 + 3`, the numbers `5` and `3` are operands, and `+` is the operator.
  - In `x++`, `x` is the operand, `++` is the operator.

- **Unary operator:** Operates on a single operand.
```js
let x = 1;

x = -x;
alert( x ); // -1, unary negation was applied
```

- **Binary operator:** Operates on two operands.
```js
let x = 1, y = 3;
alert( y - x ); // 2, binary minus subtracts values
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **2. Maths**

- `+` Addition
- `-` Subtraction
- `*` Multiplication
- `/` Division
- `%` Remainder: The result of a % b is the remainder of the integer division of a by b.

```js
console.log( 5 % 2 ); // 1, the remainder of 5 divided by 2
console.log( 8 % 3 ); // 2, the remainder of 8 divided by 3
console.log( 8 % 4 ); // 0, the remainder of 8 divided by 4
```
- `**` Exponentiation: a ** b is a to the power of b, i.e., a multiplied by itself b times.

```js
console.log( 2 ** 2 ); // 2² = 4
console.log( 2 ** 3 ); // 2³ = 8
console.log( 2 ** 4 ); // 2⁴ = 16
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **3. String concatenation with binary +**

- If the binary + is applied to strings, it merges (concatenates) them

```js
console.log( 'my' + 'string' ); // 'mystring'
console.log( '1' + 2 ); // "12"
console.log( 2 + '1' ); // "21"

console.log(2 + 2 + '1' ); // "41" and not "221"
console.log('1' + 2 + 2); // "122" and not "14"
```

-  Other arithmetic operators work only with numbers and always `convert their operands to `numbers`

```js
console.log( 6 - '2' ); // 4, converts '2' to a number
console.log( '6' / '2' ); // 3, converts both operands to numbers
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **4. Increment/Decrement**

- Increment ++ increases a variable by 1

```js
let counter = 2;
counter++; // counter = counter + 1
console.log( counter ); // 3
```

- Decrement -- decreases a variable by 1

```js
let counter = 2;
counter--; // counter = counter - 1
console.log( counter ); // 1
```

- The prefix form (++counter) returns the new value, while the postfix form (counter++) returns the old value.

```js
let counter = 1;
console.log( ++counter ); // 2, returns the new value
console.log( counter++ ); // 2, returns the old value, then increments
console.log( counter ); // 3, the final value after both increments
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

## Comparison

### ✅ **1. Comparison operators**
- All comparison operators return a boolean value
- Strings are compared letter-by-letter in the dictionary order (lexicographically).

```js
console.log( 5 > 4 ); // true
console.log( 1 < 2 ); // true
console.log( 2 >= 1 ); // true
console.log( 2 <= 1 ); // false
console.log( 2 >= 2 ); // true
console.log( 2 <= 2 ); // true
console.log( 2 == 1 ); // false (wrong)
console.log( 2 != 1 ); // true (correct)
console.log( 'Z' > 'A' ); // true
console.log( 'B' < 'b' ); // true
console.log( 'Glow' > 'Glee' ); // true
console.log( 'Bee' > 'Be' ); // true
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **2. Comparison of different types**

- When comparing values of different types, JavaScript converts the values to numbers (except for strict equality `===` and strict inequality `!==` which do not perform type conversion).

```js
console.log( '2' > 1 ); // true, string '2' is converted to number 2
console.log( '01' == 1 ); // true, string '01' is converted to number 1
console.log( true == 1 ); // true, boolean true is converted to number 1
console.log( false == 0 ); // true, boolean false is converted to number 0
console.log( null == undefined ); // true, null and undefined are considered equal
console.log( null === undefined ); // false, different types
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **3. null vs 0**

- `null` is not equal to `0` or any other value except `undefined`.

```js
console.log( null > 0 ); // false
console.log( null == 0 ); // false
console.log( null >= 0 ); // true
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **4. undefined vs 0**
- `undefined` is not equal to `0` or any other value except `null`.

```js
console.log( undefined > 0 ); // false
console.log( undefined == 0 ); // false
console.log( undefined >= 0 ); // false
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **5. == vs ===**

- `==` performs type conversion before comparing values.
- `===` does not perform type conversion and requires both the value and type to be the same.

```js
console.log( 5 == '5' ); // true, string '5' is converted to number 5
console.log( 5 === '5' ); // false, different types
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

## Conditional Statements

###  ✅ **1. `if` statement**

The `if` statement executes a block of code if a specified condition is true.

```js
let age = 18;
if (age >= 18) {
  console.log("You are an adult.");
} else {
  console.log("You are a minor.");
}

// Output: "You are an adult."
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

###  ✅ **2. `else if` statement**

The `else if` statement allows you to check multiple conditions.

```js
let score = 85;
if (score >= 90) {
  console.log("Grade: A");
} else if (score >= 80) {
  console.log("Grade: B");
} else if (score >= 70) {
  console.log("Grade: C");
} else {
  console.log("Grade: F");
}
// Output: "Grade: B"
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

###  ✅ **3. `switch` statement**

The `switch` statement is used to perform different actions based on different conditions.

```js
let day = 2;
switch (day) {
  case 1:
    console.log("Monday");
    break;
  case 2:
    console.log("Tuesday");
    break;
  case 3:
    console.log("Wednesday");
    break;
  default:
    console.log("Another day");
}

// Output: "Tuesday"
```

```js
let day = 2;
switch (day) {
  case 1:
  case 2:
  case 3:
    console.log("Weekday");
    break;
  case 4:
  case 5:
    console.log("Weekend");
    break;
  default:
    console.log("Another day");
}
// Output: "Weekday"
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

###  ✅ **4. Ternary operator**

The `?:` (ternary operator) is a shorthand for an `if-else` statement.

```js
let age = 18;
let message = (age >= 18) ? "You are an adult." : "You are a minor.";
console.log(message); // Output: "You are an adult."
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

## Logical Operators

### ✅ **1. Logical AND (&&)**

- The logical AND operator returns true if both operands are true.

```js
console.log( true && true ); // true
console.log( true && false ); // false
console.log( false && true ); // false
console.log( false && false ); // false
```

- Find the first falsy value or the last value if all are truthy:

```js
console.log( 1 && 0 ); // 0 (falsy)
console.log( 1 && 5 ); // 5 (truthy, returns last value)
console.log( null && "Hello" ); // null (falsy)
console.log( "Hello" && "World" ); // "World" (both truthy, returns last value)
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **2. Logical OR (||)**

- The logical OR operator returns true if at least one operand is true.

```js
console.log( true || true ); // true
console.log( true || false ); // true
console.log( false || true ); // true
console.log( false || false ); // false
```

- Find the first truthy value or the last value if all are falsy:

```js
console.log( 0 || 1 ); // 1 (truthy)
console.log( null || "Hello" ); // "Hello" (truthy)
console.log( "" || 0 || null ); // null (all falsy, returns last value)
console.log( "Hello" || "World" ); // "Hello" (first truthy)
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **3. Logical NOT (!)**

- The logical NOT operator returns true if the operand is false, and false if the operand is true.

```js
console.log( !true ); // false
console.log( !false ); // true
console.log( !0 ); // true (0 is falsy)
console.log( !"Hello" ); // false ("Hello" is truthy)
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **4. Double NOT (!!)**

- The double NOT operator converts a value to its boolean equivalent.

```js
console.log( !!true ); // true
console.log( !!false ); // false
console.log( !!0 ); // false (0 is falsy)
console.log( !!"Hello" ); // true ("Hello" is truthy)
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **5. Nullish Coalescing (??)**

- The nullish coalescing operator returns the right-hand operand when the left-hand operand is `null` or `undefined`, otherwise it returns the left-hand operand.

```js
console.log( null ?? "default" ); // "default"
console.log( undefined ?? "default" ); // "default"
console.log( undefined ?? null ); // null
console.log( 0 ?? "default" ); // 0 (not null or undefined)
console.log( "" ?? "default" ); // "" (not null or undefined)
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

## Loop

### ✅ **1. `for` loop**

- The `for` loop is used to repeat a block of code a certain number of times.

```js
for (let i = 0; i < 5; i++) {
  console.log(i);
}

// Output: 0 1 2 3 4
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **2. `while` loop**

- The `while` loop executes a block of code as long as a specified condition is true.

```js
let i = 0;
while (i < 5) {
  console.log(i);
  i++;
}

// Output: 0 1 2 3 4
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **3. `do...while` loop**

- The `do...while` loop is similar to the `while` loop, but it executes the block of code at least once before checking the condition.

```js
let i = 0;
do {
  console.log(i);
  i++;
} while (i < 5);

// Output: 0 1 2 3 4
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **4. `for...in` loop**

- The `for...in` loop iterates over the enumerable properties of an object.

```js
const person = { name: "Alice", age: 30, city: "New York" };
for (let key in person) {
  console.log(key + ": " + person[key]);
}
// Output: "name: Alice", "age: 30", "city: New York"
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **5. `for...of` loop**

- The `for...of` loop iterates over iterable objects (like arrays, strings, etc.) and returns the values of the elements.

```js
const arr = [10, 20, 30];
for (let value of arr) {
  console.log(value);
}
// Output: 10, 20, 30
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **6. `break` and `continue`**

- The `break` statement exits the loop immediately.
- The `continue` statement skips the current iteration and continues with the next one.

```js
for (let i = 0; i < 5; i++) {
  if (i === 2) {
    continue; // Skip the rest of the loop when i is 2
  }
  if (i === 4) {
    break; // Exit the loop when i is 4
  }
  console.log(i);
}

// Output: 0, 1, 3 (2 is skipped, and loop exits before 4)
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

## Functions

### ✅ **1. Function Declaration**

- A function declaration looks like this:

```js
function name(parameters, delimited, by, comma) {
  /* code */
}
```

- Parameters are optional, and the function body can be empty.
- Function declarations are hoisted, meaning they can be called before they are defined in the code.
- Function declarations must have a name, and they are not expressions.
- Default values for parameters can be set in the function declaration.

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **2. Function Expression**

- A function expression defines a function as part of a larger expression, and can be anonymous.

```js
const greet = function(name) {
  return "Hello, " + name + "!";
};
console.log(greet("Bob")); // Output: "Hello, Bob!"
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **3. Arrow Function**

- An arrow function is a shorter syntax for writing function expressions, and it does not have its own `this` context.

```js
const greet = (name) => {
  return "Hello, " + name + "!";
};
console.log(greet("Charlie")); // Output: "Hello, Charlie!"
```

- If the function body contains only a single expression, you can omit the braces and the `return` keyword.

```js
const greet = name => "Hello, " + name + "!";
console.log(greet("Dave")); // Output: "Hello, Dave!"
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **4. Local and Global Variables**

- Variables declared inside a function are local to that function and cannot be accessed outside of it.

```js
function myFunction() {
  let localVar = "I am local";
  console.log(localVar); // Output: "I am local"
}
myFunction();
console.log(localVar); // ❌ ReferenceError: localVar is not defined
```

- Variables declared outside of any function are global and can be accessed from anywhere in the code.

```js
let globalVar = "I am global";
function myFunction() {
  console.log(globalVar); // Output: "I am global"
}
myFunction();
console.log(globalVar); // Output: "I am global"
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **5. Callback Functions**

- A callback function is a function that is passed as an argument to another function and is executed after some operation has been completed.

```js
function fetchData(callback) {
  setTimeout(() => {
    const data = "Data fetched";
    callback(data); // Call the callback function with the fetched data
  }, 1000);
}

fetchData((result) => {
  console.log(result); // Output: "Data fetched" after 1 second
});
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

## Objects

### ✅ **1. The basics**

- An object literal is a comma-separated list of name-value pairs wrapped in curly braces.

```js
const person = {
  name: "Alice",
  age: 30,
  city: "New York"
};
```

- An Object can be created using the `new Object()` syntax, but object literals are more common and concise.

```js
let user = new Object(); // "object constructor" syntax
let user = {};  // "object literal" syntax
```

- Object properties can be accessed using dot notation or bracket notation.

```js
console.log(person.name); // Output: "Alice"
console.log(person["age"]); // Output: 30
```

- Properties can be `added`, `modified`, or `deleted` from an object.

```js
person.job = "Developer"; // Adding a new property
person.age = 31; // Modifying an existing property
delete person.city; // Deleting a property
console.log(person); // Output: { name: "Alice", age: 31, job: "Developer" }
```

- `Property value shorthand` allows you to create objects more concisely when the property name is the same as the variable name.

```js
let name = "Bob";
let age = 25;
let user = { name, age }; // Shorthand for { name: name, age: age }
console.log(user); // Output: { name: "Bob", age: 25 }
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **2. Object References and Copies**

- A variable assigned to an object stores not the object itself, but its “address in memory” – in other words “a reference” to it.

```js
let user = { name: "Alice" };
let admin = user; // admin references the same object as user
admin.name = "Bob"; // Modifying the object through admin
console.log(user.name); // Output: "Bob" (user sees the change)
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **3. Comparing Objects**

- Two variables that reference the same object are equal.

```js
let user = { name: "Alice" };
let admin = user;
console.log(user === admin); // Output: true
```

- Two objects with the same properties and values are not equal, as they are different instances.

```js
let user = { name: "Alice" };
let admin = { name: "Alice" };
console.log(user === admin); // Output: false
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **4. Cloning and Merging Objects**

- To clone an object, you can use `Object.assign()` or the spread operator.

```js
let user = { name: "Alice", age: 30 };
let clone = Object.assign({}, user); // Cloning using Object.assign
let clone2 = { ...user }; // Cloning using spread operator
console.log(clone); // Output: { name: "Alice", age: 30 }
console.log(clone2); // Output: { name: "Alice", age: 30 }
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **5. Nested Cloning**

- When an object contains nested objects, cloning it requires a deep clone to ensure all levels are copied.

```js
let user = { name: "Alice", address: { city: "New York", country: "USA" } };
let clone = JSON.parse(JSON.stringify(user)); // Deep cloning using JSON methods
console.log(clone); // Output: { name: "Alice", address: { city: "New York", country: "USA" } }
```

- Other methods for deep cloning include using libraries like `lodash` `_.cloneDeep` or implementing a custom recursive function to handle nested objects.

```js
function deepClone(obj) {
  if (obj === null || typeof obj !== "object") {
    return obj; // Return the value if obj is not an object
  }
  let clone = Array.isArray(obj) ? [] : {};
  for (let key in obj) {
    clone[key] = deepClone(obj[key]); // Recursively clone nested objects
  }
  return clone;
}
let user = { name: "Alice", address: { city: "New York", country: "USA" } };
let clone = deepClone(user);
console.log(clone); // Output: { name: "Alice", address: { city: "New York", country: "USA" } }
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **6. Object Methods**

- Objects can have methods, which are functions that operate on the object’s properties.

```js
let user = {
  name: "Alice",
  age: 30,
  greet() {
    console.log("Hello, " + this.name + "!");
  }
};
user.greet(); // Output: "Hello, Alice!"
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **7. Constructor functions**

- Constructor functions are used to create multiple objects with the same structure.

```js
function User(name, age) {
  this.name = name;
  this.age = age;
  this.greet = function() {
    console.log("Hello, " + this.name + "!");
  };
}
let user1 = new User("Alice", 30);
let user2 = new User("Bob", 25);
user1.greet(); // Output: "Hello, Alice!"
user2.greet(); // Output: "Hello, Bob!"
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **8. Optional Chaining**

- Optional chaining allows you to safely access nested properties of an object without having to check if each level exists.

```js
let user = { name: "Alice", address: { city: "New York" } };
console.log(user.address?.city); // Output: "New York"
console.log(user.address?.country); // Output: undefined (instead of throwing an error)
console.log(user.contact?.email); // Output: undefined (instead of throwing an error)
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

## `this` Keyword

### ✅ **1. The `this` keyword in a method**

- The `this` keyword in a method refers to the object it belongs to, allowing access to its properties and other methods.

```js
let user = {
  name: "Alice",
  age: 30,
  greet() {
    console.log("Hello, " + this.name + "!");
  },
  haveBirthday() {
    this.age++;
    console.log(this.name + " is now " + this.age + " years old.");
  }
};
user.greet(); // Output: "Hello, Alice!"
user.haveBirthday(); // Output: "Alice is now 31 years old."
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **2. The `this` keyword outside of a method**

- If a function is called without an object, `this` will refer to the global object (or be `undefined` in strict mode).

```js
function showThis() {
  console.log(this);
}
showThis(); // In non-strict mode: Window (global object), in strict mode: undefined
```

- It can be used in any function, but its value depends on how the function is called.

```js
let user = {
  name: "Alice",
  showThis: function() {
    console.log(this);
  }
};
user.showThis(); // Output: user object (this refers to the user object)
let show = user.showThis;
show(); // In non-strict mode: Window (global object), in strict mode: undefined
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **3. Arrow Functions and `this`**

- Arrow functions do not have their own `this` context; they inherit `this` from the surrounding scope.

```js
let user = {
  name: "Alice",
  showThis: () => {
    console.log(this); // In this case, `this` refers to the global object (or undefined in strict mode) because arrow functions do not have their own `this`.
  }
};
user.showThis(); // Output: Window (global object) or undefined (in strict mode)
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)