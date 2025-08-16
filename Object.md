## 1. **Concept**

* Objects in TypeScript store **structured data** as key-value pairs.
* You can create objects using **literals** or **classes**, and combine them with **types/interfaces** for safety.
* Objects help **organize data**, **pass it between modules**, and **ensure predictable code**.

Analogy:

> Object = a container with labeled compartments → everything is typed and organized.

---

## 2. **Syntax & Usage**

### Using object literals:

```ts
let car = { brand: "Tesla", year: 2025 };

// Access properties
console.log(car.brand); // Tesla
console.log(car.year);  // 2025
```

### Using classes:

```ts
class Car {
  constructor(
    public brand: string,
    public year: number,
    readonly vin: string // immutable field
  ) {}
}

const myCar = new Car("Tesla", 2025, "VIN123456");
console.log(myCar.brand); // Tesla
// myCar.vin = "VIN987"; ❌ readonly prevents modification
```

* **Classes** are ideal when objects need **methods or encapsulated behavior**.

---

## 3. **Real-World Use Cases**

### 3.1 Storing API Response Data

```ts
interface User {
  id: number;
  name: string;
  email: string;
  isAdmin?: boolean; // optional field
}

const apiResponse = {
  id: 101,
  name: "Alice",
  email: "alice@example.com",
};

const user: User = apiResponse; // enforce object shape
console.log(user.name.toUpperCase()); // ✅ Safe usage
```

* Using **interfaces** ensures API responses have expected properties.
* Optional fields handle incomplete responses safely.

---

### 3.2 Managing Configuration or Settings Objects

```ts
interface AppConfig {
  apiUrl: string;
  timeout?: number;
  readonly version: string;
}

const config: AppConfig = {
  apiUrl: "https://api.example.com",
  version: "1.0.0",
};

console.log(config.apiUrl);   // Safe access
// config.version = "2.0"; ❌ readonly prevents modification
```

* **Readonly properties** prevent accidental changes in critical configs.
* Optional properties allow flexibility.

---

### 3.3 Passing Structured Data Between Functions/Modules

```ts
interface Product {
  id: number;
  name: string;
  price: number;
}

function calculateTotal(products: Product[]): number {
  return products.reduce((sum, p) => sum + p.price, 0);
}

const products: Product[] = [
  { id: 1, name: "Laptop", price: 1500 },
  { id: 2, name: "Mouse", price: 50 },
];

console.log(calculateTotal(products)); // 1550
```

* Objects make **data transfer between modules predictable**.
* TypeScript enforces the **object shape**, preventing runtime bugs like missing `price` properties.

---

## 4. **Best Practices in Production**

1. **Use types or interfaces to enforce object shapes**

```ts
interface Order {
  id: number;
  product: Product;
  quantity: number;
}
```

2. **Use readonly for immutable fields**

```ts
interface Product {
  readonly id: number;
  name: string;
  price: number;
}
```

3. **Optional properties for flexible objects**

```ts
interface User {
  id: number;
  name: string;
  email?: string;
}
```

4. **Prefer object literals for simple structures**, classes for objects with **methods or behavior**.

5. **Always annotate function parameters and return types** with objects

```ts
function getUserName(user: User): string {
  return user.name;
}
```

---

## 5. **Why Objects Improve Production Code**

* **Data Organization:** Objects group related data logically.
* **Maintainability:** Interfaces and classes prevent misuse across modules.
* **Safety:** TypeScript checks object shapes, reducing runtime errors.
* **Scalability:** Large applications can reliably pass objects across services and components.

---

### ✅ TL;DR – Real Project Mindset

| Pattern         | Example Use Case                                         |
| --------------- | -------------------------------------------------------- |
| Object literals | Quick API response storage, small config objects         |
| Classes         | Models with methods (User, Product, Service classes)     |
| Interfaces      | Enforce object shape, API responses, function parameters |
| Optional fields | Handle incomplete data safely                            |
| Readonly        | Prevent accidental mutation of critical fields           |
