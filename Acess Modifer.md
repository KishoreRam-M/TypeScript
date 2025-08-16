## 1. **Concept**

* **Access modifiers** control **who can access class members** (properties or methods).
* They enforce **encapsulation**, preventing accidental misuse of internal data or logic.

Analogy:

> Think of a class like a **company**:
>
> * `public` = employee accessible by anyone outside
> * `private` = confidential internal department
> * `protected` = department info shared only with managers (subclasses)

---

## 2. **Types of Access Modifiers**

| Modifier    | Visibility                      |
| ----------- | ------------------------------- |
| `public`    | Everywhere (default if omitted) |
| `private`   | Inside the class only           |
| `protected` | Inside class + subclasses       |

---

## 3. **Practical Examples & Real-World Scenarios**

### 3.1 Hiding Sensitive Data in Models

```ts
class User {
  constructor(
    public name: string,
    private password: string // sensitive info
  ) {}

  checkPassword(input: string): boolean {
    return input === this.password; // internal use
  }
}

const user = new User("Alice", "secure123");
console.log(user.name); // ✅ Alice
// console.log(user.password); ❌ private, cannot access
console.log(user.checkPassword("secure123")); // ✅ true
```

* `password` is **hidden** from the outside → prevents accidental leaks.

---

### 3.2 Protecting Internal Methods in Services

```ts
class PaymentService {
  processPayment(amount: number) {
    if (this.validateAmount(amount)) {
      console.log(`Processing payment of $${amount}`);
    }
  }

  private validateAmount(amount: number): boolean {
    return amount > 0;
  }
}

const service = new PaymentService();
service.processPayment(100); // ✅ works
// service.validateAmount(100); ❌ private, cannot call directly
```

* Internal helper methods can be **private**, exposing only the necessary public API.

---

### 3.3 Using `protected` in Class Hierarchies

```ts
class Employee {
  constructor(protected salary: number) {} // accessible in subclasses
}

class Manager extends Employee {
  increaseSalary(amount: number) {
    this.salary += amount; // ✅ protected allows access here
  }

  getSalary() {
    return this.salary;
  }
}

const manager = new Manager(5000);
manager.increaseSalary(500);
console.log(manager.getSalary()); // 5500
// console.log(manager.salary); ❌ cannot access directly outside class
```

* `protected` lets **subclasses access members** while keeping them hidden from external code.

---

## 4. **Best Practices**

1. **Use `private` for sensitive data or internal helpers**

   * Example: passwords, database connections, internal validation methods

2. **Use `protected` for extensible base classes**

   * Example: allow subclasses to modify certain properties, but not external code

3. **Use `public` for properties/methods meant to be part of the API**

   * Example: user-facing methods like `checkPassword()`, `processPayment()`

4. **Avoid exposing everything**

   * Defaulting to public can lead to **accidental misuse** in large codebases

---

## 5. **Why Access Modifiers Matter in Production**

* **Encapsulation:** Keeps internal data and logic safe
* **Maintainability:** Clear boundaries on what can be used externally
* **Security:** Protect sensitive fields like passwords or API keys
* **Code reliability:** Reduces accidental mutations or misuse

---

### ✅ TL;DR – Real Project Mindset

| Modifier    | Example Use Case                                         |
| ----------- | -------------------------------------------------------- |
| `public`    | API-facing methods (`processPayment`, `checkPassword`)   |
| `private`   | Sensitive data (`password`, internal helpers)            |
| `protected` | Extendable base classes (`salary` in Employee → Manager) |
