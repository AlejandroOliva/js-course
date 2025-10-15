# Module 3: Advanced JavaScript Concepts

## Closures and Lexical Scoping

### Understanding Closures

A closure is a function that has access to variables in its outer (enclosing) scope even after the outer function has returned.

```javascript
// Basic closure example
function outerFunction(x) {
    // Outer function's variable
    let outerVariable = x;
    
    // Inner function (closure)
    function innerFunction(y) {
        return outerVariable + y; // Access to outer variable
    }
    
    return innerFunction;
}

let addFive = outerFunction(5);
console.log(addFive(3)); // 8
console.log(addFive(10)); // 15

// The outer variable persists even after outerFunction has returned
```

### Practical Closure Examples

```javascript
// Counter with closure
function createCounter() {
    let count = 0;
    
    return {
        increment: function() {
            count++;
            return count;
        },
        decrement: function() {
            count--;
            return count;
        },
        getCount: function() {
            return count;
        }
    };
}

let counter = createCounter();
console.log(counter.increment()); // 1
console.log(counter.increment()); // 2
console.log(counter.getCount()); // 2

// Private variables simulation
function createBankAccount(initialBalance) {
    let balance = initialBalance;
    
    return {
        deposit: function(amount) {
            balance += amount;
            return balance;
        },
        withdraw: function(amount) {
            if (amount <= balance) {
                balance -= amount;
                return balance;
            } else {
                throw new Error("Insufficient funds");
            }
        },
        getBalance: function() {
            return balance;
        }
    };
}

let account = createBankAccount(1000);
console.log(account.deposit(500)); // 1500
console.log(account.withdraw(200)); // 1300
// console.log(account.balance); // undefined (private)
```

### Common Closure Patterns

```javascript
// Module pattern
let calculator = (function() {
    let result = 0;
    
    return {
        add: function(x) {
            result += x;
            return this;
        },
        multiply: function(x) {
            result *= x;
            return this;
        },
        getResult: function() {
            return result;
        },
        reset: function() {
            result = 0;
            return this;
        }
    };
})();

// Method chaining
calculator.add(5).multiply(2).add(3).getResult(); // 13

// Function factory
function createMultiplier(factor) {
    return function(number) {
        return number * factor;
    };
}

let double = createMultiplier(2);
let triple = createMultiplier(3);
console.log(double(5)); // 10
console.log(triple(5)); // 15
```

## Promises and Async/Await

### Understanding Promises

A Promise represents the eventual completion (or failure) of an asynchronous operation.

```javascript
// Creating a Promise
function fetchUserData(userId) {
    return new Promise((resolve, reject) => {
        // Simulate API call
        setTimeout(() => {
            if (userId > 0) {
                resolve({
                    id: userId,
                    name: "John Doe",
                    email: "john@example.com"
                });
            } else {
                reject(new Error("Invalid user ID"));
            }
        }, 1000);
    });
}

// Using Promises
fetchUserData(1)
    .then(user => {
        console.log("User data:", user);
        return user.name;
    })
    .then(name => {
        console.log("User name:", name);
    })
    .catch(error => {
        console.error("Error:", error.message);
    });
```

### Promise Methods

```javascript
// Promise.all - wait for all promises to resolve
let promise1 = fetchUserData(1);
let promise2 = fetchUserData(2);
let promise3 = fetchUserData(3);

Promise.all([promise1, promise2, promise3])
    .then(users => {
        console.log("All users:", users);
    })
    .catch(error => {
        console.error("One or more requests failed:", error);
    });

// Promise.race - first promise to resolve/reject wins
Promise.race([promise1, promise2, promise3])
    .then(user => {
        console.log("First user received:", user);
    });

// Promise.allSettled - wait for all promises to settle
Promise.allSettled([promise1, promise2, promise3])
    .then(results => {
        results.forEach((result, index) => {
            if (result.status === 'fulfilled') {
                console.log(`Promise ${index + 1} succeeded:`, result.value);
            } else {
                console.log(`Promise ${index + 1} failed:`, result.reason);
            }
        });
    });
```

### Async/Await

```javascript
// Converting Promise to async/await
async function getUserData(userId) {
    try {
        const user = await fetchUserData(userId);
        console.log("User data:", user);
        return user;
    } catch (error) {
        console.error("Error:", error.message);
        throw error;
    }
}

// Multiple async operations
async function getMultipleUsers() {
    try {
        const user1 = await fetchUserData(1);
        const user2 = await fetchUserData(2);
        const user3 = await fetchUserData(3);
        
        return [user1, user2, user3];
    } catch (error) {
        console.error("Error fetching users:", error);
        return [];
    }
}

// Parallel async operations
async function getMultipleUsersParallel() {
    try {
        const [user1, user2, user3] = await Promise.all([
            fetchUserData(1),
            fetchUserData(2),
            fetchUserData(3)
        ]);
        
        return [user1, user2, user3];
    } catch (error) {
        console.error("Error fetching users:", error);
        return [];
    }
}
```

## Modules and Imports/Exports

### ES6 Modules

```javascript
// math.js - Exporting functions
export function add(a, b) {
    return a + b;
}

export function subtract(a, b) {
    return a - b;
}

export function multiply(a, b) {
    return a * b;
}

export function divide(a, b) {
    if (b === 0) {
        throw new Error("Division by zero");
    }
    return a / b;
}

// Default export
export default function calculator(operation, a, b) {
    switch (operation) {
        case 'add': return add(a, b);
        case 'subtract': return subtract(a, b);
        case 'multiply': return multiply(a, b);
        case 'divide': return divide(a, b);
        default: throw new Error("Unknown operation");
    }
}

// user.js - Exporting classes and constants
export const API_URL = "https://api.example.com";

export class User {
    constructor(name, email) {
        this.name = name;
        this.email = email;
    }
    
    greet() {
        return `Hello, I'm ${this.name}`;
    }
}

export default class AdminUser extends User {
    constructor(name, email, permissions) {
        super(name, email);
        this.permissions = permissions;
    }
    
    hasPermission(permission) {
        return this.permissions.includes(permission);
    }
}
```

### Importing Modules

```javascript
// main.js - Different import patterns
import calculator, { add, subtract, multiply, divide } from './math.js';
import { User, AdminUser, API_URL } from './user.js';

// Using imported functions
console.log(add(5, 3)); // 8
console.log(calculator('multiply', 4, 6)); // 24

// Using imported classes
let user = new User("John", "john@example.com");
let admin = new AdminUser("Jane", "jane@example.com", ["read", "write", "delete"]);

console.log(user.greet()); // "Hello, I'm John"
console.log(admin.hasPermission("write")); // true

// Namespace import
import * as MathUtils from './math.js';
console.log(MathUtils.add(2, 3)); // 5

// Dynamic import
async function loadModule() {
    const { default: Calculator } = await import('./math.js');
    return Calculator;
}
```

## Classes and Inheritance

### ES6 Classes

```javascript
// Base class
class Animal {
    constructor(name, species) {
        this.name = name;
        this.species = species;
    }
    
    // Instance method
    speak() {
        return `${this.name} makes a sound`;
    }
    
    // Getter
    get info() {
        return `${this.name} is a ${this.species}`;
    }
    
    // Setter
    set nickname(value) {
        this._nickname = value;
    }
    
    get nickname() {
        return this._nickname || this.name;
    }
    
    // Static method
    static getKingdom() {
        return "Animalia";
    }
}

// Inheritance
class Dog extends Animal {
    constructor(name, breed) {
        super(name, "Canine"); // Call parent constructor
        this.breed = breed;
    }
    
    // Override method
    speak() {
        return `${this.name} barks`;
    }
    
    // New method
    fetch() {
        return `${this.name} fetches the ball`;
    }
}

// Using classes
let dog = new Dog("Buddy", "Golden Retriever");
console.log(dog.speak()); // "Buddy barks"
console.log(dog.info); // "Buddy is a Canine"
console.log(dog.fetch()); // "Buddy fetches the ball"
console.log(Animal.getKingdom()); // "Animalia"
```

### Advanced Class Features

```javascript
// Private fields (ES2022)
class BankAccount {
    #balance = 0; // Private field
    #accountNumber; // Private field
    
    constructor(accountNumber, initialBalance = 0) {
        this.#accountNumber = accountNumber;
        this.#balance = initialBalance;
    }
    
    deposit(amount) {
        if (amount > 0) {
            this.#balance += amount;
            return this.#balance;
        }
        throw new Error("Amount must be positive");
    }
    
    withdraw(amount) {
        if (amount > 0 && amount <= this.#balance) {
            this.#balance -= amount;
            return this.#balance;
        }
        throw new Error("Invalid withdrawal amount");
    }
    
    getBalance() {
        return this.#balance;
    }
    
    getAccountNumber() {
        return this.#accountNumber;
    }
}

// Mixins
let Flyable = {
    fly() {
        return `${this.name} is flying`;
    }
};

let Swimmable = {
    swim() {
        return `${this.name} is swimming`;
    }
};

class Bird extends Animal {
    constructor(name) {
        super(name, "Bird");
    }
}

// Apply mixins
Object.assign(Bird.prototype, Flyable);

let bird = new Bird("Eagle");
console.log(bird.fly()); // "Eagle is flying"
```

## Prototypes and the `this` Keyword

### Understanding Prototypes

```javascript
// Every object has a prototype
let obj = {};
console.log(obj.__proto__ === Object.prototype); // true

// Adding methods to prototype
function Person(name) {
    this.name = name;
}

Person.prototype.greet = function() {
    return `Hello, I'm ${this.name}`;
};

Person.prototype.introduce = function() {
    return `My name is ${this.name}`;
};

let person1 = new Person("John");
let person2 = new Person("Jane");

console.log(person1.greet()); // "Hello, I'm John"
console.log(person2.greet()); // "Hello, I'm Jane"

// All instances share the same prototype methods
console.log(person1.greet === person2.greet); // true
```

### The `this` Keyword

```javascript
// Global context
console.log(this === window); // true (in browser)

// Function context
function regularFunction() {
    console.log(this); // undefined (strict mode) or global object
}

// Method context
let obj = {
    name: "MyObject",
    method: function() {
        console.log(this.name); // "MyObject"
    },
    arrowMethod: () => {
        console.log(this.name); // undefined (arrow functions don't have their own this)
    }
};

obj.method(); // "MyObject"
obj.arrowMethod(); // undefined

// Constructor context
function Car(brand) {
    this.brand = brand;
    this.start = function() {
        return `${this.brand} is starting`;
    };
}

let myCar = new Car("Toyota");
console.log(myCar.start()); // "Toyota is starting"

// Explicit binding
function greet() {
    return `Hello, ${this.name}`;
}

let person = { name: "John" };
console.log(greet.call(person)); // "Hello, John"
console.log(greet.apply(person)); // "Hello, John"

let boundGreet = greet.bind(person);
console.log(boundGreet()); // "Hello, John"
```

### Advanced `this` Patterns

```javascript
// Arrow functions and this
class Timer {
    constructor() {
        this.seconds = 0;
    }
    
    start() {
        // Regular function - this refers to the timer instance
        setInterval(function() {
            this.seconds++;
            console.log(this.seconds);
        }, 1000);
        
        // Arrow function - this refers to the timer instance
        setInterval(() => {
            this.seconds++;
            console.log(this.seconds);
        }, 1000);
    }
}

// Method borrowing
let calculator = {
    add: function(a, b) {
        return a + b;
    }
};

let mathUtils = {
    numbers: [1, 2, 3, 4, 5],
    sum: function() {
        return this.numbers.reduce(this.add, 0);
    }
};

// Borrow the add method
mathUtils.add = calculator.add;
console.log(mathUtils.sum()); // 15
```

## Exercises

### Exercise 1: Closure Counter Factory
Create a function that generates counters with different increment values:
```javascript
let counter1 = createCounter(1); // increments by 1
let counter2 = createCounter(5); // increments by 5
```

### Exercise 2: Promise-based File Reader
Create a function that simulates reading files asynchronously:
1. Takes a filename and returns a Promise
2. Simulates different file sizes with different delays
3. Handles file not found errors

### Exercise 3: Module System
Create a simple calculator module with:
1. Basic operations (add, subtract, multiply, divide)
2. History tracking
3. Export as both named and default exports

### Exercise 4: Class Hierarchy
Create a class hierarchy for vehicles:
1. Base Vehicle class
2. Car and Motorcycle subclasses
3. Use inheritance and method overriding
4. Add mixins for different capabilities

## Next Steps

In the next module, we'll start working with Node.js:
- Setting up Node.js projects
- Understanding npm and package management
- File system operations
- Building CLI applications
- Introduction to web servers

---

**Practice Tip:** Experiment with closures and async/await patterns - they're fundamental to modern JavaScript development!
