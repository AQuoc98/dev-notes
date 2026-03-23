# ⚛️ ReactJS Advanced Patterns & Interview Questions

**Status:** 🚧 - **Last Updated:** 23th March 2026

---

### Q1: How does the Virtual DOM work and what is "Reconciliation"?
**Answer:**
The **Virtual DOM (VDOM)** is a lightweight copy of the real DOM.
1. When state changes, React creates a new VDOM tree.
2. **Diffing Algorithm:** React compares the new VDOM with the previous one.
3. **Reconciliation:** Instead of re-rendering the entire UI, React only updates the specific nodes that changed in the real DOM. This minimizes expensive DOM operations.

### Q2: Explain the difference between `useMemo`, `useCallback`, and `React.memo`.
**Answer:**
- **`React.memo`:** A Higher-Order Component that prevents a functional component from re-rendering if its *props* haven't changed.
- **`useMemo`:** Memoizes a **computed value** to avoid expensive recalculations on every render.
- **`useCallback`:** Memoizes a **function instance** to prevent child components from re-rendering when a function is passed as a prop.

### Q3: What are the main differences between `useEffect` and `useLayoutEffect`?
**Answer:**
- **`useEffect`:** Runs **asynchronously** after the render is committed to the screen and the browser has painted. This is the standard choice for data fetching or event listeners.
- **`useLayoutEffect`:** Runs **synchronously** immediately after React performs all DOM mutations but *before* the browser paints. Use this only when you need to measure DOM elements (like tooltip positioning) to prevent visual flickering.

---

### 🔥 Senior Tip:
When answering these, always mention **Real-world Performance**. For example, explain how you used `useCallback` to optimize a heavy list or how the Event Loop knowledge helped you debug a race condition in an API call.