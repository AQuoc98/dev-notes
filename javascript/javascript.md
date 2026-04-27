# 🟡 JavaScript Deep Dive & Interview Questions

**Status:** 🚧 - **Last Updated:** 27th Apr 2026


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
    - [✅ **5. Array**](#-5-array)
    - [✅ **6. Map**](#-6-map)
    - [✅ **7. Set**](#-7-set)
    - [✅ **8. Date \& Time**](#-8-date--time)
    - [✅ **9. JSON**](#-9-json)
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
    - [✅ **4. Variable scope, Closures**](#-4-variable-scope-closures)
    - [✅ **5. Callback Functions**](#-5-callback-functions)
    - [✅ **6. Recursive Functions**](#-6-recursive-functions)
    - [✅ **7. Rest parameters and spread syntax**](#-7-rest-parameters-and-spread-syntax)
    - [✅ **8. setInterval and setTimeout**](#-8-setinterval-and-settimeout)
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
    - [✅ **4. Function `this` binding**](#-4-function-this-binding)
  - [Class](#class)
    - [✅ **1. Syntax**](#-1-syntax)
    - [✅ **2. Class Inheritance**](#-2-class-inheritance)
    - [✅ **3. Static properties and methods**](#-3-static-properties-and-methods)
    - [✅ **4. Private and protected properties and methods**](#-4-private-and-protected-properties-and-methods)
  - [Error Handling](#error-handling)
    - [✅ **1. Try...catch...finally**](#-1-trycatchfinally)
    - [✅ **2. Custom Error Classes**](#-2-custom-error-classes)
  - [Asynchronous Programming](#asynchronous-programming)
    - [✅ **1. JS FLow**](#-1-js-flow)
    - [✅ **2. Callbacks**](#-2-callbacks)
    - [✅ **3. Promises**](#-3-promises)
    - [✅ **4. Promise API**](#-4-promise-api)
    - [✅ **5. Async/Await**](#-5-asyncawait)
  - [Export and Import](#export-and-import)
    - [✅ **1. Named Export**](#-1-named-export)
    - [✅ \*\*2. Import `*` \*\*](#-2-import--)
    - [✅ **3. Default Export**](#-3-default-export)
  - [Interview Questions](#interview-questions)

## The Modern Mode: "use strict"

### ✅ **1. What is Strict Mode in JavaScript and why should we use it?**

* Strict Mode is a way to opt into a restricted version of JavaScript by adding `"use strict";` at the top of a file or function.

* It helps developers write safer, cleaner, and more predictable code by:
  * Preventing accidental global variables
  * Throwing errors for silent bugs (e.g., assigning to non-writable properties)
  * Disallowing duplicate parameter names
  * Making `this` behavior more predictable
  * Restricting some unsafe or deprecated features

```js
"use strict";

x = 10; // ❌ ReferenceError (instead of creating global variable)
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

---

### ✅ **2. How does `this` behave differently in Strict Mode?**

* Non-strict mode: `this` defaults to `window` (or global object)

```js
function test() {
  console.log(this);
}

test(); // window (in browser)
```

* Strict mode: `this` is `undefined` unless explicitly set**

```js
"use strict";

function test() {
  console.log(this);
}

test(); // undefined
```

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

* `let` and `const` exist in a TDZ from the start of the block until declaration.

**3. `const` is NOT immutable**

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

**There are 8 basic data types in JavaScript.**

* Seven primitive data types:
  * `number` for numbers of any kind: integer or floating-point, integers are limited by ±(253-1).
  * `bigint` for integer numbers of arbitrary length.
  * `string` for strings. A string may have zero or more characters, there’s no separate single-character type.
  * `boolean` for true/false.
  * `null` for unknown values – a standalone type that has a single value null.
  * `undefined` for unassigned values – a standalone type that has a single value undefined.
  * `symbol` for unique identifiers.

* one non-primitive data type:
  * `object` for more complex data structures.

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **2. How to see which type is stored in variable?**

**The `typeof` operator allows us to see which type is stored in a variable.**

* Returns a string with the name of the type, like "string".
* For null returns "object" – this is an error in the language, it’s not actually an object.

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **3. Number**

* The `number` type represents both integer and floating point numbers.

* `NaN` represents a computational error

```js
console.log("not a number" / 2); // NaN, such division is erroneous
console.log(NaN + 1); // NaN
console.log(3 * NaN); // NaN
```

* The common methods for numbers: [number methods](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number), [math methods](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math)

* Why `0.1 + 0.2 != 0.3` --> Due to the way numbers are represented in binary

```js
console.log(0.1 + 0.2); // 0.30000000000000004
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **4. String**

* In JavaScript, there are 3 types of quotes.
```js
// Double quotes
let str1 = "Hello";

// Single quotes
let str2 = 'Hello';

// Backticks string (template literals)
let str3 = `Hello`;

// Use template literals for multi-line strings and string interpolation
let name = "Alice";
let greeting = `Hello, ${name}!`; // "Hello, Alice!"
```

* Special characters in strings can be escaped using a backslash `\`.
```js
let str = "Hello\nWorld"; // Newline character
console.log(str);
```

* Other special characters include: `\n` (newline), `\t` (tab), `\\` (backslash), `\'` (single quote), `\"` (double quote).

* Common string methods: [string methods](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String#methods)

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **5. Array**

* Syntax for creating an array:
```js
let arr = [1, 2, 3];

// Or using the Array constructor
let arr2 = new Array(1, 2, 3);
```

* Get element by index:
```js
console.log(arr[0]); // 1
console.log(arr[1]); // 2
console.log(arr[2]); // 3
```

* Arrays are zero-indexed, meaning the first element is at index 0.

* Length of an array:
```js
console.log(arr.length); // 3
```

* Arrays can hold any type of data, including mixed types:
```js
let mixedArr = [1, "two", true, null, { name: "Alice" }, [3, 4]];
```

* How to compare arrays: Arrays are reference types, so comparing them with `==` or `===` checks if they reference the same object, not if their contents are equal. To compare contents, you can use methods like `JSON.stringify()` or a `deep comparison function`.

```js
let arr1 = [1, 2, 3];
let arr2 = [1, 2, 3];
console.log(arr1 === arr2); // false (different references)
console.log(JSON.stringify(arr1) === JSON.stringify(arr2)); // true (same contents)
```

```js
function arraysEqual(a, b) {
  if (a.length !== b.length) return false;
  for (let i = 0; i < a.length; i++) {
    if (a[i] !== b[i]) return false;
  }
  return true;
}
console.log(arraysEqual(arr1, arr2)); // true
```

* Array destructuring:
```js
let [first, second, third] = arr;
console.log(first); // 1
console.log(second); // 2
console.log(third); // 3
```

* The rest operator `...` can be used to collect the remaining elements into an array:
```js
let [first, ...rest] = arr;
console.log(first); // 1
console.log(rest); // [2, 3]
```

* Common array methods: [array methods](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array#methods)

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **6. Map**

* A `Map` is a collection of key-value pairs where keys can be of any type.
```js
// Creating a Map
let myMap = new Map();

// Store the value by the key
myMap.set("1", "str1");
myMap.set(true, "bool1");
myMap.set(1, "num1");

// Check the size of the Map
console.log(myMap.size); // 3

// Get the value by the key. Undefined is returned if the key doesn’t exist in the map
console.log(myMap.get("1")); // "str1"
console.log(myMap.get("2")); // undefined

// Check if a key exists
console.log(myMap.has("1")); // true

// Delete a key-value pair
myMap.delete("1");

// Remove all key-value pairs
myMap.clear();
```

* Use objects as keys in a Map:
```js
let objKey = { id: 1 };

let myMap = new Map();

myMap.set(objKey, "Object Value");

console.log(myMap.get(objKey)); // "Object Value"
```

* Common Map methods: [map methods](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map#methods)

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **7. Set**

* A `Set` is a collection of unique values. It can store any type of value, whether primitive or object references.
```js
// Creating a Set
let mySet = new Set();

// Add values to the Set
mySet.add(1);
mySet.add(5);
mySet.add(5); // Duplicate value, will be ignored
mySet.add("some text");
mySet.add({ a: 1, b: 2 }); // Object reference

// Check the size of the Set
console.log(mySet.size); // 4

// Check if a value exists in the Set
console.log(mySet.has(1)); // true
console.log(mySet.has(3)); // false

// Delete a value from the Set
mySet.delete(5);

// Remove all values from the Set
mySet.clear();
```
* Common Set methods: [set methods](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Set#methods)

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **8. Date & Time**

* Common Date methods: [date methods](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date#methods)

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **9. JSON**

* JSON.stringify() converts a JavaScript object or value to a JSON string.
```js
let obj = { name: "Alice", age: 30 };
let jsonString = JSON.stringify(obj);
console.log(jsonString); // '{"name":"Alice","age":30}'
```

* JSON.parse() parses a JSON string, constructing the JavaScript value or object described by the string.
```js
let jsonString = '{"name":"Alice","age":30}';
let obj = JSON.parse(jsonString);
console.log(obj); // { name: "Alice", age: 30 }
```

* toJSON() method of an object is called by JSON.stringify() when converting the object to a JSON string. It allows you to customize the JSON representation of an object.
```js
let user = {
  name: "Alice",
  age: 30,
  toJSON() {
    return { name: this.name }; // Only include the name in the JSON output
  }
};
let jsonString = JSON.stringify(user);
console.log(jsonString); // '{"name":"Alice"}'
```

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

* Numeric conversion in `mathematical functions` and `expressions` happens automatically.
```js
console.log( "6" / "2" ); // 3
console.log( "6" * "2" ); // 12
console.log( "6" - "2" ); // 4
console.log( "6" % "2" ); // 0
```

* Use `Number()` function to explicitly convert a value to a number
```js
let str = "123";
console.log(typeof str); // 'string'

let num = Number(str); // becomes a number 123
console.log(typeof num); // 'number'
```

* Number conversion rules
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
* `0`, `null`, `undefined`, `NaN`, and `""` (empty string) become `false`.
* All other values become `true`.

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

* **Operand:** An operand is a value or variable on which an operator acts.
  * In the expression `5 + 3`, the numbers `5` and `3` are operands, and `+` is the operator.
  * In `x++`, `x` is the operand, `++` is the operator.

* **Unary operator:** Operates on a single operand.
```js
let x = 1;

x = -x;
alert( x ); // -1, unary negation was applied
```

* **Binary operator:** Operates on two operands.
```js
let x = 1, y = 3;
alert( y - x ); // 2, binary minus subtracts values
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **2. Maths**

 * `+` Addition
 * `-` Subtraction
 * `*` Multiplication
 * `/` Division
 * `%` Remainder: The result of a % b is the remainder of the integer division of a by b.

```js
console.log( 5 % 2 ); // 1, the remainder of 5 divided by 2
console.log( 8 % 3 ); // 2, the remainder of 8 divided by 3
console.log( 8 % 4 ); // 0, the remainder of 8 divided by 4
```
 * `**` Exponentiation: a ** b is a to the power of b, i.e., a multiplied by itself b times.

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

* Do not have their own `this`, `arguments`, `super`, or `new.target` bindings. They are best suited for non-method functions, and they cannot be used as constructors.

```js
const greet = (name) => {
  return "Hello, " + name + "!";
};
console.log(greet("Charlie")); // Output: "Hello, Charlie!"
```

```js
const greet = name => "Hello, " + name + "!";
console.log(greet("Dave")); // Output: "Hello, Dave!"
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **4. Variable scope, Closures**

* Variables declared inside a function are local to that function and cannot be accessed outside of it.

```js
function myFunction() {
  let localVar = "I am local";
  console.log(localVar); // Output: "I am local"
}
myFunction();
console.log(localVar); // ❌ ReferenceError: localVar is not defined
```

* Variables declared outside of any function are global and can be accessed from anywhere in the code.

```js
let globalVar = "I am global";
function myFunction() {
  console.log(globalVar); // Output: "I am global"
}
myFunction();
console.log(globalVar); // Output: "I am global"
```

* Closures are created when a function is defined inside another function and has access to the outer function's variables.

```js
function outerFunction() {
  let outerVar = "I am from the outer function";
  function innerFunction() {
    console.log(outerVar); // Output: "I am from the outer function"
  }
  return innerFunction;
}
const closure = outerFunction();
closure(); // Output: "I am from the outer function"
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **5. Callback Functions**

* A callback function is a function that is passed as an argument to another function and is executed after some operation has been completed.

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

### ✅ **6. Recursive Functions**

* A recursive function is a function that calls itself in order to solve a problem.

```js
function factorial(n) {
  if (n === 0) {
    return 1; // Base case: factorial of 0 is 1
  }
  return n * factorial(n - 1); // Recursive case: n! = n * (n - 1)!
}
console.log(factorial(5)); // Output: 120
```

* Recursive functions must have a base case to prevent infinite recursion.

```js
function countDown(n) {
  if (n <= 0) {
    console.log("Done!"); // Base case: when n is 0 or negative
    return;
  }
  console.log(n); // Recursive case: print n and call countDown with n - 1
  countDown(n - 1);
}
countDown(5); // Output: 5, 4, 3, 2, 1, "Done!"
```

* Tail recursion is a special case of recursion where the recursive call is the last operation in the function. Tail recursion can be optimized by the JavaScript engine to prevent stack overflow.

```js
function tailRecursiveFactorial(n, acc = 1) {
  if (n === 0) {
    return acc; // Base case: return the accumulated result
  }
  return tailRecursiveFactorial(n - 1, n * acc); // Recursive case: pass the accumulated result
}
console.log(tailRecursiveFactorial(5)); // Output: 120
```

* Iterative solutions can often be used as an alternative to recursion, especially when dealing with large inputs that may cause stack overflow.

```js
function iterativeFactorial(n) {
  let result = 1;
  for (let i = 1; i <= n; i++) {
    result *= i; // Multiply result by i for each number from 1 to n
  }
  return result;
}
console.log(iterativeFactorial(5)); // Output: 120
```

* Recursive functions can be more elegant and easier to understand for certain problems, such as tree traversal or combinatorial problems, while iterative solutions may be more efficient for simple loops or when performance is a concern.

* When using recursion, it's important to consider the maximum call stack size and potential performance issues, especially for deep recursion. Tail recursion optimization can help mitigate these issues in some cases.

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **7. Rest parameters and spread syntax**

* Rest parameters allow a function to accept an indefinite number of arguments as an array.

```js
function sum(...numbers) {
  return numbers.reduce((total, num) => total + num, 0); // Sum all numbers in the array
}
console.log(sum(1, 2, 3)); // Output: 6
console.log(sum(4, 5)); // Output: 9
```

* Spread syntax allows an iterable (like an array) to be expanded in places where zero or more arguments are expected.

```js
const arr1 = [1, 2, 3];
const arr2 = [4, 5, 6];
const combined = [...arr1, ...arr2]; // Combine two arrays using spread syntax
console.log(combined); // Output: [1, 2, 3, 4, 5, 6]
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **8. setInterval and setTimeout**

* `setTimeout` is used to execute a function after a specified delay.

```js
setTimeout(() => {
  console.log("This message is displayed after 2 seconds");
}, 2000);
```

* `setInterval` is used to execute a function repeatedly at specified intervals.

```js
const intervalId = setInterval(() => {
  console.log("This message is displayed every 3 seconds");
}, 3000);
// To stop the interval after some time
setTimeout(() => {
  clearInterval(intervalId);
  console.log("Interval stopped");
}, 10000); // Stop after 10 seconds
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

## Objects

### ✅ **1. The basics**

* An object literal is a comma-separated list of name-value pairs wrapped in curly braces.
```js
const person = {
  name: "Alice",
  age: 30,
  city: "New York"
};
```

* An Object can be created using the `new Object()` syntax, but object literals are more common and concise.
```js
let user = new Object(); // "object constructor" syntax
let user = {};  // "object literal" syntax
```

* Object properties can be accessed using dot notation or bracket notation.
```js
console.log(person.name); // Output: "Alice"
console.log(person["age"]); // Output: 30
```

* Properties can be `added`, `modified`, or `deleted` from an object.
```js
person.job = "Developer"; // Adding a new property
person.age = 31; // Modifying an existing property
delete person.city; // Deleting a property
console.log(person); // Output: { name: "Alice", age: 31, job: "Developer" }
```

* `Property value shorthand` allows you to create objects more concisely when the property name is the same as the variable name.
```js
let name = "Bob";
let age = 25;
let user = { name, age }; // Shorthand for { name: name, age: age }
console.log(user); // Output: { name: "Bob", age: 25 }
```

* Object destructuring allows you to extract properties from an object into variables.
```js
const person = { name: "Alice", age: 30, city: "New York" };
const { name, age } = person; // Destructuring assignment
console.log(name); // Output: "Alice"
console.log(age); // Output: 30
```
* The rest operator `...` can be used to collect the remaining properties into a new object.
```js
const person = { name: "Alice", age: 30, city: "New York" };
const { name, ...rest } = person; // Destructuring with rest operator
console.log(name); // Output: "Alice"
console.log(rest); // Output: { age: 30, city: "New York" }
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

* The `this` keyword in a method refers to the object it belongs to, allowing access to its properties and other methods.

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

* If a function is called without an object, `this` will refer to the global object (or be `undefined` in strict mode).

```js
function showThis() {
  console.log(this);
}
showThis(); // In non-strict mode: Window (global object), in strict mode: undefined
```

* It can be used in any function, but its value depends on how the function is called.

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

* Arrow functions do not have their own `this` context; they inherit `this` from the surrounding scope.

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

### ✅ **4. Function `this` binding**

* Solution 1: a wrapper

```js
let user = {
  name: "Alice",
  greet() {
    console.log("Hello, " + this.name + "!");
  }
};
setTimeout(function() {
  user.greet(); // Output: "Hello, Alice!" (using the wrapper to maintain the correct `this`)
}, 1000);
```

* Solution 2: `bind`

```js
let user = {
  name: "Alice",
  greet() {
    console.log("Hello, " + this.name + "!");
  }
};
setTimeout(user.greet.bind(user), 1000); // Output: "Hello, Alice!" (binding `this` to the user object)
```


[↑ Back to top](#-javascript-deep-dive--interview-questions)


## Class

### ✅ **1. Syntax**

* Basic class syntax:

```js
class User {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  greet() {
    console.log("Hello, " + this.name + "!");
  }
}

// Usage
const user1 = new User("Alice", 30);
user1.greet(); // Output: "Hello, Alice!"
```

* Class expressions

```js
const User = class {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  greet() {
    console.log("Hello, " + this.name + "!");
  }
};
const user2 = new User("Bob", 25);
user2.greet(); // Output: "Hello, Bob!"
```

* Getters and setters

```js
class User {
  constructor(name, age) {
    this.name = name;
    this.age = age;
  }
  get info() {
    return `${this.name} is ${this.age} years old.`;
  }
  set info(value) {
    const [name, age] = value.split(" is ");
    this.name = name;
    this.age = parseInt(age);
  }
}

const user3 = new User("Charlie", 35);
console.log(user3.info); // Output: "Charlie is 35 years old."
user3.info = "Dave is 40 years old.";
console.log(user3.info); // Output: "Dave is 40 years old."
```

* Computed names [...]

```js
class User {
  ['say' + 'Hi']() {
    alert("Hello");
  }
}

new User().sayHi(); // Output: "Hello"
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **2. Class Inheritance**

* Basic inheritance using `extends` and `super`

```js
class Animal {

  constructor(name) {
    this.speed = 0;
    this.name = name;
  }

  run(speed) {
    this.speed = speed;
    alert(`${this.name} runs with speed ${this.speed}.`);
  }

  stop() {
    this.speed = 0;
    alert(`${this.name} stands still.`);
  }

}

class Rabbit extends Animal {
  constructor(name, earLength) {
    super(name);
    this.earLength = earLength;
  }

  hide() {
    alert(`${this.name} hides!`);
  }

  stop() {
    super.stop(); // call parent stop
    this.hide(); // and then hide
  }
}

let rabbit = new Rabbit("White Rabbit", 10);

console.log(rabbit.name); // Output: "White Rabbit"
console.log(rabbit.earLength); // Output: 10

rabbit.run(5); // White Rabbit runs with speed 5.
rabbit.stop(); // White Rabbit stands still. White Rabbit hides!
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **3. Static properties and methods**

* Static methods are called on the class itself, not on instances of the class.

```js
class User {
  static compare(userA, userB) {
    return userA.age - userB.age; // Compare ages of two users
  }
}

const user1 = { name: "Alice", age: 30 };
const user2 = { name: "Bob", age: 25 };
console.log(User.compare(user1, user2)); // Output: 5 (user1 is older than user2)
```

* Static properties are properties that belong to the class itself, rather than to instances of the class.

```js
class User {
  static count = 0; // Static property to keep track of the number of users

  constructor(name) {
    this.name = name;
    User.count++; // Increment the count whenever a new user is created
  }
}

const user1 = new User("Alice");
const user2 = new User("Bob");
console.log(User.count); // Output: 2 (two users have been created)
```

* Inheritance of static methods and properties

```js
class Animal {
  static kingdom = "Animalia"; // Static property
  static classify() {
    return "This is an animal."; // Static method
  }
}

class Dog extends Animal {}

console.log(Dog.kingdom); // Output: "Animalia" (inherited static property)
console.log(Dog.classify()); // Output: "This is an animal." (inherited static method)
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **4. Private and protected properties and methods**

* In javascript, there are two types of object properties: public and private. Public properties can be accessed from outside the class, while private properties can only be accessed from within the class.

* Protected properties are usually prefixed with an underscore _

```js
class CoffeeMachine {
  _waterAmount = 0;

  set waterAmount(value) {
    if (value < 0) {
      value = 0;
    }
    this._waterAmount = value;
  }

  get waterAmount() {
    return this._waterAmount;
  }

  constructor(power) {
    this._power = power;
  }

}

// create the coffee machine
let coffeeMachine = new CoffeeMachine(100);

// add water
coffeeMachine.waterAmount = -10; // _waterAmount will become 0, not -10
```

```js
class CoffeeMachine {
  // ...

  constructor(power) {
    this._power = power;
  }

  get power() {
    return this._power;
  }

}

// create the coffee machine
let coffeeMachine = new CoffeeMachine(100);

alert(`Power is: ${coffeeMachine.power}W`); // Power is: 100W

coffeeMachine.power = 25; // Error (no setter)
```

* Private properties and methods can be defined using the `#` syntax, which makes them truly private and inaccessible from outside the class.

```js
class CoffeeMachine {

  #waterAmount = 0;

  get waterAmount() {
    return this.#waterAmount;
  }

  set waterAmount(value) {
    if (value < 0) value = 0;
    this.#waterAmount = value;
  }
}

let machine = new CoffeeMachine();

machine.waterAmount = 100;
alert(machine.#waterAmount); // Error
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

## Error Handling

### ✅ **1. Try...catch...finally**

* The `try...catch...finally` statement allows you to handle errors gracefully in your code.

```js
try {
   // run this code
  let result = riskyOperation();
  console.log(result);
} catch (error) {
  // if an error happened, then jump here
  // err is the error object
  console.error("An error occurred:", error);
} finally {
  // do in any case after try/catch
  console.log("This will always be executed.");
}
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **2. Custom Error Classes**

* Extending the built-in `Error` class allows you to create custom error types with specific properties and methods.
* Custom error classes can also include additional properties or methods to provide more context about the error.

```js
class ValidationError extends Error {
  constructor(message, field) {
    super(message); // Call the parent constructor with the message
    this.name = "ValidationError"; // Set the error name to the class name
    this.field = field; // Additional property to indicate which field caused the error
  }
}

function validateUser(user) {
  if (!user.name) {
    throw new ValidationError("Name is required", "name"); // Throw a custom error if validation fails
  }
  return true; // Return true if validation passes
}

try {
  validateUser({}); // This will throw a ValidationError
} catch (error) {
  if (error instanceof ValidationError) {
    console.error("Validation error:", error.message); // Handle validation errors specifically
    console.error("Field causing error:", error.field); // Log the field that caused the error
  } else {
    console.error("Unknown error:", error); // Handle other types of errors
  }
}
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

## Asynchronous Programming

### ✅ **1. JS FLow**

* https://jsflow.info/

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **2. Callbacks**

* A callback is a function passed as an argument to another function, which is then invoked inside the outer function to complete some kind of routine or action.

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

* Callbacks can lead to "**callback hell**" when there are multiple nested callbacks, making the code difficult to read and maintain.

```js
doSomething(function(result) {
  doSomethingElse(result, function(newResult) {
    doThirdThing(newResult, function(finalResult) {
      console.log("Got the final result: " + finalResult);
    });
  });
});
```

* **Handling errors** in callbacks can be tricky, as you need to check for errors at each level of the callback chain.

```js
function fetchData(callback) {
  setTimeout(() => {
    const error = null; // Simulate an error
    const data = "Data fetched";
    callback(error, data); // Pass error as the first argument
  }, 1000);
}

fetchData((error, result) => {
  if (error) {
    console.error("Error fetching data:", error); // Handle the error
  } else {
    console.log(result); // Output: "Data fetched" after 1 second
  }
});
```

### ✅ **3. Promises**

* A Promise is an object that represents the eventual completion (or failure) of an asynchronous operation and its resulting value.

```js
function fetchData() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      const data = "Data fetched";
      resolve(data); // Resolve the promise with the fetched data
    }, 1000);
  });
}

fetchData()
  .then(result => {
    console.log(result); // Output: "Data fetched" after 1 second
  })
  .catch(error => {
    console.error("Error fetching data:", error); // Handle any errors that occur
  })
  .finally(() => {
    console.log("Fetch attempt completed."); // This will always be executed
  })
```

* **Promise chaining** allows you to perform a sequence of asynchronous operations in a more readable and maintainable way.

```js
function fetchData() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      const data = "Data fetched";
      resolve(data); // Resolve the promise with the fetched data
    }, 1000);
  });
}

function processData(data) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      const processedData = "Processed data";
      resolve(processedData); // Resolve the promise with the processed data
    }, 1000);
  });
}

fetchData()
  .then(result => {
    console.log(result); // Output: "Data fetched"
    return processData(result); // Return a new promise for the next operation
  })
  .then(processedResult => {
    console.log(processedResult); // Output: "Processed data"
  })
  .catch(error => {
    console.error("Error:", error); // Handle any errors that occur in the chain
  });
```

* **Error handling** in promises is more straightforward, as you can use a single `.catch()` at the end of the chain to handle any errors that occur in any of the promises.

```js
fetchData()
  .then(result => {
    console.log(result); // Output: "Data fetched"
    return processData(result); // Return a new promise for the next operation
  })
  .then(processedResult => {
    console.log(processedResult); // Output: "Processed data"
  })
  .catch(error => {
    console.error("Error:", error); // Handle any errors that occur in the chain
  })
  .finally(() => {
    console.log("Fetch attempt completed."); // This will always be executed
  });
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **4. Promise API**

* **Promise.all()** waits for all promises to resolve and returns an array of their results. If any of the given promises rejects, it becomes the error of Promise.all, and all other results are ignored.

```js
const promise1 = Promise.resolve("First promise");
const promise2 = new Promise((resolve) => setTimeout(() => resolve("Second promise"), 1000));
const promise3 = Promise.resolve("Third promise");

Promise.all([promise1, promise2, promise3])
  .then(results => {
    console.log(results); // Output: ["First promise", "Second promise", "Third promise"]
  })
  .catch(error => {
    console.error("Error:", error); // Handle any errors that occur in any of the promises
  });
```

* **Promise.allSettled()** waits for all promises to settle (either fulfilled or rejected) and returns an array of their results.

```js
const promise1 = Promise.resolve("First promise");
const promise2 = new Promise((_, reject) => setTimeout(() => reject("Second promise failed"), 1000));
const promise3 = Promise.resolve("Third promise");

Promise.allSettled([promise1, promise2, promise3])
  .then(results => {
    console.log(results);
    // Output: [
    //   { status: "fulfilled", value: "First promise" },
    //   { status: "rejected", reason: "Second promise failed" },
    //   { status: "fulfilled", value: "Third promise" }
    // ]
  });
```

* **Promise.race()** returns a promise that resolves or rejects as soon as one of the promises in the iterable resolves or rejects.

```js
const promise1 = new Promise((resolve) => setTimeout(() => resolve("First promise"), 1000));
const promise2 = new Promise((resolve) => setTimeout(() => resolve("Second promise"), 500));
const promise3 = new Promise((resolve) => setTimeout(() => resolve("Third promise"), 1500));

Promise.race([promise1, promise2, promise3])
  .then(result => {
    console.log(result); // Output: "Second promise" (the fastest one to resolve)
  })
  .catch(error => {
    console.error("Error:", error); // Handle any errors that occur in the promises
  });
```

* **Promise.any()** returns a promise that resolves as soon as any of the promises in the iterable resolves, or rejects if all of the promises reject.

```js
const promise1 = new Promise((_, reject) => setTimeout(() => reject("First promise failed"), 1000));
const promise2 = new Promise((resolve) => setTimeout(() => resolve("Second promise"), 500));
const promise3 = new Promise((_, reject) => setTimeout(() => reject("Third promise failed"), 1500));

Promise.any([promise1, promise2, promise3])
  .then(result => {
    console.log(result); // Output: "Second promise" (the first one to resolve)
  })
  .catch(error => {
    console.error("All promises failed:", error); // Handle the case where all promises reject
  });
```

* **Promise.resolve() and Promise.reject()** are used to create promises that are already resolved or rejected.

```js
Promise.resolve("Resolved value").then(result => {
  console.log(result); // Output: "Resolved value"
});

Promise.reject("Rejected reason").catch(error => {
  console.error(error); // Output: "Rejected reason"
});
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **5. Async/Await**

* `async` functions allow you to write asynchronous code that looks synchronous, making it easier to read and maintain.
*  The `await` keyword can only be used inside an `async` function and is used to wait for a promise to resolve before proceeding with the execution of the code.
*  When using `async/await`, it's important to remember that the function will always return a promise, even if you return a non-promise value. This allows you to use `await` with any function, regardless of whether it is asynchronous or not.
*  Error handling in `async/await` can be done using `try...catch` blocks, allowing you to handle errors in a more straightforward way compared to promises.
*  The `finally` block can be used in `async/await` to execute code that should run regardless of whether an error occurred or not.

```js
async function fetchData() {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve("Data fetched");
    }, 1000);
  });
}

async function main() {
  try {
    const result = await fetchData(); // Wait for the promise to resolve
    console.log(result); // Output: "Data fetched"
  } catch (error) {
    console.error("Error fetching data:", error); // Handle any errors that occur
  } finally {
    console.log("Fetch attempt completed."); // This will always be executed
  }
}

main();
```

## Export and Import

### ✅ **1. Named Export**

* Named exports allow you to export multiple values from a module, and they can be imported using their specific names.

```js
// math.js
export const pi = 3.14159;
export function add(a, b) {
  return a + b;
}
export class Calculator {
  static multiply(a, b) {
    return a * b;
  }
}

// math2.js
const e = 2.71828;
function subtract(a, b) {
  return a - b;
}
export { e, subtract }; // Named export of existing variables and functions

// main.js
import { pi, add, Calculator } from './math.js';
import { e, subtract } from './math2.js';

console.log(pi); // Output: 3.14159
console.log(add(2, 3)); // Output: 5
console.log(Calculator.multiply(4, 5)); // Output: 20
console.log(e); // Output: 2.71828
console.log(subtract(5, 3)); // Output: 2
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **2. Import `*` **

* The `import * as` syntax allows you to import all exported values from a module as a single object.

```js
// math.js
export const pi = 3.14159;
export function add(a, b) {
  return a + b;
}
export class Calculator {
  static multiply(a, b) {
    return a * b;
  }
}

// main.js
import * as math from './math.js';

console.log(math.pi); // Output: 3.14159
console.log(math.add(2, 3)); // Output: 5
console.log(math.Calculator.multiply(4, 5)); // Output: 20
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

### ✅ **3. Default Export**

* A default export allows you to export a single value from a module, and it can be imported without using curly braces.

```js
// math.js
export default function square(x) {
  return x * x;
}

// math2.js
const cube = (x) => x * x * x;
export default cube; // Default export of an existing variable

// main.js
import square from './math.js';
import cube from './math2.js';
console.log(square(5)); // Output: 25
console.log(cube(3)); // Output: 27
```

[↑ Back to top](#-javascript-deep-dive--interview-questions)

## Interview Questions

[↑ Back to top](#-javascript-deep-dive--interview-questions)