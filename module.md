## 1. **Concept**

* **Modules** = separate files that encapsulate code (classes, functions, constants, interfaces).
* Helps **organize large codebases**, **re-use code**, and **avoid polluting the global scope**.

Analogy:

> Think of each module as a **specialized toolbox**: you can export the tools you need and import them wherever required.

---

## 2. **Syntax & Usage**

### Named Exports

```ts
// Car.ts
export class Car {
  constructor(public brand: string) {}
  drive() { console.log(`${this.brand} is driving`); }
}

// main.ts
import { Car } from "./Car";

const myCar = new Car("Tesla");
myCar.drive(); // Tesla is driving
```

* `export` = makes class/function/type available outside the file
* `import { Car }` = brings the class into another file

---

### Default Exports

```ts
// Logger.ts
export default class Logger {
  log(message: string) { console.log(message); }
}

// main.ts
import Logger from "./Logger";
const logger = new Logger();
logger.log("App started"); // App started
```

* **Default export** = only one per file, imported without braces

---

### Exporting Multiple Items

```ts
// utils.ts
export function formatPrice(price: number) { return `$${price}`; }
export function calculateTax(amount: number) { return amount * 0.1; }

// main.ts
import { formatPrice, calculateTax } from "./utils";
console.log(formatPrice(150)); // $150
console.log(calculateTax(150)); // 15
```

* Helps **group related utility functions** in one module

---

## 3. **Real-World Use Cases**

### 3.1 Structuring Large-Scale Applications

```text
src/
 ├─ models/
 │   └─ Product.ts
 ├─ services/
 │   └─ CartService.ts
 ├─ utils/
 │   └─ helpers.ts
 └─ main.ts
```

```ts
// models/Product.ts
export interface Product { id: number; name: string; price: number; }

// services/CartService.ts
import { Product } from "../models/Product";
export class CartService {
  private items: Product[] = [];
  addItem(item: Product) { this.items.push(item); }
  getTotal() { return this.items.reduce((sum, i) => sum + i.price, 0); }
}

// main.ts
import { CartService } from "./services/CartService";
import { Product } from "./models/Product";

const cart = new CartService();
const product: Product = { id: 1, name: "Laptop", price: 1500 };
cart.addItem(product);
console.log(cart.getTotal()); // 1500
```

* **Models, services, and utils** are in separate files
* Improves **readability, maintainability, and scalability**

---

### 3.2 Reusing Components Across Multiple Parts of a Project

```ts
// models/User.ts
export interface User { id: number; name: string; email: string; }

// services/UserService.ts
import { User } from "../models/User";
export class UserService {
  private users: User[] = [];
  addUser(user: User) { this.users.push(user); }
  listUsers() { return this.users; }
}

// main.ts
import { UserService } from "./services/UserService";
import { User } from "./models/User";

const service = new UserService();
service.addUser({ id: 1, name: "Alice", email: "alice@example.com" });
console.log(service.listUsers());
```

* `UserService` and `User` can be reused in **multiple modules**, like dashboards, admin panels, or reports

---

### 3.3 Managing Dependencies & Avoiding Global Scope Pollution

* Without modules, everything would live in the global scope, causing **name conflicts** and bugs in large projects.
* Modules **encapsulate code**, so only exported members are accessible externally.

---

## 4. **Best Practices**

1. **Use named exports** for multiple utilities or classes in a file
2. **Use default exports** when a file represents a single main entity
3. **Maintain consistent folder structure**: `models/`, `services/`, `utils/`
4. **Keep modules small and focused** – one responsibility per module
5. **Avoid circular dependencies** – organize code to prevent files importing each other endlessly

---

## 5. **Why Modules Matter in Production**

* **Maintainability:** Easy to navigate large codebases
* **Reusability:** Import the same class/function in multiple files
* **Scalability:** New features fit cleanly into existing module structure
* **Code Safety:** TypeScript checks imports/exports, preventing runtime errors

---

### ✅ TL;DR – Real Project Mindset

| Concept          | Example Use Case                                           |
| ---------------- | ---------------------------------------------------------- |
| Named export     | Utilities like `formatPrice`, `calculateTax`               |
| Default export   | Single service or class like `Logger`                      |
| Folder structure | `models/`, `services/`, `utils/`                           |
| Module reuse     | Use `UserService` in admin panel, dashboard, API           |
| Encapsulation    | Keeps internal variables/functions private unless exported |
