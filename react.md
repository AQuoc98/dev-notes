# ⚛️ ReactJS Advanced Patterns & Interview Questions

**Status:** 🚧 - **Last Updated:** 27th March 2026

- [⚛️ ReactJS Advanced Patterns \& Interview Questions](#️-reactjs-advanced-patterns--interview-questions)
  - [Resources](#resources)
  - [✅ **1. What is the Virtual DOM and how does it improve performance?**](#-1-what-is-the-virtual-dom-and-how-does-it-improve-performance)

## Resources
* [React Official Docs](https://react.dev/)

## ✅ **1. What is the Virtual DOM and how does it improve performance?**

The **Virtual DOM (VDOM)** is a lightweight, in-memory JavaScript representation of the actual DOM. Each node is a plain object (a *React Element*) describing `type`, `props`, and `children` — no browser API calls, no layout, no paint. It allows React to efficiently update the UI by minimizing direct manipulations of the real DOM, which trigger expensive reflow and repaint.

**How it works:**
1. When a component's state or props change, React builds a new Virtual DOM tree.
2. React compares it with the previous tree using a **reconciliation** algorithm (O(n) heuristic) to find what changed.
3. It computes the minimal set of mutations needed to bring the real DOM in sync.
4. Those mutations are applied in a single synchronous **commit phase**, after multiple state updates have been batched together within the same tick.

**Why it improves performance:**

* **Batched state updates** — multiple `setState` calls in an event handler / `Promise` / `setTimeout` (since React 18) coalesce into one render + one commit, avoiding intermediate layout thrash.
* **Heuristic diffing — O(n) instead of O(n³)** — React assumes:
  * Different element `type` ⇒ replace the whole subtree (no deep compare).
  * Sibling lists are reconciled by **`key`** for stable identity across reorders.
* **Render / commit separation (Fiber, React 16+)** — each VDOM node is backed by a **Fiber** unit of work. The render phase is **pure and interruptible**; the scheduler yields to the browser so a long render never blocks input > 5 ms. Only the commit phase touches the DOM.
* **Concurrent rendering (React 18+)** — lanes + `startTransition` let high-priority updates (typing, hover) preempt low-priority ones (filtering a large list). Suspense and selective hydration build on the same model.
* **Bailouts** — when `React.memo` / `useState` see `Object.is`-equal props or state, React skips the subtree entirely. **Re-render ≠ re-paint**: an empty diff produces zero DOM work.

**Reconciliation by `key` — concrete example:**

```jsx
// Before
<ul>
  <li key="a">A</li>
  <li key="b">B</li>
</ul>

// After
<ul>
  <li key="b">B</li>
  <li key="a">A</li>
</ul>
```

* With stable `key`s React performs **2 moves**, preserving DOM nodes, focus, and local state.
* With **index-based** keys React mutates the text of every `<li>`, losing focus/selection and component state.

[↑ Back to top](#️-reactjs-advanced-patterns--interview-questions)

---