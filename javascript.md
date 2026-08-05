# Typescript Interview Questions

## Interview Questions

<table>
  <thead>
    <tr>
      <th>No.</th>
      <th>Questions</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>1</td>
      <td><a href="#what-is-the-difference-between-undefined-and-not-defined-in-javascript">What is the difference between undefined and not defined in JavaScript?</a></td>
    </tr>
    <tr>
      <td>2</td>
      <td><a href="#what-is-the-difference-between-var-let-and-const-in-javascript">What is the difference between var, let, and const in JavaScript?</a></td>
    </tr>
  </tbody>
</table>

### What is the difference between undefined and not defined in JavaScript?

**undefined**

- This is a primitive value that indicates that a variable has been declared but has not been assigned a value.

**not defined**

- This occurs when you try to access a variable that has not been declared at all in the current scope. It results in a ReferenceError.

**Examples:**

```javascript
let a;
console.log(a); // Output: undefined
console.log(b); // ReferenceError: b is not defined
```

[↑ Back to top](#table-of-contents)

### What is the difference between var, let, and const in JavaScript?


| Point | `var` | `let` | `const` |
| --- | --- | --- | --- |
| Scope | Function-scoped | Block-scoped | Block-scoped |
| Redeclaration | Allowed in the same scope | Not allowed in the same scope | Not allowed in the same scope |
| Reassignment | Allowed | Allowed | Not allowed |
| Hoisting | Hoisted and initialized with `undefined` | Hoisted but stays in temporal dead zone | Hoisted but stays in temporal dead zone |
| Initialization required | Optional at declaration | Optional at declaration | Required at declaration |
| Global object behavior | Becomes a property of `window` when declared globally in browsers | Does not become a property of `window` | Does not become a property of `window` |
| Best use case | Older code, but generally avoided in modern JavaScript | Use when value may change | Use when value should not be reassigned |

**Examples**

**1. Scope**

```javascript
if (true) {
  var a = 1;
  let b = 2;
  const c = 3;
}

console.log(a); // 1
console.log(b); // ReferenceError
console.log(c); // ReferenceError
```

**2. Redeclaration**

Redeclaration means declaring the same variable name again in the same scope using a declaration keyword such as `var`, `let`, or `const`.

- `var` allows redeclaration in the same scope.
- `let` does not allow redeclaration in the same scope.
- `const` does not allow redeclaration in the same scope.

```javascript
var x = 10;
var x = 20; // Allowed

let y = 10;
let y = 20; // SyntaxError

const z = 10;
const z = 20; // SyntaxError
```

**3. Reassignment**

Reassignment means giving a new value to a variable after it has already been declared.

- `var` allows reassignment.
- `let` allows reassignment.
- `const` does not allow reassignment because its assigned reference must stay the same.

```javascript
var a = 1;
a = 2; // Allowed

let b = 1;
b = 2; // Allowed

const c = 1;
c = 2; // TypeError
```

**4. Hoisting**

```javascript
console.log(a); // undefined
var a = 5;

console.log(b); // ReferenceError
let b = 5;

console.log(c); // ReferenceError
const c = 5;
```

**5. Initialization required**

```javascript
var a;
let b;
const c; // SyntaxError: Missing initializer in const declaration
```

**6. Global object behavior**

```javascript
var a = 1;
let b = 2;
const c = 3;

console.log(window.a); // 1
console.log(window.b); // undefined
console.log(window.c); // undefined
```

**7. Best use case**

```javascript
let counter = 0;
counter++;

const API_URL = 'https://example.com';
```

**Important note about `const`:**

`const` prevents reassignment, but it does not make objects or arrays immutable.

```javascript
const user = { name: 'Ken' };
user.name = 'Alex'; // Allowed
console.log(user.name); // Alex

user = { name: 'Sam' }; // TypeError
```

**Quick summary:**

- Use `const` by default.
- Use `let` when the value needs to change.
- Avoid `var` in modern JavaScript unless you are working with older codebases.
