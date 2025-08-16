# 1. How TypeScript → JavaScript works (short foundation)

**Analogy:** TypeScript is a static safety checker and spec writer. It annotates your code and validates it at *compile-time*. When you build, the type annotations are removed; the runtime sees normal JavaScript.

* **What’s erased:** `: types`, `interface`, `type`, generics, most `enum` metadata (except emitted runtime code for regular enums).
* **What remains/emits code at runtime:** functions, classes, object shapes, `const enum` can be inlined, `enum` (non-const) emits a JS object, decorator metadata (if enabled) emits runtime code.
* **Target & module config matters:** `target` determines whether async/await, classes, and newer syntax are transpiled; `module` determines import/require output.

**Quick compiled example (enum reverse mapping):**

```ts
// typescript
enum OrderStatus { Pending = 0, Shipped = 1 }
const s = OrderStatus.Shipped; // 1
```

becomes (simplified):

```js
// emitted JS
var OrderStatus;
(function (OrderStatus) {
  OrderStatus[OrderStatus["Pending"] = 0] = "Pending";
  OrderStatus[OrderStatus["Shipped"] = 1] = "Shipped";
})(OrderStatus || (OrderStatus = {}));
const s = OrderStatus.Shipped; // 1
```

> numeric enums produce that JS object (hence reverse mapping). `const enum` is usually **inlined** and produces no object.

---

# 2. Primitive types (string, number, boolean, null, undefined, symbol, bigint)

### Analogy

Primitives are the basic fields on a form: name (string), age (number), active (boolean). They’re the leaves of your object tree.

### JS vs TS

```js
// JavaScript (no types)
const name = "Asha";
const age = 28;
const active = true;
```

```ts
// TypeScript (typed)
const name: string = "Asha";
const age: number = 28;
const active: boolean = true;
```

* `null` and `undefined`: with `"strictNullChecks": true`, `null`/`undefined` are *not* assignable to other types unless explicitly included: `string | null`.
* `symbol`: unique tokens (often for private object keys).
* `bigint`: arbitrary large integers (useful for high precision counts, crypto).

  ```ts
  const big: bigint = 9007199254740991n;
  ```

### Hands-on exercises

1. Create `src/primitives.ts` and declare: `username: string`, `balance: number`, `isAdmin: boolean`, `lastLogin: Date | null`.
2. Try assigning `null` to a `string` when `strictNullChecks` is true — examine the error.

### Common mistakes & debugging

* Assigning `null` to a non-nullable type. Fix: `string | null` or avoid `null` and use `undefined`.
* Confusing `bigint` and `number` (`1n` vs `1`): they are distinct types; operations between them are invalid.

### Quick quiz

1. With `strictNullChecks`, can a variable typed `string` accept `null`?
2. What literal suffix is used for `bigint` in TS?

---

# 3. Special types (`any`, `unknown`, `never`, `void`)

### Analogy

* `any` = turning off the type checker for a value (like removing the seatbelt).
* `unknown` = you received a package; you must inspect before using it.
* `never` = a function that never returns (throws or infinite loop).
* `void` = a function returns nothing useful (side-effect only).

### Side-by-side

```js
// JS: everything allowed
let data = JSON.parse(someString); // data could be anything
```

```ts
// TS: explicit
let anything: any = JSON.parse(someString);    // no checks - dangerous
let value: unknown = JSON.parse(someString);    // safe: must narrow before use
```

```ts
function fail(msg: string): never {
  throw new Error(msg);
}
function log(msg: string): void {
  console.log(msg);
}
```

### Hands-on

1. Parse user input: `const raw = JSON.parse(inp);` try to assign to `unknown` and then safely assert or narrow.
2. Write `function assertIsNumber(x: unknown): asserts x is number` and use it.

### Debugging tips

* Avoid `any` wherever possible. Use `unknown` at module boundaries and then validate (zod/io-ts/ajv).
* `never` often appears in exhaustiveness checks. If you add a case to a discriminated union and your switch doesn’t handle it, a `never` assignment will error and help you find the missing branch.

### Quick quiz

1. Why is `unknown` safer than `any`?
2. When would you use `never`?

---

# 4. Object types — objects, arrays, tuples, functions

### Analogy

Objects = records in a database; arrays = lists; tuples = fixed columns; functions = stored procedures with typed inputs/outputs.

### Objects (interface/type)

```js
// JS
function printUser(u) { console.log(u.name, u.email); }
```

```ts
interface User {
  id: string;
  name: string;
  email?: string; // optional
}
function printUser(u: User) { console.log(u.name, u.email ?? "—"); }
```

### Arrays & tuples

```ts
const tags: string[] = ["sale", "new"];
const coords: [number, number] = [12.9, 77.6]; // tuple: lat, lng
```

### Functions (signatures & overloaded style)

```ts
type Formatter = (input: string) => string;

function wrap(text: string, wrapper: string): string { return wrapper + text + wrapper; }

// Overloads
function parse(x: string): Date;
function parse(x: number): Date;
function parse(x: any): Date { /* implementation */ }
```

### Hands-on

1. Create `Product` interface with `id`, `name`, `price`, `tags?: string[]`, `dimensions?: [number, number, number]`.
2. Create a `transformProducts` function type: `(items: Product[]) => Record<string, Product>` and implement.

### Common mistakes

* Using `Array<any>` or bare `object`: too loose. Prefer `Record<string, unknown>` if you need general objects and narrow later.
* Mutating tuple elements when they are intended fixed; mark as `readonly` if immutable.

### Quick quiz

1. How do you type an array of `User` objects?
2. What’s the difference between `object` and `Record<string, unknown>`?

---

# 5. Union & Intersection types

### Analogy

* Union (`A | B`): a container can be apple **or** orange. You must check before using fruit-specific methods.
* Intersection (`A & B`): a single object that satisfies both blueprints — a hybrid device.

### Examples

```ts
type ID = string | number;

function lookup(id: ID) {
  if (typeof id === "string") { /* string logic */ }
  else { /* number logic */ } // narrowed
}
```

Intersection:

```ts
type Timestamped = { createdAt: string };
type WithId = { id: string };
type Entity = Timestamped & WithId; // must have both fields
```

### Discriminated union (best practice)

```ts
type Success = { status: "ok"; data: Product[] };
type Error = { status: "error"; message: string };
type ApiResult = Success | Error;

function handle(res: ApiResult) {
  if (res.status === "ok") { /* res.data available */ }
  else { /* res.message available */ }
}
```

### Hands-on

1. Model `Payment` union: `{ type: "card"; last4: string } | { type: "upi"; vpa: string }`.
2. Write `formatPayment` that narrows and returns a display string.

### Debugging tips

* Use discriminant fields (`kind`/`type`/`status`) so narrowings are obvious.
* If union members share properties with different types, use user-defined type guards.

### Quick quiz

1. Name two ways to narrow a union.
2. How would you model a resource that may be `null` or a `Post`?

---

# 6. Literal types & Type aliases

### Analogy

Literal types are like enumerated options on a form (e.g., `"pending"`, `"shipped"`).

```ts
type SortOrder = "asc" | "desc";
function sortBy(order: SortOrder) { /* ... */ }
```

Type aliases:

```ts
type ProductID = string;
type Maybe<T> = T | null;
```

### Hands-on

1. Create `Currency = "INR" | "USD" | "EUR"`.
2. Create `type ApiResponse<T> = { ok: true; value: T } | { ok: false; error: string }`.

### Common mistakes

* Overusing big unions of string literals where enums or union discriminants would be better organized.
* Naming collisions: prefer `PascalCase` for type aliases to distinguish from variables.

### Quick quiz

1. How do you define a type alias for `string | null`?
2. When would a union of string literals be preferred over `enum`?

---

# 7. Enums — numeric, string, heterogeneous, const enums, reverse mapping

### Analogy

Enums are labelled constants for finite states — e.g., order statuses.

### Numeric enum (default)

```ts
enum OrderStatus { Pending, Shipped, Delivered }
const s = OrderStatus.Shipped; // 1
```

* **Reverse mapping** exists: `OrderStatus[1] === "Shipped"`.

### String enum

```ts
enum PaymentStatus { Pending = "PENDING", Paid = "PAID" }
```

* No reverse mapping by numeric index; values remain strings.

### Heterogeneous enum (mix of numbers & strings)

```ts
enum Mixed { No = 0, Yes = "YES" } // allowed, but discouraged
```

* Use only when necessary — usually avoid.

### `const enum`

```ts
const enum Direction { Up, Down }
const d = Direction.Up; // inlined: the emitted JS contains `0` literal
```

* `const enum` is **inlined** (no object emitted). Great for performance — but can cause issues with some bundlers or when using `--preserveConstEnums`.

### When to use enums vs union literals

* `enum` (non-const) is useful when you need runtime object (reverse mapping, iterate values).
* `string unions` are lightweight for type-level constraints with no runtime object: `type Status = "pending" | "shipped"`.

### Hands-on

1. Create `enum Role { Admin = "ADMIN", User = "USER" }`.
2. Write a function `isAdmin(role: Role | string): boolean` that properly narrows `role`.

### Common mistakes & debugging

* Expecting reverse mapping for string enums — it doesn’t exist.
* Using `const enum` with tools that don’t support inlining results in runtime errors.
* Relying on numeric values across systems: prefer string enums for clarity across services.

### Quick quiz

1. Does a string enum produce reverse mapping?
2. What’s a key difference between `const enum` and regular enum at runtime?

---

# 8. Advanced typing — optional & readonly properties, type inference, type narrowing, discriminated unions

### Optional & readonly

```ts
interface Product { id: string; name: string; price?: number; readonly sku: string }
```

* Optional: property may be absent.
* `readonly`: cannot be reassigned (helps immutability).

### Type inference

TS infers types from values; prefer `const` for literal inference.

```ts
const x = "hello"; // inferred as "hello" (literal) if `as const` used or if const context
const y = { a: 1 }; // y inferred with property types
```

### Type narrowing techniques

* `typeof x === "string"`
* `Array.isArray(x)`
* `in` operator: `'price' in obj`
* discriminant property checks: `if (obj.kind === "circle")`
* user-defined type guards: `function isUser(v: any): v is User { return v && typeof v.id === "string"; }`

### Discriminated unions

(See earlier examples) — make discriminant literal property small and consistent: `kind`, `type`, `status`.

### Hands-on

1. Given union type `Event = { kind: "click"; x:number } | { kind: "key"; key: string }` implement `handleEvent(e: Event)` with exhaustive switch and `never` catch to force compile breaks when new variants are added.

### Debugging tips

* If narrowing fails, make sure discriminant is a literal type (use `as const` when creating the discriminants).
* If you get `Property X does not exist on type Y`, you probably forgot to narrow or the property is optional.

### Quick quiz

1. Name three ways to narrow a variable.
2. What does `readonly` protect you from?

---

# 9. `keyof`, `typeof`, indexed access types, and `in` operator

### `keyof`

Gives union of property names.

```ts
type UserKeys = keyof User; // "id" | "name" | "email"?
```

### `typeof` (type query)

Get the type of a value expression.

```ts
const product = { id: "p1", name: "Desk" };
type ProductType = typeof product; // { id: string; name: string }
```

### Indexed access `T[K]`

Access property type.

```ts
type NameType = User["name"]; // string
```

### Hands-on

1. Given `const model = { a: 1, b: "x" } as const`, create `type Keys = keyof typeof model`.

### Debugging tips

* `keyof` on unions becomes intersections of keys (be mindful).
* When using `typeof` for runtime values, ensure the value exists in the current scope.

### Quick quiz

1. What does `keyof T` produce?
2. How do you get the type of `obj.count` using indexed access?

---

# 10. Conditional types, distributive conditional types, `infer`, mapped types

### Analogy

Conditional types are a compile-time `if` that maps types to other types.

```ts
type IdOf<T> = T extends { id: infer U } ? U : never;
```

* `infer` extracts a type variable from a matched pattern.

### Distributive behavior

If `T` is a union `A | B`, `T extends U ? X : Y` distributes: becomes `(A extends U ? X : Y) | (B extends U ? X : Y)` unless wrapped in tuple ` [T] extends [U] ? ...` to prevent distribution.

### Mapped types

Create new types by mapping over keys.

```ts
type Readonly<T> = { readonly [K in keyof T]: T[K] };
type Mutable<T> = { -readonly [K in keyof T]: T[K] };
```

### Example: PartialUpdate

```ts
type PartialUpdate<T> = { [K in keyof T]?: T[K] | null };
```

### Hands-on

1. Implement `Mutable<T>` that removes `readonly` from all properties.
2. Create `Values<T>` that returns a union of all property value types: `T[keyof T]`.

### Debugging tips

* Conditional/mapped types can be hard to read in errors. Break them into named intermediate types during development.
* Limit recursion depth in conditional types to avoid compiler slowdown.

### Quick quiz

1. What does `infer` let you do?
2. How to prevent distributive conditional types?

---

# 11. Interfaces vs Types — when to use each

### Short rules & analogy

* **Interface** = extendable blueprint (like a class contract). Good for public API shapes that might be extended by consumers.
* **Type alias** = flexible, composable (unions, tuples, mapped types). Good for one-off unions, complex generics.

**Analogy:** interface = an extendable building plan you publish; type = a specific blueprint cassette you compose.

### Differences

* `interface` supports declaration merging (you can declare the same interface in multiple places — good for global augmentation).
* `type` can represent unions and advanced constructs that `interface` cannot.
* Most of the time they are interchangeable for object shapes. Use interfaces for public object shapes and types for unions/utility types.

### Hands-on

1. Define a `type` for `Result<T>` union and an `interface` for `User`. Try to `extend` both in a new declaration.

### Common mistakes

* Expecting declaration merging with `type` (it doesn’t work).
* Picking `interface` for advanced union types — prefer `type`.

### Quick quiz

1. Can you create `type A = { x: number }` and later add `{ y: string }` to it with another declaration? (yes/no)
2. When would you prefer `type` over `interface`?

---

# 12. Practical use cases (React props, Node models, API contracts)

### React Props

```tsx
type Todo = { id: string; title: string; done: boolean };
function TodoItem({ todo, onToggle }: { todo: Todo; onToggle: (id: string) => void }) {
  // strongly typed event handlers
}
```

* Use `React.ChangeEvent<HTMLInputElement>` when typing DOM events.
* Use `satisfies` (TS 4.9+) to verify object shapes while keeping inferred narrow types.

### Node / Express models

```ts
interface CreateUserReq { name: string; email: string; password: string }
app.post("/users", (req: Request<{}, {}, CreateUserReq>, res) => { /* req.body typed */ });
```

* At boundaries, validate runtime (zod/joi) and then narrow to TS types.

### API contracts

* Use shared `types.ts` between client and server or codegen (OpenAPI, tRPC, or GraphQL codegen) so API changes fail at compile time.

### Hands-on

1. Create `types.ts` with `User`, `LoginRequest`, and `ApiResponse<T>`. Use the types in both a simulated `server.ts` function and `client.ts` function.

### Debugging tips

* If client and server disagree, compile fails early — use shared types or codegen.
* Don’t rely solely on TypeScript for input validation — always validate runtime payloads.

### Quick quiz

1. Why should you validate runtime payloads even if TypeScript types exist?
2. Name a tool that generates types from server schema (OpenAPI, GraphQL, etc).

---

# 13. Common compiler errors & how to fix them

* **`Property 'x' does not exist on type 'Y'`**
  Fix: narrow the type first or widen the object definition.

* **`Type 'A' is not assignable to type 'B'`**
  Fix: check structural compatibility or cast using safe guards. If you must cast, prefer runtime checks first.

* **`Cannot find name 'Iterable'` or missing DOM types**
  Fix: add the proper `"lib"` entries in `tsconfig.json` (e.g., `"ES2015", "DOM"`).

* **Excessive `any`**
  Fix: change to `unknown` at boundaries and parse/validate.

* **Type inference too broad**
  Use `as const` for literal inference or add explicit generic annotations.

---

# 14. Mini-project — Order Management Types & Enums (practical, uses many features)

**Goal:** Create a strongly-typed TypeScript module that models an order system and exposes types and helper functions for a client and server.

**What to include (deliverables)**

* `src/types.ts`

  * `enum OrderStatus` (use string enum: `"PENDING" | "PROCESSING" | "SHIPPED" | "DELIVERED" | "CANCELLED"`)
  * `type Currency = "INR" | "USD" | "EUR"`
  * `interface Product { id: string; name: string; price: number; currency: Currency; tags?: string[] }`
  * `type SKU = `\${Product\["id"]}-\${string}\`\` (template literal type)
  * `type Money = { amount: number; currency: Currency }`
  * `interface PaymentCard { brand: string; last4: string; type: "card" }`
  * `interface UPI { vpa: string; type: "upi" }`
  * `type PaymentMethod = PaymentCard | UPI` (discriminated union)
  * `interface OrderItem { product: Product; qty: number; priceAtOrder: Money }`
  * `interface Order { id: string; items: OrderItem[]; status: OrderStatus; payment: PaymentMethod; createdAt: string; shippedAt?: string }`
  * `type ApiResult<T> = { ok: true; result: T } | { ok: false; error: string }`

* `src/utils.ts`

  * `type Total<T extends OrderItem[]> = number` (compute totals at runtime; use utility types for shapes)
  * `function isCard(m: PaymentMethod): m is PaymentCard` (user defined type guard)
  * `function updateOrder<T extends Partial<Order>>(order: Order, patch: T): Order & T` — show `Partial` and `&` usage.

* `src/tests.ts` (examples)

  * Example `const order1: Order = { ... }` compile-checked.
  * Use `as const` for inlined discriminants.
  * Attempt invalid assignment (e.g., `status: "SOMETHING"`) and observe the compile error.

**Tasks for you (practice steps)**

1. Implement types above in `src/types.ts`.
2. Write `isCard` guard and `formatPayment` function that returns a display string and demonstrates narrowing.
3. Implement `calculateOrderTotal(order: Order): Money` that sums `item.qty * item.priceAtOrder.amount` and returns `Money`.
4. Create an `updateOrderStatus(order, status)` that enforces allowed transitions (use `keyof` or a transition map typed with `Record<OrderStatus, OrderStatus[]>`).
5. Add a `const enum` for internal log levels and prove they are inlined by viewing emitted JS.

**Stretch (advanced)**

* Create a mapped type `ReadonlyOrder = Readonly<Order>` and try mutating to see errors.
* Generate `OrderDTO = Omit<Order, "items"> & { itemIds: string[] }` to illustrate `Omit`.
* Use conditional types to define `Payload<T>` where `T` is `Order` or `Product` and returns appropriate API payload type.

**Testing the mini-project**

* `npm run typecheck` should pass with `tsc --noEmit`.
* Try to intentionally break types (e.g., set `payment: { type: "card", last4: 123 }`) and fix it.

---

# 15. Best practices & guidelines (summary)

* Turn on `"strict": true` and enable `noUncheckedIndexedAccess` where helpful.
* Use `unknown` at external boundaries; validate with runtime schemas (zod/io-ts).
* Prefer union literals for small, simple enumerations; use `string enum` for cross-service clarity.
* Keep shared types in a `packages/types` or `shared/types.ts` when client/server share code.
* Avoid `any`. If you must use it temporarily, mark with `// TODO: narrow` and add a task for runtime validation.
* Use type guards & discriminant properties for safe narrowing.
* Use `as const` to preserve literal types on constant objects.
* Prefer `Record<K, V>` for maps with known key sets.
* When authoring libraries, ship `.d.ts` files or `types` entry in `package.json`.

---

# 16. Final quizzes (answer these to check comprehension)

**Quiz A — primitives & specials**

1. What’s the difference between `unknown` and `any`?
2. Why is `1n` not assignable to `number`?

**Quiz B — objects & arrays**
3\. How to type a function that receives an array of `User` and returns the first user or `undefined`?
4\. What error will you get if you remove optional chaining on a possibly undefined property?

**Quiz C — unions & narrowing**
5\. Given `type P = { kind:"a"; a: number } | { kind:"b"; b: string }`, write the safest `getValue(p: P)` returning `number | string`.

**Quiz D — enums**
6\. What does reverse mapping mean and for which enum types does it exist?
7\. When are `const enum` inlined?

**Quiz E — advanced**
8\. What does `keyof T` produce for `T = { id: string; value: number }`?
9\. How do you stop a conditional type from distributing over a union?

Reply with your answers and any code you want me to review from the mini-project. I’ll review, correct types, and show improvements and refactorings (and optionally provide a ready-to-copy `tsconfig.json` and sample `package.json` scripts).

Would you like the mini-project as a ready scaffold (files + `tsconfig.json`) that you can paste into your repo next? If yes — tell me whether you prefer a single package or a small monorepo structure and I’ll generate it.
