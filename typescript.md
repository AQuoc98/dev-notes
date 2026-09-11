# TypeScript Interview Questions & Answers

> A short collection of commonly asked TypeScript interview questions and answers.

---

### Table of Contents

<!-- TOC_START -->

<table>
  <thead>
    <tr><th>No.</th><th>Questions</th></tr>
  </thead>
  <tbody>
    <tr><td>1</td><td><a href="#what-is-static-type-checking-in-typescript-and-how-does-it-differ-from-javascripts-dynamic-typing">What is Static Type Checking in TypeScript, and how does it differ from JavaScript's Dynamic Typing?</a></td></tr>
    <tr><td>2</td><td><a href="#what-is-the-difference-between-explicit-types-and-inferred-types-in-typescript">What is the difference between Explicit types and Inferred types in TypeScript?</a></td></tr>
    <tr><td>3</td><td><a href="#what-does-type-erasure-mean-in-the-context-of-typescript-compilation">What does "Type Erasure" mean in the context of TypeScript compilation?</a></td></tr>
    <tr><td>4</td><td><a href="#what-is-downleveling-in-typescript-and-how-is-it-configured">What is "Downleveling" in TypeScript, and how is it configured?</a></td></tr>
    <tr><td>5</td><td><a href="#what-is-the-purpose-of-noemitonerror-in-tsconfigjson">What is the purpose of noEmitOnError in tsconfig.json?</a></td></tr>
  </tbody>
</table>

<!-- TOC_END -->

### What is Static Type Checking in TypeScript, and how does it differ from JavaScript's Dynamic Typing?

<details><summary><b>Answer</b></summary>
<p>

Static type checking means that types are checked at **compile-time** before the code runs. TypeScript uses static type checking to catch type-related errors (like calling an undefined method or misspelled property names) directly in the editor during development. In contrast, JavaScript is **dynamically typed**, meaning type checking happens at **runtime**, which can lead to unexpected crashes or bugs when executing the code.

</p>
</details>

### What is the difference between Explicit types and Inferred types in TypeScript?

<details><summary><b>Answer</b></summary>
<p>

Explicit typing occurs when a developer manually specifies the type of a value using a type annotation:

~~~ts
let count: number = 10;
~~~

Inferred typing allows TypeScript to determine the type automatically from the assigned value:

~~~ts
let count = 10; // inferred as number
~~~

Explicit types are useful for uninitialized variables, function parameters, public APIs, and cases where the intended type should be made clear. Inference usually keeps simple code cleaner when the type is obvious from the assigned value.

</p>
</details>

### What does "Type Erasure" mean in the context of TypeScript compilation?

<details><summary><b>Answer</b></summary>
<p>

Type erasure is the process where TypeScript removes constructs that are used only for compile-time type checking when producing JavaScript. Type annotations, interfaces, type aliases, generics, and type assertions do not exist in the emitted JavaScript because JavaScript engines do not understand TypeScript's type syntax.

~~~ts
interface User {
  name: string;
}

const user: User = { name: "Ada" };
~~~

The emitted JavaScript contains the runtime value but not the interface or type annotation:

~~~js
const user = { name: "Ada" };
~~~

Because these types are erased, TypeScript does not provide runtime validation by itself.

</p>
</details>

### What is "Downleveling" in TypeScript, and how is it configured?

<details><summary><b>Answer</b></summary>
<p>

Downleveling is the process of compiling newer JavaScript syntax into an older ECMAScript version so the output can run in older environments. For example, TypeScript can transform features such as arrow functions and template literals into syntax supported by an older target such as ES5.

This behavior is configured with the `target` option in `tsconfig.json`:

~~~json
{
  "compilerOptions": {
    "target": "ES5"
  }
}
~~~

The `target` option controls syntax transformation, but it does not automatically add polyfills for missing runtime APIs. Polyfills may still be required depending on the target environment and the APIs used by the application.

</p>
</details>

### What is the purpose of `noEmitOnError` in `tsconfig.json`?

<details><summary><b>Answer</b></summary>
<p>

The `noEmitOnError` setting controls whether TypeScript generates output JavaScript files when there are compilation errors. When set to `true`, the TypeScript compiler (`tsc`) will **not emit/generate JavaScript code** if any type checking or syntax errors exist. This ensures that broken code or invalid JavaScript is not produced for testing or production builds.

</p>
</details>

## Disclaimer

The questions in this document are examples of commonly discussed TypeScript topics. They are intended as a study guide and do not guarantee what will be asked in a particular interview.

Good luck with your interview 😊
