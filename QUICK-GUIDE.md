# QUICK GUIDE - JavaScript & Node.js Cheat Sheet

## 📝 JavaScript Syntax Quick Reference

### Variables and Constants
```javascript
// Variable declarations
var name = "John";           // Function-scoped (avoid in modern JS)
let age = 25;               // Block-scoped
const city = "New York";    // Block-scoped, immutable

// Destructuring
const [first, second] = [1, 2];
const {name, age} = person;
```

### Data Types
```javascript
// Primitive types
let string = "Hello";
let number = 42;
let boolean = true;
let undefinedVar = undefined;
let nullVar = null;
let symbol = Symbol('id');

// Type checking
typeof variable;           // Returns type as string
Array.isArray(arr);        // Check if array
```

### Functions
```javascript
// Function declarations
function greet(name) {
    return `Hello ${name}`;
}

// Function expressions
const greet = function(name) {
    return `Hello ${name}`;
};

// Arrow functions
const greet = (name) => `Hello ${name}`;
const add = (a, b) => a + b;

// Default parameters
function greet(name = "World") {
    return `Hello ${name}`;
}

// Rest parameters
function sum(...numbers) {
    return numbers.reduce((total, num) => total + num, 0);
}
```

### Objects and Arrays
```javascript
// Object creation
const person = {
    name: "John",
    age: 30,
    greet() {
        return `Hello, I'm ${this.name}`;
    }
};

// Array methods
arr.push(item);           // Add to end
arr.pop();                // Remove from end
arr.unshift(item);        // Add to beginning
arr.shift();              // Remove from beginning
arr.map(fn);              // Transform array
arr.filter(fn);           // Filter array
arr.reduce(fn, initial);  // Reduce array
arr.find(fn);             // Find first match
arr.includes(item);       // Check if includes
```

### Control Flow
```javascript
// Conditionals
if (condition) {
    // code
} else if (condition) {
    // code
} else {
    // code
}

// Ternary operator
condition ? valueIfTrue : valueIfFalse;

// Switch statement
switch (value) {
    case 'option1':
        // code
        break;
    case 'option2':
        // code
        break;
    default:
        // code
}

// Loops
for (let i = 0; i < 10; i++) {
    // code
}

for (let item of array) {
    // code
}

for (let key in object) {
    // code
}

while (condition) {
    // code
}

do {
    // code
} while (condition);
```

### Async Programming
```javascript
// Promises
fetch('/api/data')
    .then(response => response.json())
    .then(data => console.log(data))
    .catch(error => console.error(error));

// Async/Await
async function fetchData() {
    try {
        const response = await fetch('/api/data');
        const data = await response.json();
        return data;
    } catch (error) {
        console.error(error);
    }
}

// Promise methods
Promise.all([promise1, promise2]);      // Wait for all
Promise.race([promise1, promise2]);     // First to resolve
Promise.allSettled([promise1, promise2]); // All settle
```

### Classes
```javascript
// Class definition
class Person {
    constructor(name, age) {
        this.name = name;
        this.age = age;
    }
    
    greet() {
        return `Hello, I'm ${this.name}`;
    }
    
    static getSpecies() {
        return 'Homo sapiens';
    }
}

// Inheritance
class Student extends Person {
    constructor(name, age, grade) {
        super(name, age);
        this.grade = grade;
    }
    
    study() {
        return `${this.name} is studying`;
    }
}
```

### Modules
```javascript
// Exporting
export const PI = 3.14159;
export function add(a, b) { return a + b; }
export default class Calculator {}

// Importing
import Calculator, { add, PI } from './math.js';
import * as MathUtils from './math.js';
```

## 🚀 Node.js Quick Reference

### Basic Server
```javascript
const http = require('http');

const server = http.createServer((req, res) => {
    res.writeHead(200, { 'Content-Type': 'text/html' });
    res.end('<h1>Hello World</h1>');
});

server.listen(3000, () => {
    console.log('Server running on port 3000');
});
```

### Express.js
```javascript
const express = require('express');
const app = express();

app.use(express.json());

// Routes
app.get('/', (req, res) => {
    res.json({ message: 'Hello World' });
});

app.post('/users', (req, res) => {
    const { name, email } = req.body;
    res.status(201).json({ name, email });
});

app.listen(3000, () => {
    console.log('Server running on port 3000');
});
```

### File System Operations
```javascript
const fs = require('fs');
const path = require('path');

// Synchronous
const data = fs.readFileSync('file.txt', 'utf8');
fs.writeFileSync('output.txt', 'Hello World');

// Asynchronous
fs.readFile('file.txt', 'utf8', (err, data) => {
    if (err) throw err;
    console.log(data);
});

// Promises
const fsPromises = require('fs').promises;
const data = await fsPromises.readFile('file.txt', 'utf8');
```

### Package Management
```bash
# Initialize project
npm init -y

# Install packages
npm install package-name
npm install --save-dev package-name
npm install -g package-name

# Run scripts
npm start
npm run script-name

# Update packages
npm update
npm outdated
```

## 🗄️ Database Operations

### MongoDB with Mongoose
```javascript
const mongoose = require('mongoose');

// Schema
const userSchema = new mongoose.Schema({
    name: String,
    email: String,
    age: Number
});

// Model
const User = mongoose.model('User', userSchema);

// Operations
const user = new User({ name: 'John', email: 'john@example.com' });
await user.save();

const users = await User.find();
const user = await User.findById(id);
await User.findByIdAndUpdate(id, { name: 'Jane' });
await User.findByIdAndDelete(id);
```

### PostgreSQL with pg
```javascript
const { Pool } = require('pg');

const pool = new Pool({
    user: 'postgres',
    host: 'localhost',
    database: 'myapp',
    password: 'password',
    port: 5432,
});

// Query
const result = await pool.query('SELECT * FROM users WHERE id = $1', [userId]);
const users = result.rows;
```

## 🔐 Authentication

### JWT Authentication
```javascript
const jwt = require('jsonwebtoken');
const bcrypt = require('bcryptjs');

// Generate token
const token = jwt.sign(
    { userId: user.id, email: user.email },
    JWT_SECRET,
    { expiresIn: '24h' }
);

// Verify token
jwt.verify(token, JWT_SECRET, (err, decoded) => {
    if (err) throw err;
    console.log(decoded);
});

// Hash password
const hashedPassword = await bcrypt.hash(password, 10);

// Compare password
const isValid = await bcrypt.compare(password, hashedPassword);
```

### Middleware
```javascript
function authenticateToken(req, res, next) {
    const token = req.headers['authorization']?.split(' ')[1];
    
    if (!token) {
        return res.status(401).json({ error: 'No token provided' });
    }
    
    jwt.verify(token, JWT_SECRET, (err, user) => {
        if (err) return res.status(403).json({ error: 'Invalid token' });
        req.user = user;
        next();
    });
}
```

## 🧪 Testing

### Jest
```javascript
// Basic test
test('adds 1 + 2 to equal 3', () => {
    expect(add(1, 2)).toBe(3);
});

// Async test
test('fetches user data', async () => {
    const user = await fetchUser(1);
    expect(user).toHaveProperty('id');
});

// Mock
jest.mock('./api');
api.getUser.mockResolvedValue({ id: 1, name: 'John' });
```

## 🛠️ Build Tools

### Webpack
```javascript
// webpack.config.js
module.exports = {
    entry: './src/index.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'bundle.js'
    },
    module: {
        rules: [
            {
                test: /\.js$/,
                exclude: /node_modules/,
                use: 'babel-loader'
            }
        ]
    }
};
```

### Package.json Scripts
```json
{
    "scripts": {
        "start": "node server.js",
        "dev": "nodemon server.js",
        "build": "webpack --mode production",
        "test": "jest",
        "lint": "eslint src/"
    }
}
```

## 🌐 HTTP Methods and Status Codes

### HTTP Methods
- `GET` - Retrieve data
- `POST` - Create new resource
- `PUT` - Update entire resource
- `PATCH` - Partial update
- `DELETE` - Remove resource

### Status Codes
- `200` - OK
- `201` - Created
- `400` - Bad Request
- `401` - Unauthorized
- `403` - Forbidden
- `404` - Not Found
- `500` - Internal Server Error

## 🔧 Common Commands

### Node.js
```bash
node script.js          # Run script
node --version          # Check version
npm --version           # Check npm version
npm init                # Initialize project
npm install             # Install dependencies
npm start               # Start application
npm test                # Run tests
npm run build           # Build project
```

### ASDF (Version Manager)
```bash
asdf plugin add nodejs  # Add Node.js plugin
asdf install nodejs lts # Install latest LTS
asdf global nodejs lts  # Set global version
asdf local nodejs 18.0.0 # Set project version
asdf list nodejs        # List installed versions
asdf current nodejs     # Show current version
```

### Git
```bash
git init                # Initialize repository
git add .               # Stage all changes
git commit -m "message" # Commit changes
git push                # Push to remote
git pull                # Pull from remote
git clone url           # Clone repository
git status              # Check status
git log                 # View history
```

## 📚 Useful Resources

### Documentation
- [MDN JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript)
- [Node.js Docs](https://nodejs.org/docs/)
- [Express.js Docs](https://expressjs.com/)

### Tools
- [VS Code](https://code.visualstudio.com/)
- [Postman](https://www.postman.com/)
- [MongoDB Compass](https://www.mongodb.com/products/compass)

### Learning
- [JavaScript.info](https://javascript.info/)
- [Node.js Tutorial](https://nodejs.org/en/learn/)
- [Express.js Guide](https://expressjs.com/en/guide/routing.html)

---

**Keep this cheat sheet handy while coding! 📖**
