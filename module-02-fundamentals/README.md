# Module 2: JavaScript Fundamentals

## Data Types and Structures

### Primitive Data Types

```javascript
// String
let message = "Hello World";
let singleQuote = 'Hello World';
let template = `Hello ${name}`; // Template literal

// Number
let integer = 42;
let decimal = 3.14;
let scientific = 1e6; // 1,000,000
let infinity = Infinity;
let notANumber = NaN;

// Boolean
let isTrue = true;
let isFalse = false;

// Undefined
let undefinedVar; // undefined

// Null
let nullVar = null;

// Symbol (ES6)
let id = Symbol('id');
let anotherId = Symbol('id');
console.log(id === anotherId); // false (unique)

// BigInt (ES2020)
let bigNumber = 123456789012345678901234567890n;
```

### Type Checking and Conversion

```javascript
// Type checking
console.log(typeof "hello"); // "string"
console.log(typeof 42); // "number"
console.log(typeof true); // "boolean"
console.log(typeof undefined); // "undefined"
console.log(typeof null); // "object" (historical quirk)

// Type conversion
let str = "123";
let num = Number(str); // 123
let bool = Boolean(str); // true
let str2 = String(num); // "123"

// Automatic conversion (coercion)
console.log("5" + 3); // "53" (string concatenation)
console.log("5" - 3); // 2 (numeric subtraction)
console.log("5" * 3); // 15 (numeric multiplication)
```

### Objects

```javascript
// Object literal
let person = {
    name: "John",
    age: 30,
    city: "New York",
    isStudent: false,
    hobbies: ["reading", "coding", "gaming"],
    address: {
        street: "123 Main St",
        zipCode: "10001"
    }
};

// Accessing properties
console.log(person.name); // "John"
console.log(person["age"]); // 30
console.log(person.address.street); // "123 Main St"

// Adding properties
person.email = "john@example.com";
person["phone"] = "555-1234";

// Methods
person.greet = function() {
    return `Hello, I'm ${this.name}`;
};

console.log(person.greet()); // "Hello, I'm John"
```

### Arrays

```javascript
// Array creation
let fruits = ["apple", "banana", "orange"];
let numbers = [1, 2, 3, 4, 5];
let mixed = ["hello", 42, true, {name: "John"}];

// Accessing elements
console.log(fruits[0]); // "apple"
console.log(fruits[fruits.length - 1]); // "orange"

// Array methods
fruits.push("grape"); // Add to end
fruits.pop(); // Remove from end
fruits.unshift("kiwi"); // Add to beginning
fruits.shift(); // Remove from beginning

// Iteration
fruits.forEach(function(fruit) {
    console.log(fruit);
});

// Modern arrow function syntax
fruits.forEach(fruit => console.log(fruit));

// Map (transform array)
let upperFruits = fruits.map(fruit => fruit.toUpperCase());

// Filter
let longFruits = fruits.filter(fruit => fruit.length > 5);

// Find
let foundFruit = fruits.find(fruit => fruit.startsWith("a"));
```

## Functions and Scope

### Function Declarations

```javascript
// Function declaration
function greet(name) {
    return `Hello, ${name}!`;
}

// Function expression
const greet2 = function(name) {
    return `Hello, ${name}!`;
};

// Arrow function (ES6)
const greet3 = (name) => {
    return `Hello, ${name}!`;
};

// Arrow function (shorter)
const greet4 = name => `Hello, ${name}!`;

// Multiple parameters
function add(a, b) {
    return a + b;
}

// Default parameters
function greetWithDefault(name = "World") {
    return `Hello, ${name}!`;
}

// Rest parameters
function sum(...numbers) {
    return numbers.reduce((total, num) => total + num, 0);
}

console.log(sum(1, 2, 3, 4, 5)); // 15
```

### Scope

```javascript
// Global scope
let globalVar = "I'm global";

function outerFunction() {
    // Function scope
    let functionVar = "I'm in function scope";
    
    function innerFunction() {
        // Inner function scope
        let innerVar = "I'm in inner function";
        console.log(globalVar); // Accessible
        console.log(functionVar); // Accessible
        console.log(innerVar); // Accessible
    }
    
    innerFunction();
    // console.log(innerVar); // Error: not accessible
}

// Block scope (let/const)
if (true) {
    let blockVar = "I'm in block scope";
    const blockConst = "I'm also in block scope";
}
// console.log(blockVar); // Error: not accessible
```

### Higher-Order Functions

```javascript
// Functions that take other functions as parameters
function processArray(array, processor) {
    return array.map(processor);
}

let numbers = [1, 2, 3, 4, 5];
let doubled = processArray(numbers, x => x * 2);
console.log(doubled); // [2, 4, 6, 8, 10]

// Functions that return functions
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

## Control Flow

### Conditional Statements

```javascript
// if/else
let age = 18;
if (age >= 18) {
    console.log("You are an adult");
} else if (age >= 13) {
    console.log("You are a teenager");
} else {
    console.log("You are a child");
}

// Ternary operator
let status = age >= 18 ? "adult" : "minor";

// Switch statement
let day = "Monday";
switch (day) {
    case "Monday":
        console.log("Start of work week");
        break;
    case "Friday":
        console.log("TGIF!");
        break;
    case "Saturday":
    case "Sunday":
        console.log("Weekend!");
        break;
    default:
        console.log("Regular day");
}
```

### Loops

```javascript
// for loop
for (let i = 0; i < 5; i++) {
    console.log(i);
}

// for...of loop (arrays)
let fruits = ["apple", "banana", "orange"];
for (let fruit of fruits) {
    console.log(fruit);
}

// for...in loop (objects)
let person = {name: "John", age: 30, city: "NYC"};
for (let key in person) {
    console.log(`${key}: ${person[key]}`);
}

// while loop
let count = 0;
while (count < 3) {
    console.log(count);
    count++;
}

// do...while loop
let x = 0;
do {
    console.log(x);
    x++;
} while (x < 3);
```

### Loop Control

```javascript
// break - exit loop
for (let i = 0; i < 10; i++) {
    if (i === 5) {
        break; // Exit loop
    }
    console.log(i);
}

// continue - skip iteration
for (let i = 0; i < 10; i++) {
    if (i % 2 === 0) {
        continue; // Skip even numbers
    }
    console.log(i); // Only odd numbers
}
```

## Error Handling

```javascript
// try/catch/finally
function divide(a, b) {
    try {
        if (b === 0) {
            throw new Error("Division by zero is not allowed");
        }
        return a / b;
    } catch (error) {
        console.error("Error:", error.message);
        return null;
    } finally {
        console.log("Division operation completed");
    }
}

// Custom error types
class ValidationError extends Error {
    constructor(message) {
        super(message);
        this.name = "ValidationError";
    }
}

function validateAge(age) {
    if (typeof age !== "number") {
        throw new ValidationError("Age must be a number");
    }
    if (age < 0) {
        throw new ValidationError("Age cannot be negative");
    }
    return true;
}

try {
    validateAge("not a number");
} catch (error) {
    if (error instanceof ValidationError) {
        console.log("Validation failed:", error.message);
    } else {
        console.log("Unexpected error:", error.message);
    }
}
```

## Exercises

### Exercise 1: Array Manipulation
Create a program that:
1. Takes an array of numbers
2. Finds the maximum and minimum values
3. Calculates the average
4. Returns an object with these statistics

### Exercise 2: Function Composition
Create functions for:
1. Adding 5 to a number
2. Multiplying by 2
3. Subtracting 3
4. Compose these functions to create a pipeline

### Exercise 3: Data Validation
Create a function that validates user input:
1. Name must be a non-empty string
2. Age must be a positive number
3. Email must contain "@" symbol
4. Return validation results

### Exercise 4: Temperature Converter with Error Handling
Enhance the temperature converter from Module 1:
1. Add input validation
2. Handle invalid inputs gracefully
3. Support both Celsius and Fahrenheit

## Next Steps

In the next module, we'll explore advanced JavaScript concepts including:
- Closures and lexical scoping
- Promises and async programming
- Modules and imports
- Classes and inheritance
- Prototypes and the `this` keyword

---

**Practice Tip:** Use the browser console or Node.js REPL to experiment with these concepts!
