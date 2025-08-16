## 1. **Concept**

* TypeScript classes bring **Object-Oriented Programming (OOP)** to JS.
* They provide **blueprints for objects** with properties, methods, and **encapsulation**.
* Classes improve **reusability, maintainability, and structure**, especially in large-scale applications.

Analogy:

> Class = blueprint for a “thing” (e.g., User, Product, Order) → you can create multiple instances safely and consistently.

---

## 2. **Syntax & Usage**

```ts
class Person {
  constructor(public name: string, private age: number) {}

  greet(): string {
    return `Hello, my name is ${this.name}`;
  }

  getAge(): number {
    return this.age; // encapsulated access
  }
}

const alice = new Person("Alice", 25);
console.log(alice.greet()); // Hello, my name is Alice
console.log(alice.getAge()); // 25
// console.log(alice.age); ❌ private, cannot access directly
```

* `public` → accessible everywhere
* `private` → only accessible inside the class
* `protected` → accessible inside class and subclasses

---

## 3. **Real-World Use Cases**

### 3.1 Creating Reusable Models for API Data

```ts
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

const userData = { id: 1, name: "Bob", email: "bob@example.com" };
const user = new User(userData.id, userData.name, userData.email);
console.log(user.getDisplayName()); // Bob <bob@example.com>
```

* Wraps raw API data in a **typed, reusable model**
* Methods like `getDisplayName()` centralize logic, reducing repeated code

---

### 3.2 Building Services or Utility Classes

```ts
class ProductService {
  private products: { id: number; name: string; price: number }[] = [];

  addProduct(product: { id: number; name: string; price: number }) {
    this.products.push(product);
  }

  getTotalPrice(): number {
    return this.products.reduce((sum, p) => sum + p.price, 0);
  }
}

const service = new ProductService();
service.addProduct({ id: 1, name: "Laptop", price: 1500 });
service.addProduct({ id: 2, name: "Mouse", price: 50 });
console.log(service.getTotalPrice()); // 1550
```

* Service classes encapsulate **state + behavior**
* Improves maintainability in large apps

---

### 3.3 Implementing Inheritance & Access Modifiers

```ts
class Employee extends Person {
  constructor(name: string, age: number, public role: string) {
    super(name, age);
  }

  getDetails(): string {
    return `${this.name}, Age: ${this.getAge()}, Role: ${this.role}`;
  }
}

const emp = new Employee("Charlie", 30, "Developer");
console.log(emp.getDetails()); // Charlie, Age: 30, Role: Developer
```

* **Inheritance** allows code reuse
* `protected` or `private` properties prevent accidental external changes
* Supports **OOP patterns** like polymorphism and encapsulation

---

## 4. **Best Practices in Real Projects**

1. **Use constructors for mandatory properties**

```ts
class Product {
  constructor(
    public id: number,
    public name: string,
    public readonly price: number
  ) {}
}
```

* `readonly` ensures critical fields aren’t accidentally modified

2. **Encapsulate internal logic** using private/protected properties

```ts
class BankAccount {
  private balance: number = 0;

  deposit(amount: number) {
    this.balance += amount;
  }

  getBalance() {
    return this.balance;
  }
}
```

3. **Use getters/setters** for controlled access

```ts
class Rectangle {
  constructor(private _width: number, private _height: number) {}

  get area() {
    return this._width * this._height;
  }

  set width(value: number) {
    if (value > 0) this._width = value;
  }
}
```

4. **Organize utility classes** (services, helpers) instead of global functions

* Makes code **modular, testable, and reusable**

---

## 5. **Why Classes Improve Production-Level Code**

* **Reusability:** Create multiple instances easily
* **Maintainability:** Encapsulate logic in one place
* **Readability:** Clear structure + access modifiers
* **Scalability:** Supports inheritance and polymorphism
* **Type Safety:** Ensures properties and methods are used correctly

---

### ✅ TL;DR – Real Project Mindset

| Feature          | Example Use Case                                            |
| ---------------- | ----------------------------------------------------------- |
| Constructor      | Initialize API model objects                                |
| Methods          | Encapsulate object behavior (getDisplayName)                |
| Access Modifiers | Protect sensitive data, control access                      |
| Inheritance      | Employee extends Person → reuse code                        |
| Readonly         | Prevent accidental modification of critical fields          |
| Services         | Encapsulate application logic (ProductService, BankAccount) |
