## 1. **Concept**

* **Constructors** are special methods in a class used to **initialize object properties** when creating instances.
* They ensure objects start in a **consistent, predictable state**.

Analogy:

> Constructor = a “setup routine” that runs every time you create a new object, making sure it’s ready to use.

---

## 2. **Syntax & Usage**

```ts
class Student {
  constructor(public id: number, public name: string) {}
}

const student = new Student(101, "Alice");
console.log(student.name); // Alice
console.log(student.id);   // 101
```

* `public` automatically creates properties and assigns values.
* No need to declare properties separately if using **parameter properties**.

---

## 3. **Real-World Use Cases**

### 3.1 Initializing Models for API Responses

```ts
interface UserResponse {
  id: number;
  name: string;
  email: string;
}

class User {
  constructor(
    public id: number,
    public name: string,
    public email: string
  ) {}

  getDisplayName(): string {
    return `${this.name} <${this.email}>`;
  }
}

// Simulate API response
const apiData: UserResponse = { id: 1, name: "Bob", email: "bob@example.com" };
const user = new User(apiData.id, apiData.name, apiData.email);

console.log(user.getDisplayName()); // Bob <bob@example.com>
```

* Constructor ensures the **model object matches API data structure**.
* Avoids creating objects with **undefined properties**.

---

### 3.2 Setting Default Values in Services or Utilities

```ts
class ProductService {
  private discount: number;
  
  constructor(discount: number = 0) {
    this.discount = discount; // default value if not provided
  }

  applyDiscount(price: number): number {
    return price - price * this.discount;
  }
}

const service = new ProductService(0.1);
console.log(service.applyDiscount(100)); // 90

const defaultService = new ProductService(); // discount defaults to 0
console.log(defaultService.applyDiscount(100)); // 100
```

* Constructors can **provide default values** for optional parameters.
* Makes **service instances predictable** without extra setup.

---

### 3.3 Creating Multiple Objects with Consistent Structure

```ts
class Product {
  constructor(
    public id: number,
    public name: string,
    public price: number = 0 // default value
  ) {}
}

const products = [
  new Product(1, "Laptop", 1500),
  new Product(2, "Mouse"), // price defaults to 0
];

products.forEach(p => console.log(`${p.name}: $${p.price}`));
// Laptop: $1500
// Mouse: $0
```

* Constructors help create **multiple objects** with consistent structure and optional defaults.

---

## 4. **Best Practices**

1. **Use parameter properties** (`public`, `private`) to reduce boilerplate:

```ts
class Order {
  constructor(public id: number, public total: number) {}
}
```

2. **Optional parameters** using `?`:

```ts
class Customer {
  constructor(public name: string, public email?: string) {}
}
```

3. **Default values** for flexibility:

```ts
class Config {
  constructor(public timeout: number = 5000) {}
}
```

4. **Keep constructors lightweight**

* Avoid putting heavy logic; initialize properties, not perform complex operations.

---

## 5. **Why Constructors Matter in Production**

* Ensures objects are **properly initialized**, reducing runtime errors.
* Makes code **predictable and consistent** when creating instances.
* Supports **default values and optional parameters**, improving flexibility.
* Improves **maintainability** by centralizing object setup logic.

---

### ✅ TL;DR – Real Project Mindset

| Feature                   | Example Use Case                                         |
| ------------------------- | -------------------------------------------------------- |
| Parameter properties      | `constructor(public id: number)` → auto-creates property |
| Optional parameters       | Handle missing data from APIs safely                     |
| Default values            | Set defaults in services, models, or configs             |
| Consistent initialization | Multiple objects with same structure (Product, User)     |
| Lightweight constructors  | Focus on setting up properties, not heavy logic          |
