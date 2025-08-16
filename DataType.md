## 1. **Primitive Types**

### `string`, `number`, `boolean`

Used everywhere—user input, API responses, configuration values.

```ts
// API response for user profile
interface User {
  id: number;
  name: string;
  email: string;
  isAdmin: boolean;
}

const user: User = {
  id: 101,
  name: "Alice",
  email: "alice@example.com",
  isAdmin: false,
};

// Using values safely
if (user.isAdmin) {
  console.log(`${user.name} has admin access`);
} else {
  console.log(`${user.name} is a regular user`);
}
```

**Why it matters:**

* TypeScript ensures `isAdmin` is **always boolean**, preventing bugs like `"false"` vs `false`.

---

### `null` & `undefined`

```ts
function getUserEmail(user: User | null): string {
  if (!user || !user.email) {
    return "No email available";
  }
  return user.email;
}

const userFromAPI = fetchUser(); // might return null
console.log(getUserEmail(userFromAPI));
```

* In real apps, API responses often return `null` or `undefined`.
* TypeScript forces you to **handle these cases explicitly**, avoiding runtime crashes.

---

## 2. **Complex Types**

### 2.1 **Object Types**

```ts
interface Product {
  id: number;
  name: string;
  price: number;
  tags?: string[]; // optional property
}

const product: Product = {
  id: 1,
  name: "Laptop",
  price: 1500,
  tags: ["electronics", "computers"],
};
```

* Objects with **typed keys** ensure your code won’t break if you misspell a property.
* Teams benefit from **self-documenting structures**.

---

### 2.2 **Arrays**

```ts
const productIds: number[] = [1, 2, 3, 4]; // Array of numbers
const users: User[] = [user];             // Array of user objects
```

* TypeScript prevents accidentally mixing types in arrays:

```ts
const productIds: number[] = [1, 2, "3"]; // ❌ compile-time error
```

---

### 2.3 **Tuple**

Use tuples when you need **fixed-length, fixed-type collections**, e.g., returning multiple values from a function.

```ts
function getCoordinates(): [number, number] {
  return [40.7128, -74.006]; // [latitude, longitude]
}

const coords = getCoordinates();
console.log(`Lat: ${coords[0]}, Long: ${coords[1]}`);
```

* Prevents errors like swapping latitude/longitude types.

---

### 2.4 **Enum**

Enums are great for **fixed sets of values**, like statuses, roles, or types.

```ts
enum OrderStatus {
  Pending = "PENDING",
  Shipped = "SHIPPED",
  Delivered = "DELIVERED",
}

interface Order {
  id: number;
  status: OrderStatus;
}

const order: Order = { id: 101, status: OrderStatus.Pending };

if (order.status === OrderStatus.Pending) {
  console.log("Order is being processed");
}
```

* Enums prevent **magic strings** and typos (`"Pendig"` ❌).

---

### 2.5 **any** & **unknown**

#### `any` → bypass type checking (avoid if possible)

```ts
let data: any = fetchFromLegacyAPI();
data = 42;
data = "string"; // ✅ no errors, but unsafe
```

* Only use `any` when **working with legacy JS or third-party data** temporarily.

#### `unknown` → safer alternative to `any`

```ts
let result: unknown = fetchFromAPI();
if (typeof result === "string") {
  console.log(result.toUpperCase()); // ✅ Safe
}
```

* Forces **type checking before use**, reducing runtime errors.

---

## 3. **Practical Patterns & Best Practices**

1. **Always type API responses**

```ts
interface Todo {
  id: number;
  title: string;
  completed: boolean;
}

const todos: Todo[] = await fetchTodos(); // TS ensures array of Todo
```

2. **Use `readonly` for immutables**

```ts
const tags: readonly string[] = ["typescript", "js"];
// tags.push("new"); ❌ prevents accidental mutation
```

3. **Prefer `unknown` over `any`** when consuming untyped data
4. **Use enums or union types** instead of magic strings

```ts
type UserRole = "admin" | "editor" | "viewer";

const role: UserRole = "admin"; // ❌ "superuser" would fail
```

5. **Always annotate variables in shared modules or function returns**

* Makes the code **self-documenting**, **safe**, and easier for teams.

---

### ✅ TL;DR – Real Project Mindset

* **Primitive types** → user data, flags, configs
* **Objects & arrays** → API responses, collections
* **Tuples** → fixed, ordered data
* **Enums & union types** → statuses, roles, states
* **unknown / any** → external data handling (use `unknown` when possible)
* **Type safety** → prevents runtime crashes, improves maintainability, makes onboarding new devs easie
