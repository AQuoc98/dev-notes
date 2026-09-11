# Technical Requirement Specification: `handleChangeNumberInput` Reusable Handler

## 1. Overview & Objective
The goal is to implement a reusable JavaScript/TypeScript blur handler function, `handleChangeNumberInput`, to sanitize and validate numeric values in text input fields. 

Instead of triggering validation on every keystroke (`onChange`), this function executes on the **`onBlur`** event (when the input field loses focus). It parses the user's raw input, applies constraints, and replaces the input content with a standardized numeric value.

---

## 2. Input Element Attributes & Constraints
Any target HTML `<input>` element using this handler will support/define the following attributes:

* **`type`**: Required to be `"text"`.
* **`min`**: Minimum numeric threshold (*optional, default value is `0`*).
* **`max`**: Maximum numeric threshold (*optional, unbounded if omitted*).

---

## 3. Business Logic & Processing Rules

When the `onBlur` event fires, the handler extracts the `rawValue` and evaluates it against the following rules in strict order of priority:

1. **Empty Input**:
   * If `rawValue` is empty (`""`) or consists solely of whitespace $ightarrow$ Return **`min`**.
2. **Leading Text / Invalid Non-Numeric Format**:
   * If the input starts with alphabetic or special characters (excluding a valid negative sign `"-"` at index 0) $ightarrow$ Return **`min`**.
3. **Trailing Non-Numeric Characters (e.g., `"12abc"`, `"150xyz"`)**:
   * Parse the leading numeric portion ($N$) before the trailing text:
     * If $N > 	ext{max}$ $ightarrow$ Return **`max`**.
     * If $N \le 	ext{max}$ $ightarrow$ Return **$N$**.
4. **Boundary Limit Checks**:
   * If $N < 	ext{min}$ $ightarrow$ Return **`min`**.
   * If $N > 	ext{max}$ (when `max` is defined) $ightarrow$ Return **`max`**.
   * If $	ext{min} \le N \le 	ext{max}$ $ightarrow$ Return **$N$**.

---

## 4. Test Matrix & Boundary Examples

Below is the expected output matrix assuming default conditions: **`min = 0`** and **`max = 100`**.

| Raw Input (`onBlur`) | Input Classification | Calculated Value | Explanation / Rule Triggered |
| :--- | :--- | :--- | :--- |
| `""` | Empty String | `0` | Empty input resets to `min`. |
| `"   "` | Whitespace | `0` | Whitespace trimmed to empty, resets to `min`. |
| `"abc"` | Pure Text | `0` | Invalid text resets to `min`. |
| `"abc50"` | Leading Text | `0` | Text preceding numbers resets to `min`. |
| `"-5"` | Out of Bounds (Below Min) | `0` | Less than `min` (0) $ightarrow$ Returns `min`. |
| `"50"` | Valid Number | `50` | Within range $[0, 100]$. |
| `"150"` | Out of Bounds (Above Max) | `100` | Exceeds `max` (100) $ightarrow$ Returns `max`. |
| `"150abc"` | Trailing Text + Over Max | `100` | Extracted number $150 > 100 ightarrow$ Returns `max`. |
| `"50abc"` | Trailing Text + Valid Num | `50` | Extracted number $50 \le 100 ightarrow$ Returns `50`. |
| `"-20abc"` | Trailing Text + Negative | `0` | Extracted number $-20 < 0 ightarrow$ Returns `min`. |

---

## 5. Reference Implementation Concept (TypeScript)

```typescript
export interface NumberInputConfig {
  min?: number;
  max?: number;
}

export const sanitizeNumberInput = (rawValue: string, config?: NumberInputConfig): number => {
  const min = config?.min ?? 0;
  const max = config?.max;

  const trimmed = rawValue.trim();
  if (!trimmed) return min;

  // Match leading optional minus sign followed by digits
  const match = trimmed.match(/^-?\d+/);
  if (!match) return min;

  const parsedNumber = parseInt(match[0], 10);

  if (parsedNumber < min) return min;
  if (max !== undefined && parsedNumber > max) return max;

  return parsedNumber;
};
```
