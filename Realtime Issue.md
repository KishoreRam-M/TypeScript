### 1. **What’s happening**

```ts
const productIds: number[] = [1, 2, "3"]; // ❌ compile-time error
```

* `productIds: number[]` tells TypeScript:

  > “This array **must only contain numbers**.”

* When we try to add `"3"` (a string), TS sees a **type mismatch**:

  * Expected: `number`
  * Found: `string`

TypeScript immediately **throws a compile-time error**, even before the code runs.

---

### 2. **Why it matters in real projects**

Imagine a large e-commerce app:

```ts
const productIds: number[] = [101, 102, "103", 104]; // some IDs accidentally as strings
processProducts(productIds);
```

* Without TypeScript (plain JS), this runs, but functions like `processProducts` might fail or behave unexpectedly when they try to do arithmetic or database queries with `"103"` instead of `103`.
* **Runtime errors** appear only when the code executes → hard to debug.

With TypeScript:

* Error appears **during development**
* You catch mistakes **before deployment**
* The code is safer and more predictable

---

### 3. **How TypeScript keeps arrays safe**

* `number[]` → array of numbers only
* `string[]` → array of strings only
* You **cannot mix types** unless you explicitly allow it:

```ts
const mixed: (number | string)[] = [1, 2, "3"]; // ✅ allowed
```

* Here, TypeScript knows the array can contain **numbers or strings**, so no error.

---

### ✅ Real-World Analogy

Think of it like **a basket labeled “Apples Only”**:

* `number[]` → “Numbers only” basket
* Adding `"3"` → like putting an orange in an apple-only basket → TypeScript yells at you before the basket leaves the store.
