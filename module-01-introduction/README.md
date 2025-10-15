# Module 1: Introduction to JavaScript

## What is JavaScript?

JavaScript is a high-level, interpreted programming language that was originally created to make web pages interactive. Today, it's one of the most popular programming languages in the world and can be used for:

- **Frontend development** - Making websites interactive
- **Backend development** - Server-side applications with Node.js
- **Mobile development** - React Native, Ionic
- **Desktop applications** - Electron
- **Game development** - With libraries like Phaser
- **IoT and embedded systems** - With platforms like Johnny-Five

## Why JavaScript?

### Advantages:
- **Universal** - Runs everywhere (browser, server, mobile, desktop)
- **Large ecosystem** - Millions of packages on npm
- **Active community** - Constant updates and improvements
- **Easy to start** - No complex setup required
- **High demand** - Most sought-after skill in web development
- **Flexible** - Supports multiple programming paradigms

### Key Features:
- **Dynamic typing** - Variables can change types
- **First-class functions** - Functions are treated as values
- **Prototype-based inheritance** - Objects inherit from other objects
- **Asynchronous programming** - Non-blocking operations
- **Event-driven** - Responds to user interactions

## Setting Up Your Development Environment

### 1. Install Node.js

#### Option A: Using ASDF (Recommended)

ASDF is a version manager that allows you to easily install and manage multiple versions of Node.js, just like rbenv for Ruby or nvm for Node.js.

**Install ASDF:**
```bash
# Install ASDF
git clone https://github.com/asdf-vm/asdf.git ~/.asdf --branch v0.14.0

# Add to your shell profile (~/.bashrc, ~/.zshrc, etc.)
echo '. "$HOME/.asdf/asdf.sh"' >> ~/.bashrc
echo '. "$HOME/.asdf/completions/asdf.bash"' >> ~/.bashrc

# Reload your shell
source ~/.bashrc
```

**Install Node.js plugin:**
```bash
# Add Node.js plugin
asdf plugin add nodejs https://github.com/asdf-vm/asdf-nodejs.git

# Install latest LTS version
asdf install nodejs lts

# Set as global default
asdf global nodejs lts

# Verify installation
node --version
npm --version
```

**ASDF Benefits:**
- Easy version switching
- Project-specific Node.js versions
- No sudo required
- Works with multiple languages

#### Option B: Official Installer

Visit [nodejs.org](https://nodejs.org) and download the LTS (Long Term Support) version.

**Verify installation:**
```bash
node --version
npm --version
```

### 2. Choose a Code Editor

**Recommended options:**
- **Visual Studio Code** (free, most popular)
- **WebStorm** (paid, full-featured IDE)
- **Sublime Text** (paid, lightweight)
- **Atom** (free, GitHub's editor)

### 3. Browser Developer Tools

Learn to use browser DevTools:
- **Chrome DevTools** - Press F12 or Ctrl+Shift+I
- **Firefox Developer Tools** - Press F12 or Ctrl+Shift+I
- **Safari Web Inspector** - Enable in Preferences

## JavaScript Runtime Environments

### 1. Browser Environment
- **V8 Engine** (Chrome, Edge)
- **SpiderMonkey** (Firefox)
- **JavaScriptCore** (Safari)

### 2. Node.js Environment
- **V8 Engine** with additional APIs
- File system access
- Network operations
- Process management

## Basic Syntax and Variables

### Variables Declaration

```javascript
// ES5 (older way)
var name = "John";

// ES6+ (modern way)
let age = 25;
const city = "New York";
```

**Key differences:**
- `var` - Function-scoped, can be redeclared
- `let` - Block-scoped, cannot be redeclared
- `const` - Block-scoped, cannot be reassigned

### Data Types

```javascript
// Primitive types
let string = "Hello World";
let number = 42;
let boolean = true;
let undefinedVar = undefined;
let nullVar = null;
let symbol = Symbol('id');

// Check type
console.log(typeof string); // "string"
console.log(typeof number); // "number"
console.log(typeof boolean); // "boolean"
```

### Basic Operations

```javascript
// Arithmetic
let sum = 10 + 5;
let difference = 10 - 5;
let product = 10 * 5;
let quotient = 10 / 5;
let remainder = 10 % 3;

// String operations
let firstName = "John";
let lastName = "Doe";
let fullName = firstName + " " + lastName; // "John Doe"
let template = `${firstName} ${lastName}`; // "John Doe" (template literals)

// Comparison
console.log(5 > 3); // true
console.log(5 === 5); // true (strict equality)
console.log(5 == "5"); // true (loose equality)
console.log(5 === "5"); // false (strict equality)
```

### Console Output

```javascript
// Different console methods
console.log("Basic message");
console.error("Error message");
console.warn("Warning message");
console.info("Info message");
console.table([{name: "John", age: 25}, {name: "Jane", age: 30}]);
```

## Your First JavaScript Program

### In Browser (HTML file)
```html
<!DOCTYPE html>
<html>
<head>
    <title>My First JS Program</title>
</head>
<body>
    <h1>Hello JavaScript!</h1>
    <script>
        console.log("Hello from JavaScript!");
        alert("Welcome to JavaScript!");
    </script>
</body>
</html>
```

### In Node.js (Terminal)
```javascript
// Create a file called hello.js
console.log("Hello from Node.js!");
console.log("Current time:", new Date().toLocaleString());
```

Run it:
```bash
node hello.js
```

## Interactive JavaScript (Browser Console)

Open your browser's developer tools and try these commands:

```javascript
// Basic calculations
2 + 2
10 * 5
Math.sqrt(16)

// String operations
"Hello" + " " + "World"
"JavaScript".length
"JavaScript".toUpperCase()

// Date operations
new Date()
new Date().getFullYear()
```

## Exercises

### Exercise 1: Variables and Basic Operations
Create a program that:
1. Declares variables for your name, age, and city
2. Calculates your age in days (approximate)
3. Prints a message combining all information

### Exercise 2: Temperature Converter
Create a program that:
1. Converts Celsius to Fahrenheit
2. Formula: F = (C * 9/5) + 32
3. Test with different temperatures

### Exercise 3: Simple Calculator
Create a program that:
1. Takes two numbers
2. Performs basic operations (+, -, *, /)
3. Displays results

## Next Steps

In the next module, we'll dive deeper into JavaScript fundamentals including:
- More data types and structures
- Functions and scope
- Objects and arrays
- Control flow

---

**Practice Tip:** Try running all examples in both browser console and Node.js to see the differences!
