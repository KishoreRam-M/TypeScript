## 1. `let` → Block-Scoped Variables

**Key idea:** `let` is **block-scoped** (only accessible within `{}`) and is safe for most variable declarations.

**Why use it:** prevents accidental overwrites and scope bugs common in JS.

```ts
function calculateTotal(items: number[]) {
  let total = 0; // block-scoped, safe inside this function
  for (let i = 0; i < items.length; i++) {
    total += items[i];
  }
  // i is NOT accessible here
  return total;
}
```

**Scenario:** You’re looping through a list of products. Using `let` ensures your loop index **doesn’t leak outside** the loop.
Analogy: `let` = a private workspace in your function; it disappears when done.

---

## 2. `const` → Constants & Enforcing Immutability

**Key idea:** `const` prevents **reassignment**. The value cannot be reassigned, but **objects/arrays can still be mutated** (we’ll see that next).

```ts
const TAX_RATE: number = 0.1; // never changes
let subtotal = 200;
const total = subtotal + subtotal * TAX_RATE;
```

**Scenario:**

* Config values, API URLs, fixed business rules → **always use `const`**
* Prevents accidental changes in team projects, reducing subtle bugs.

**Tip:** If you want an object or array truly immutable, use **`readonly`** types.

```ts
const user = { id: 1, name: "Alice" };
user.name = "Bob"; // ✅ allowed
// user = { id: 2, name: "Eve" } ❌ not allowed
```

---

## 3. `var` → Function-Scoped, Avoid in Modern TS

**Key idea:** `var` is function-scoped and **hoisted**, which causes unpredictable behavior.

```ts
function testVar() {
  if (true) {
    var x = 10; // accessible outside the block
  }
  console.log(x); // 10 ✅ unexpected in modern code
}
```

**Why avoided:** can lead to **scope bugs**, especially in loops and async code.
**Best practice:** never use `var` in TypeScript projects; stick to `let` or `const`.

---

## 4. Type Annotations → Catch Bugs & Improve Readability

Type annotations explicitly tell TS what type a variable should hold.

```ts
let age: number = 21;        // number
let username: string = "Bob"; // string
let isAdmin: boolean = true;  // boolean
let scores: number[] = [10, 20, 30]; // array of numbers
let data: any;                // any type (avoid if possible)
```

**Why useful in projects:**

1. **Prevents wrong assignments**

```ts
let age: number = 21;
// age = "twenty-one"; ❌ Compile-time error
```

2. **Improves readability:** anyone reading your code knows what type of data to expect.
3. **Helps IDEs with autocompletion and hints**.

---

## 5. Practical Best Practices

| Practice                                                                            | Why it matters                           |
| ----------------------------------------------------------------------------------- | ---------------------------------------- |
| Prefer `const` by default                                                           | Values rarely change → safer, fewer bugs |
| Use `let` only when reassignment is needed                                          | Keeps code readable and predictable      |
| Avoid `var` completely                                                              | Prevents scope-related bugs              |
| Always use type annotations for **function parameters, API responses, and objects** | Prevents runtime surprises               |
| Use arrays & objects with explicit types                                            | Avoid `any`, improves maintainability    |

**Example in a real project scenario:**

```ts
// API response
interface Product {
  id: number;
  name: string;
  price: number;
}

const products: Product[] = fetchProducts(); // typed array
let totalPrice: number = 0;

for (const product of products) {
  totalPrice += product.price;
}

console.log(`Total: $${totalPrice}`);
```

* TS ensures `product.price` exists and is a number
* Team members immediately know **what data they’re working with**
* Bugs like `product.price` being undefined are caught **at compile time**

---

### ✅ TL;DR

1. `const` → default for immutables
2. `let` → only if you need reassignment
3. `var` → avoid
4. **Type annotations** → critical for safety, readability, and maintainability
5. In real projects, clear, typed variables = fewer runtime errors + easier teamwork
