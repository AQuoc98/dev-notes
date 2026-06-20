**Status:** 🚧 - **Last Updated:** 26th May 2026

#### Table of Contents
- [Interview Questions](#interview-questions)
  - [What is the difference between undefined and not defined in JavaScript?](#what-is-the-difference-between-undefined-and-not-defined-in-javascript)


## Interview Questions

| No. | Questions |
| --- | --------- |
| 1 | [What is the difference between undefined and not defined in JavaScript?](#what-is-the-difference-between-undefined-and-not-defined-in-javascript) |

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