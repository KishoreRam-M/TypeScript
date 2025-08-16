## 1. **Concept**

* **Getters** allow controlled **reading** of a property.
* **Setters** allow controlled **writing** to a property, enabling **validation, logging, or side-effects**.
* Both enable **encapsulation**, keeping the internal representation private while controlling access.

Analogy:

> Think of a property like a **door**.
>
> * Getter = “peek inside safely”
> * Setter = “only let in valid things”

---

## 2. **Syntax & Usage**

```ts
class Circle {
  private _radius: number = 0;

  get radius() {
    return this._radius;
  }

  set radius(r: number) {
    if (r < 0) throw new Error("Radius cannot be negative");
    this._radius = r;
  }
}

const circle = new Circle();
circle.radius = 5;       // ✅ Setter validates input
console.log(circle.radius); // ✅ Getter returns value
// circle.radius = -3;    ❌ Throws error
```

* `_radius` is a **private backing field**.
* Getter/setter control **how external code interacts** with the property.

---

## 3. **Real-World Use Cases**

### 3.1 Validating User Input in Models

```ts
class User {
  private _age: number = 0;

  constructor(age: number) {
    this.age = age; // use setter for validation
  }

  get age() {
    return this._age;
  }

  set age(value: number) {
    if (value < 0 || value > 120) {
      throw new Error("Invalid age");
    }
    this._age = value;
  }
}

const user = new User(25);
console.log(user.age); // 25
// user.age = -5; ❌ Validation prevents invalid value
```

* Ensures **model integrity** and prevents **runtime errors** from invalid data.

---

### 3.2 Logging or Triggering Events When Properties Change

```ts
class Cart {
  private _items: string[] = [];

  get items() {
    return this._items;
  }

  set items(newItems: string[]) {
    console.log("Cart updated:", newItems);
    this._items = newItems;
  }
}

const cart = new Cart();
cart.items = ["Laptop"]; // Logs: Cart updated: ["Laptop"]
```

* Useful in **services/components** to trigger **side-effects** when data changes.

---

### 3.3 Enforcing Business Rules in Large Applications

```ts
class BankAccount {
  private _balance: number = 0;

  get balance() {
    return this._balance;
  }

  set balance(amount: number) {
    if (amount < 0) {
      throw new Error("Balance cannot be negative");
    }
    this._balance = amount;
  }

  deposit(amount: number) {
    this.balance += amount; // uses setter validation
  }
}

const account = new BankAccount();
account.deposit(100);
console.log(account.balance); // 100
// account.balance = -50; ❌ Prevented by setter
```

* Business rules like **no negative balance** are enforced centrally.

---

## 4. **Best Practices**

1. **Use private backing fields (`_property`)** to hide internal data.
2. **Keep accessors concise** – avoid heavy computation in getters or setters.
3. **Validation and side-effects** are OK in setters, but avoid async or heavy logic in getters.
4. **Consistent naming**: `_property` for private, `property` for public accessor.

---

## 5. **Why Getters/Setters Matter in Production**

* **Encapsulation**: Hide internal representation and control access.
* **Validation & Safety**: Prevent invalid data from entering your models.
* **Maintainability**: Centralize property rules in one place.
* **Extensibility**: Add logging, events, or triggers without changing external code.

---

### ✅ TL;DR – Real Project Mindset

| Pattern               | Example Use Case                                        |
| --------------------- | ------------------------------------------------------- |
| Private backing field | `_age`, `_balance` – hide internal data                 |
| Getter                | Read property safely (`user.age`)                       |
| Setter                | Validate input, enforce business rules (`balance >= 0`) |
| Side-effects          | Logging, events, state updates (`cart.items`)           |
| Production benefit    | Data integrity, maintainability, encapsulation          |
