**Status:** 🚧 - **Last Updated:** 27th Apr 2026

# 🟡 Table of Contents

- [🟡 Table of Contents](#-table-of-contents)
  - [What is the difference between undefined and not defined in JavaScript?](#what-is-the-difference-between-undefined-and-not-defined-in-javascript)

## What is the difference between undefined and not defined in JavaScript?

- **`undefined`**: This is a primitive value that indicates that a variable has been declared but has not been assigned a value. For example:

```javascript
let a;
console.log(a); // Output: undefined
```

- **`not defined`**: This occurs when you try to access a variable that has not been declared at all in the current scope. For example:

```javascript
console.log(b); // ReferenceError: b is not defined
```