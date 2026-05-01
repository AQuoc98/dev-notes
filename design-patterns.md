**Status:** 🚧 - **Last Updated:** 1st May 2026

# Table of Contents
- [Table of Contents](#table-of-contents)
  - [Resources](#resources)
  - [MVC](#mvc)
    - [Core Components](#core-components)
      - [Model](#model)
      - [View](#view)
      - [Controller](#controller)
    - [Why Use MVC](#why-use-mvc)
    - [Workflow](#workflow)
    - [Example](#example)
      - [Execution Flow](#execution-flow)
  - [OOP](#oop)
    - [Class and Object](#class-and-object)
    - [Encapsulation](#encapsulation)
    - [Inheritance](#inheritance)
    - [Polymorphism](#polymorphism)
    - [Abstraction](#abstraction)
    - [Access Modifiers](#access-modifiers)
    - [Creating Objects](#creating-objects)
      - [1. Define a Class](#1-define-a-class)
      - [2. Create Objects](#2-create-objects)

## Resources

- [Design Patterns - Viblo](https://viblo.asia/p/design-patterns-la-gi-tai-sao-no-lai-la-tro-thu-dac-luc-cua-developers-tong-hop-23-mau-design-pattern-GrLZDBQV5k0)
- [SOLID Principles - Toi Di Code Dao](https://toidicodedao.com/2015/03/24/solid-la-gi-ap-dung-cac-nguyen-ly-solid-de-tro-thanh-lap-trinh-vien-code-cung/)

## MVC

MVC (Model - View - Controller) is a software design pattern that separates an application into three core components, making the code easier to maintain and scale.

### Core Components

#### Model
Responsible for data and business logic.
- Interacts with the database (create, read, update, delete).
- Handles calculations and data rules.
- Example: In a sales application, the Model holds product information, prices, and inventory.

#### View
This is what the user sees and interacts with.
- Displays data from the Model (via the Controller).
- Receives user input (mouse clicks, form entries).
- Example: HTML pages, buttons, revenue charts.

#### Controller
Acts as the "coordinator" — the middleman between Model and View.
- Receives requests from the user through the View.
- Calls the Model to fetch or update the necessary data.
- Sends that data back to the View to be rendered.
- Example: When you click the "Login" button, the Controller takes the username/password you entered, asks the Model to verify them, then tells the View to display "Success" or "Failure".

### Why Use MVC

- **Easy to maintain:** Want to change the UI (View) from blue to red? Go ahead — you don't need to touch the data-handling logic (Model).
- **Parallel development:** Front-end developers can design the View while back-end developers build the Model without stepping on each other's toes.
- **Code reusability:** A single Model can be rendered by multiple Views (e.g. revenue data shown as a table or as a chart).

### Workflow

1. The user interacts with the View (sends a request).
2. The Controller receives the request and performs the initial logic.
3. The Controller asks the Model to fetch or update data.
4. The Model finishes its work and responds to the Controller.
5. The Controller passes the new data to the View.
6. The View updates the UI so the user can see the result.

### Example

**Model** — Its only job is to talk to the database to retrieve data. It doesn't care what the UI looks like.

```php
// UserModel.php
class UserModel {
    public function getUserById($id) {
        // Imagine this is a database query
        // SELECT * FROM users WHERE id = $id
        $users = [
            1 => ['name' => 'Nguyen Van A', 'email' => 'ana@gmail.com'],
            2 => ['name' => 'Tran Thi B', 'email' => 'btra@gmail.com']
        ];

        return $users[$id] ?? null;
    }
}
```

**View** — Its only job is to render data on the screen (typically as HTML). It doesn't care where the data comes from.

```php
<html>
<head>
    <title>User Information</title>
</head>
<body>
    <h1>Profile</h1>
    <?php if ($user): ?>
        <p>Name: <strong><?php echo $user['name']; ?></strong></p>
        <p>Email: <strong><?php echo $user['email']; ?></strong></p>
    <?php else: ?>
        <p>User not found!</p>
    <?php endif; ?>
</body>
</html>
```

**Controller** — Receives the ID from the URL, calls the Model to fetch data, then passes that data to the View.

```php
// UserController.php
class UserController {
    public function showProfile($id) {
        // 1. Call the Model to get the data
        $model = new UserModel();
        $userData = $model->getUserById($id);

        // 2. Pass the data to the View and render it
        $user = $userData;
        include 'UserView.php';
    }
}
```

#### Execution Flow

1. The user visits the URL: `website.com/user/show/1`.
2. The Controller receives `1` and runs `showProfile(1)`.
3. The Controller asks the Model: "Hey, give me the data for the user with ID 1."
4. The Model looks up the database, finds "Nguyen Van A", and returns it to the Controller.
5. The Controller takes that data, opens the View, and says: "Hey View, fill this user's information into your HTML and render it for the user."
6. The user sees the page displaying the name "Nguyen Van A".

[Back to top](#table-of-contents)

## OOP

- If MVC is the "framework" used to build a house, then OOP (Object-Oriented Programming) is the "brick" and the "design philosophy" for each piece of furniture inside that house.
- OOP is not a language but a programming mindset that puts the **Object** at the center, instead of functions or sequential steps.

### Class and Object

- **Class:** A blueprint. For example, the technical drawing of a car. It defines what a car has (color, engine) and what it can do (drive, brake).
- **Object:** A concrete entity created from that blueprint. For example, the red Toyota with plate 29A, or the black BMW with plate 51H.

### Encapsulation

- **Purpose:** Protect data from being arbitrarily modified from the outside.
- **Example:** We want to protect the salary data and prevent anyone from setting it to a negative value. We use `private` together with Getter/Setter methods.

```java
class BankAccount {
    private double balance; // No one can access this field directly

    public void deposit(double amount) {
        if (amount > 0) {
            this.balance += amount;
            System.out.println("Deposit successful!");
        } else {
            System.out.println("Invalid amount!");
        }
    }

    public double getBalance() {
        return balance;
    }
}
```

### Inheritance

Allows one class (child class) to reuse the properties and methods of another class (parent class).

- **Purpose:** Reuse code and avoid duplication.
- **Example:** We create specific employee types that inherit from the `Employee` class. Full-time employees get a bonus; part-time employees are paid by the hour.

```java
// Child class inheriting from Employee
class FullTimeEmployee extends Employee {
    private double bonus;

    public FullTimeEmployee(String name, double baseSalary, double bonus) {
        super(name, baseSalary); // Call the parent constructor
        this.bonus = bonus;
    }

    @Override
    public double calculateSalary() {
        return baseSalary + bonus;
    }
}

class PartTimeEmployee extends Employee {
    private int hoursWorked;

    public PartTimeEmployee(String name, double hourlyRate, int hoursWorked) {
        super(name, hourlyRate);
        this.hoursWorked = hoursWorked;
    }

    @Override
    public double calculateSalary() {
        return baseSalary * hoursWorked; // Here baseSalary acts as the hourly rate
    }
}
```

### Polymorphism

A single action can be performed in multiple ways depending on the object performing it.

- **Purpose:** Make code flexible — one function can handle many types of objects.
- **Example:** You can create a generic list of `Employee`, but when calling `calculateSalary()`, the runtime automatically picks the correct formula for each one.

```java
public class Main {
    public static void main(String[] args) {
        // Create a list containing different types of employees
        Employee[] employees = new Employee[2];

        employees[0] = new FullTimeEmployee("Nguyen Van A", 1000, 200);
        employees[1] = new PartTimeEmployee("Tran Thi B", 20, 80);

        // Iterate over the list (Polymorphism in action)
        for (Employee emp : employees) {
            // Same calculateSalary() call, but each runs a different formula
            System.out.println("Employee: " + emp.name + " - Salary: " + emp.calculateSalary());
        }
    }
}
```

### Abstraction

Show only the necessary information and hide the complex implementation details.

- **Purpose:** Reduce complexity. The user only needs to know "what it does", not "how it does it".
- **Example:** We create an abstract `Employee` class. We know every employee must "calculate salary", but each type calculates it differently. We don't need to define the concrete formula here.

```java
abstract class Employee {
    protected String name;
    protected double baseSalary;

    public Employee(String name, double baseSalary) {
        this.name = name;
        this.baseSalary = baseSalary;
    }

    // Abstract method: only declared, no implementation
    public abstract double calculateSalary();
}
```

### Access Modifiers

- **Public:** Use when you want to expose methods to outside callers.
- **Private:** Use when you want to protect important data (Encapsulation).
- **Protected:** Use when you want descendant classes to inherit access, but not strangers.
- **Default:** Used when no keyword is specified. Access is allowed only within the same package (folder).

| Access Modifier      | Same Class | Same Package | Subclass (Inheritance) | Everywhere |
| -------------------- | :--------: | :----------: | :--------------------: | :--------: |
| `public`             |     ✅     |      ✅      |           ✅           |     ✅     |
| `protected`          |     ✅     |      ✅      |           ✅           |     ❌     |
| `default` (no modifier) |  ✅     |      ✅      |           ❌           |     ❌     |
| `private`            |     ✅     |      ❌      |           ❌           |     ❌     |

### Creating Objects

#### 1. Define a Class

```java
class SuperHero {
    // 1. Properties (Data)
    String name;
    String power;

    // 2. Constructor (runs immediately when 'new' is used)
    public SuperHero(String heroName, String ability) {
        this.name = heroName;
        this.power = ability;
        System.out.println("--- Summoned: " + this.name + " ---");
    }

    // 3. Method (Behavior)
    public void attack() {
        System.out.println(name + " is using: " + power);
    }
}
```

#### 2. Create Objects

```java
public class Game {
    public static void main(String[] args) {
        // CREATE THE FIRST OBJECT
        SuperHero ironMan = new SuperHero("Iron Man", "High-tech armor");

        // CREATE THE SECOND OBJECT
        SuperHero spiderMan = new SuperHero("Spider Man", "Web shooting");

        // Use the objects
        ironMan.attack();   // Output: Iron Man is using: High-tech armor
        spiderMan.attack(); // Output: Spider Man is using: Web shooting
    }
}
```

[Back to top](#table-of-contents)