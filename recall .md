# **1. Core Concepts Recap**

## **1.1 Types**

* **Purpose:** Ensure values are the expected type, preventing runtime bugs.

* **Practical Use:**

  ```ts
  let userId: number = 101;
  let userName: string = "Alice";
  let isAdmin: boolean = true;
  ```

* **Real Project Scenario:**

  * API responses often have strict types: `id: number`, `email: string`.
  * Using types avoids mistakes like `userId = "abc"`.

* **Complex Types:** Arrays, tuples, enums, `any`, `unknown`

  ```ts
  const productIds: number[] = [1, 2, 3];
  enum Role { Admin, User, Guest }
  let userRole: Role = Role.Admin;
  ```

---

## **1.2 Variables**

* **let** → block-scoped, for mutable values

* **const** → immutable reference, ideal for constants/config

* **var** → avoid in modern TypeScript

* **Example:**

  ```ts
  const API_URL = "https://api.example.com";
  let sessionCount = 0;
  ```

* **Real Project Scenario:**

  * Keep configs `const`
  * Track counters, temporary states with `let`
  * Prevent accidental global leaks with `let` instead of `var`.

---

## **1.3 Interfaces**

* **Purpose:** Define a **contract** for object structure.
* **Example:**

  ```ts
  interface User {
    id: number;
    name: string;
    email?: string; // optional
  }
  const user: User = { id: 1, name: "Bob" };
  ```
* **Real Project Scenario:**

  * Enforce API response shape across services
  * Ensures consistency when passing objects between modules
  * Optional fields handle partial data gracefully.

---

## **1.4 Classes**

* **Purpose:** Blueprint for creating objects, supports OOP.
* **Example:**

  ```ts
  class Product {
    constructor(public id: number, public name: string, private price: number) {}
    getPrice() { return this.price; }
  }
  const laptop = new Product(1, "Laptop", 1500);
  console.log(laptop.getPrice());
  ```
* **Real Project Scenario:**

  * API models, services, and utilities in large applications
  * Encapsulation via private/protected properties
  * Reusable methods for object behavior

---

## **1.5 Objects**

* **Purpose:** Store structured data in key-value pairs.
* **Example:**

  ```ts
  let config = { apiUrl: "https://api.example.com", timeout: 5000 };
  ```
* **Real Project Scenario:**

  * Manage configs, settings, or API response data
  * Pass structured data between functions/modules
  * Combine with interfaces for type safety

---

## **1.6 Access Modifiers**

* **public** → accessible everywhere
* **private** → internal to the class
* **protected** → accessible in class & subclasses
* **Example:**

  ```ts
  class BankAccount {
    constructor(private balance: number) {}
    getBalance() { return this.balance; }
  }
  ```
* **Real Project Scenario:**

  * Protect sensitive data (passwords, balances)
  * Expose only necessary methods to consumers
  * Maintain encapsulated, secure, and maintainable classes

---

## **1.7 Modules**

* **Purpose:** Organize code into **reusable, importable units**.
* **Example:**

  ```ts
  // user.ts
  export interface User { id: number; name: string; }
  export const getUserName = (user: User) => user.name;

  // app.ts
  import { User, getUserName } from "./user";
  const user: User = { id: 1, name: "Alice" };
  console.log(getUserName(user));
  ```
* **Real Project Scenario:**

  * Split large projects into modules: services, models, utils
  * Improves maintainability, scalability, and testability

---

# **2. Real-Time Use Case: Combining All Concepts**

Imagine building a **mini e-commerce dashboard**:

* **Types** → enforce API response shapes (`Product`, `Order`)
* **Variables** → track state (`let cartCount = 0`)
* **Interfaces** → define models (`User`, `Product`)
* **Classes** → encapsulate services (`CartService`, `OrderService`)
* **Objects** → configuration or API payloads
* **Access Modifiers** → protect internal data (`private balance`)
* **Modules** → split code into files: `models.ts`, `services.ts`, `utils.ts`

```ts
// models.ts
export interface Product { id: number; name: string; price: number; }
export interface User { id: number; name: string; email?: string; }

// services.ts
import { Product } from "./models";
export class CartService {
  private items: Product[] = [];
  addItem(item: Product) { this.items.push(item); }
  getTotal() { return this.items.reduce((sum, i) => sum + i.price, 0); }
}

// app.ts
import { CartService } from "./services";
const cart = new CartService();
cart.addItem({ id: 1, name: "Laptop", price: 1500 });
console.log(cart.getTotal());
```

* ✅ **Type safety** prevents mistakes (e.g., missing `price`)
* ✅ **Encapsulation** hides internal cart state
* ✅ **Modules** make code reusable and organized

---

# **3. Next Steps**

1. **Practice Projects:**

   * Mini CRUD app for users or products
   * API service with models, services, and validation
   * Dashboard tracking products/orders with state management

2. **Learn Advanced Concepts:**

   * **Generics:** Make classes and functions reusable with flexible types

     ```ts
     class ApiResponse<T> { constructor(public data: T) {} }
     const userResponse = new ApiResponse<User>({ id: 1, name: "Alice" });
     ```
   * **Decorators:** Add metadata or behaviors to classes/methods (common in NestJS)
   * **Advanced Types:** Union, intersection, mapped types for scalable apps

3. **Practical Advice:**

   * Always **use interfaces/types** for API data and function parameters
   * **Encapsulate logic in classes** with proper access modifiers
   * Split code into **modules** for clarity and maintainability
   * Start with **small projects**, gradually adding advanced features
