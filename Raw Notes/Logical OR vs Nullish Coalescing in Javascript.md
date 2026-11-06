---
tags:
  - languages/typescript
  - ksi
  - best-practices
  - tips
---

---

- **`||` (Logical OR):** Falls back to the right-hand side if the left-hand side is **any falsy value** (`null`, `undefined`, `""`, `0`, `false`, `NaN`).

- **`??` (Nullish Coalescing):** Falls back to the right-hand side **only** if the left-hand side is `null` or `undefined`.

### Summary rule of thumb

- Use **`??`** when you want to provide a **default value** only if the property was omitted (`undefined`) or explicitly empty (`null`).
    
- Use **`||`** only when you intentionally want to reject empty strings `""`, `0`, or `false` in addition to `null` and `undefined`.