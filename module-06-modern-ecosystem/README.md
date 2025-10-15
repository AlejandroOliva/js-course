# Module 6: Modern JavaScript Ecosystem

## ES6+ Features and Modern Syntax

### Destructuring Assignment

```javascript
// Array destructuring
const numbers = [1, 2, 3, 4, 5];
const [first, second, ...rest] = numbers;
console.log(first); // 1
console.log(second); // 2
console.log(rest); // [3, 4, 5]

// Object destructuring
const person = {
    name: 'John',
    age: 30,
    city: 'New York',
    hobbies: ['reading', 'coding']
};

const { name, age, ...other } = person;
console.log(name); // John
console.log(age); // 30
console.log(other); // { city: 'New York', hobbies: [...] }

// Destructuring with default values
const { name: fullName = 'Anonymous', country = 'USA' } = person;

// Destructuring in function parameters
function greet({ name, age = 18 }) {
    return `Hello ${name}, you are ${age} years old`;
}

console.log(greet({ name: 'Jane' })); // Hello Jane, you are 18 years old
```

### Template Literals and String Methods

```javascript
// Template literals
const name = 'John';
const age = 30;
const message = `Hello, my name is ${name} and I am ${age} years old`;

// Multi-line strings
const html = `
    <div class="user">
        <h2>${name}</h2>
        <p>Age: ${age}</p>
    </div>
`;

// String methods
const text = 'Hello World';
console.log(text.startsWith('Hello')); // true
console.log(text.endsWith('World')); // true
console.log(text.includes('lo')); // true
console.log(text.repeat(3)); // Hello WorldHello WorldHello World

// Pad methods
console.log('5'.padStart(3, '0')); // 005
console.log('5'.padEnd(3, '0')); // 500
```

### Arrow Functions and Lexical This

```javascript
// Arrow function syntax
const add = (a, b) => a + b;
const square = x => x * x;
const greet = () => 'Hello World';

// Arrow functions with objects
const person = {
    name: 'John',
    greet: function() {
        // Regular function - this refers to person
        console.log(`Hello, I'm ${this.name}`);
        
        // Arrow function - this refers to person (lexical this)
        setTimeout(() => {
            console.log(`Delayed: Hello, I'm ${this.name}`);
        }, 1000);
    }
};

// Arrow functions in array methods
const numbers = [1, 2, 3, 4, 5];
const doubled = numbers.map(n => n * 2);
const evens = numbers.filter(n => n % 2 === 0);
const sum = numbers.reduce((acc, n) => acc + n, 0);
```

### Classes and Inheritance

```javascript
// ES6 Classes
class Animal {
    constructor(name, species) {
        this.name = name;
        this.species = species;
    }
    
    speak() {
        return `${this.name} makes a sound`;
    }
    
    get info() {
        return `${this.name} is a ${this.species}`;
    }
    
    static getKingdom() {
        return 'Animalia';
    }
}

// Inheritance
class Dog extends Animal {
    constructor(name, breed) {
        super(name, 'Canine');
        this.breed = breed;
    }
    
    speak() {
        return `${this.name} barks`;
    }
    
    fetch() {
        return `${this.name} fetches the ball`;
    }
}

// Private fields (ES2022)
class BankAccount {
    #balance = 0;
    #accountNumber;
    
    constructor(accountNumber, initialBalance = 0) {
        this.#accountNumber = accountNumber;
        this.#balance = initialBalance;
    }
    
    deposit(amount) {
        this.#balance += amount;
        return this.#balance;
    }
    
    getBalance() {
        return this.#balance;
    }
}
```

### Modules and Imports

```javascript
// math.js
export const PI = 3.14159;

export function add(a, b) {
    return a + b;
}

export function multiply(a, b) {
    return a * b;
}

export default function calculator(operation, a, b) {
    switch (operation) {
        case 'add': return add(a, b);
        case 'multiply': return multiply(a, b);
        default: throw new Error('Unknown operation');
    }
}

// main.js
import calculator, { add, multiply, PI } from './math.js';
import * as MathUtils from './math.js';

console.log(add(2, 3)); // 5
console.log(calculator('multiply', 4, 5)); // 20
console.log(MathUtils.PI); // 3.14159

// Dynamic imports
async function loadModule() {
    const { default: Calculator } = await import('./math.js');
    return Calculator;
}
```

### Promises and Async/Await

```javascript
// Promise creation
function fetchData(url) {
    return new Promise((resolve, reject) => {
        setTimeout(() => {
            if (url.includes('error')) {
                reject(new Error('Failed to fetch data'));
            } else {
                resolve({ data: 'Some data', url });
            }
        }, 1000);
    });
}

// Using Promises
fetchData('https://api.example.com/users')
    .then(response => {
        console.log('Data:', response.data);
        return fetchData('https://api.example.com/posts');
    })
    .then(response => {
        console.log('Posts:', response.data);
    })
    .catch(error => {
        console.error('Error:', error.message);
    });

// Async/Await
async function fetchAllData() {
    try {
        const users = await fetchData('https://api.example.com/users');
        const posts = await fetchData('https://api.example.com/posts');
        
        return { users, posts };
    } catch (error) {
        console.error('Error:', error.message);
        throw error;
    }
}

// Parallel async operations
async function fetchMultipleData() {
    try {
        const [users, posts, comments] = await Promise.all([
            fetchData('https://api.example.com/users'),
            fetchData('https://api.example.com/posts'),
            fetchData('https://api.example.com/comments')
        ]);
        
        return { users, posts, comments };
    } catch (error) {
        console.error('Error:', error.message);
        return null;
    }
}
```

## Build Tools and Bundlers

### Webpack

```bash
npm install --save-dev webpack webpack-cli webpack-dev-server
```

```javascript
// webpack.config.js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
    entry: './src/index.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'bundle.js',
        clean: true
    },
    module: {
        rules: [
            {
                test: /\.js$/,
                exclude: /node_modules/,
                use: {
                    loader: 'babel-loader',
                    options: {
                        presets: ['@babel/preset-env']
                    }
                }
            },
            {
                test: /\.css$/,
                use: ['style-loader', 'css-loader']
            },
            {
                test: /\.(png|svg|jpg|jpeg|gif)$/i,
                type: 'asset/resource'
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: './src/index.html'
        })
    ],
    devServer: {
        static: './dist',
        hot: true,
        port: 3000
    },
    mode: 'development'
};
```

### Vite

```bash
npm create vite@latest my-app -- --template vanilla
cd my-app
npm install
```

```javascript
// vite.config.js
import { defineConfig } from 'vite';

export default defineConfig({
    server: {
        port: 3000,
        open: true
    },
    build: {
        outDir: 'dist',
        sourcemap: true
    },
    plugins: [
        // Add plugins here
    ]
});
```

### Package.json Scripts

```json
{
    "scripts": {
        "dev": "webpack serve --mode development",
        "build": "webpack --mode production",
        "build:analyze": "webpack --mode production --analyze",
        "test": "jest",
        "test:watch": "jest --watch",
        "lint": "eslint src/",
        "lint:fix": "eslint src/ --fix",
        "format": "prettier --write src/"
    }
}
```

## Testing Frameworks

### Jest

```bash
npm install --save-dev jest
```

```javascript
// math.test.js
const { add, multiply, divide } = require('./math');

describe('Math functions', () => {
    test('adds two numbers correctly', () => {
        expect(add(2, 3)).toBe(5);
        expect(add(-1, 1)).toBe(0);
        expect(add(0, 0)).toBe(0);
    });
    
    test('multiplies two numbers correctly', () => {
        expect(multiply(2, 3)).toBe(6);
        expect(multiply(-2, 3)).toBe(-6);
        expect(multiply(0, 5)).toBe(0);
    });
    
    test('divides two numbers correctly', () => {
        expect(divide(6, 2)).toBe(3);
        expect(divide(5, 2)).toBe(2.5);
    });
    
    test('throws error when dividing by zero', () => {
        expect(() => divide(5, 0)).toThrow('Division by zero');
    });
});

// Async testing
describe('Async functions', () => {
    test('fetches user data', async () => {
        const user = await fetchUser(1);
        expect(user).toHaveProperty('id');
        expect(user).toHaveProperty('name');
    });
    
    test('handles fetch errors', async () => {
        await expect(fetchUser(999)).rejects.toThrow('User not found');
    });
});

// Mock testing
jest.mock('./api');

describe('User service', () => {
    test('creates user successfully', async () => {
        const mockUser = { id: 1, name: 'John' };
        api.createUser.mockResolvedValue(mockUser);
        
        const result = await createUser('John');
        expect(result).toEqual(mockUser);
        expect(api.createUser).toHaveBeenCalledWith('John');
    });
});
```

### Mocha and Chai

```bash
npm install --save-dev mocha chai
```

```javascript
// test/user.test.js
const { expect } = require('chai');
const { User } = require('../src/user');

describe('User', () => {
    let user;
    
    beforeEach(() => {
        user = new User('John', 'john@example.com');
    });
    
    describe('constructor', () => {
        it('should create a user with name and email', () => {
            expect(user.name).to.equal('John');
            expect(user.email).to.equal('john@example.com');
        });
    });
    
    describe('greet', () => {
        it('should return greeting message', () => {
            const greeting = user.greet();
            expect(greeting).to.equal('Hello, I am John');
        });
    });
    
    describe('validate', () => {
        it('should return true for valid user', () => {
            expect(user.validate()).to.be.true;
        });
        
        it('should return false for invalid email', () => {
            user.email = 'invalid-email';
            expect(user.validate()).to.be.false;
        });
    });
});
```

## TypeScript Introduction

### Basic TypeScript

```bash
npm install --save-dev typescript @types/node
npx tsc --init
```

```typescript
// types.ts
interface User {
    id: number;
    name: string;
    email: string;
    age?: number; // Optional property
}

interface CreateUserRequest {
    name: string;
    email: string;
    age?: number;
}

type UserRole = 'admin' | 'user' | 'guest';

class UserService {
    private users: User[] = [];
    
    createUser(userData: CreateUserRequest): User {
        const newUser: User = {
            id: this.users.length + 1,
            name: userData.name,
            email: userData.email,
            age: userData.age
        };
        
        this.users.push(newUser);
        return newUser;
    }
    
    getUserById(id: number): User | undefined {
        return this.users.find(user => user.id === id);
    }
    
    getUsersByRole(role: UserRole): User[] {
        // Implementation here
        return this.users;
    }
}

// Generic types
interface ApiResponse<T> {
    data: T;
    status: number;
    message: string;
}

function createApiResponse<T>(data: T, status: number = 200): ApiResponse<T> {
    return {
        data,
        status,
        message: 'Success'
    };
}

// Usage
const userResponse: ApiResponse<User> = createApiResponse({
    id: 1,
    name: 'John',
    email: 'john@example.com'
});
```

### TypeScript with Express

```typescript
// server.ts
import express, { Request, Response, NextFunction } from 'express';
import { UserService } from './services/UserService';

const app = express();
const userService = new UserService();

app.use(express.json());

// Middleware with types
interface AuthenticatedRequest extends Request {
    user?: {
        id: number;
        name: string;
        email: string;
    };
}

function authenticate(req: AuthenticatedRequest, res: Response, next: NextFunction) {
    const token = req.headers.authorization;
    
    if (!token) {
        return res.status(401).json({ error: 'No token provided' });
    }
    
    // Mock authentication
    req.user = {
        id: 1,
        name: 'John Doe',
        email: 'john@example.com'
    };
    
    next();
}

// Routes with types
app.get('/api/users', (req: Request, res: Response) => {
    const users = userService.getAllUsers();
    res.json(users);
});

app.post('/api/users', (req: Request<{}, {}, CreateUserRequest>, res: Response) => {
    try {
        const newUser = userService.createUser(req.body);
        res.status(201).json(newUser);
    } catch (error) {
        res.status(400).json({ error: 'Invalid user data' });
    }
});

app.get('/api/users/:id', (req: Request<{ id: string }>, res: Response) => {
    const userId = parseInt(req.params.id);
    const user = userService.getUserById(userId);
    
    if (!user) {
        return res.status(404).json({ error: 'User not found' });
    }
    
    res.json(user);
});

app.listen(3000, () => {
    console.log('Server running on http://localhost:3000');
});
```

## Frontend Frameworks Overview

### React Basics

```jsx
// App.jsx
import React, { useState, useEffect } from 'react';

function App() {
    const [users, setUsers] = useState([]);
    const [loading, setLoading] = useState(true);
    
    useEffect(() => {
        fetchUsers();
    }, []);
    
    const fetchUsers = async () => {
        try {
            const response = await fetch('/api/users');
            const data = await response.json();
            setUsers(data);
        } catch (error) {
            console.error('Error fetching users:', error);
        } finally {
            setLoading(false);
        }
    };
    
    if (loading) {
        return <div>Loading...</div>;
    }
    
    return (
        <div>
            <h1>Users</h1>
            <ul>
                {users.map(user => (
                    <li key={user.id}>
                        {user.name} - {user.email}
                    </li>
                ))}
            </ul>
        </div>
    );
}

export default App;
```

### Vue.js Basics

```vue
<!-- App.vue -->
<template>
    <div>
        <h1>Users</h1>
        <div v-if="loading">Loading...</div>
        <ul v-else>
            <li v-for="user in users" :key="user.id">
                {{ user.name }} - {{ user.email }}
            </li>
        </ul>
    </div>
</template>

<script>
export default {
    data() {
        return {
            users: [],
            loading: true
        };
    },
    async mounted() {
        await this.fetchUsers();
    },
    methods: {
        async fetchUsers() {
            try {
                const response = await fetch('/api/users');
                this.users = await response.json();
            } catch (error) {
                console.error('Error fetching users:', error);
            } finally {
                this.loading = false;
            }
        }
    }
};
</script>
```

### Angular Basics

```typescript
// app.component.ts
import { Component, OnInit } from '@angular/core';
import { HttpClient } from '@angular/common/http';

interface User {
    id: number;
    name: string;
    email: string;
}

@Component({
    selector: 'app-root',
    template: `
        <div>
            <h1>Users</h1>
            <div *ngIf="loading">Loading...</div>
            <ul *ngIf="!loading">
                <li *ngFor="let user of users">
                    {{ user.name }} - {{ user.email }}
                </li>
            </ul>
        </div>
    `
})
export class AppComponent implements OnInit {
    users: User[] = [];
    loading = true;
    
    constructor(private http: HttpClient) {}
    
    ngOnInit() {
        this.fetchUsers();
    }
    
    fetchUsers() {
        this.http.get<User[]>('/api/users').subscribe({
            next: (users) => {
                this.users = users;
                this.loading = false;
            },
            error: (error) => {
                console.error('Error fetching users:', error);
                this.loading = false;
            }
        });
    }
}
```

## Full-Stack Development Patterns

### Monorepo Structure

```
my-app/
├── packages/
│   ├── frontend/          # React/Vue/Angular app
│   ├── backend/           # Node.js API
│   ├── shared/            # Shared types and utilities
│   └── mobile/            # React Native app
├── package.json
├── lerna.json
└── README.md
```

### API-First Development

```yaml
# api.yaml - OpenAPI specification
openapi: 3.0.0
info:
  title: User API
  version: 1.0.0
paths:
  /users:
    get:
      summary: Get all users
      responses:
        '200':
          description: List of users
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/User'
    post:
      summary: Create a new user
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateUserRequest'
      responses:
        '201':
          description: User created
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/User'
components:
  schemas:
    User:
      type: object
      properties:
        id:
          type: integer
        name:
          type: string
        email:
          type: string
          format: email
    CreateUserRequest:
      type: object
      required:
        - name
        - email
      properties:
        name:
          type: string
        email:
          type: string
          format: email
```

### Microservices Architecture

```javascript
// user-service.js
const express = require('express');
const app = express();

app.use(express.json());

// User service endpoints
app.get('/users', (req, res) => {
    // Return users
});

app.post('/users', (req, res) => {
    // Create user
});

// Health check
app.get('/health', (req, res) => {
    res.json({ status: 'OK', service: 'user-service' });
});

app.listen(3001, () => {
    console.log('User service running on port 3001');
});

// order-service.js
const express = require('express');
const app = express();

app.use(express.json());

// Order service endpoints
app.get('/orders', (req, res) => {
    // Return orders
});

app.post('/orders', (req, res) => {
    // Create order
});

app.listen(3002, () => {
    console.log('Order service running on port 3002');
});
```

## Exercises

### Exercise 1: Modern JavaScript Features
Create a project that demonstrates:
1. ES6+ features (destructuring, template literals, arrow functions)
2. Modules and imports
3. Async/await patterns
4. Classes and inheritance

### Exercise 2: Build Tool Setup
Set up a project with:
1. Webpack or Vite configuration
2. Babel for transpilation
3. ESLint and Prettier for code quality
4. Hot reloading in development

### Exercise 3: Testing Suite
Create a comprehensive testing suite:
1. Unit tests with Jest
2. Integration tests
3. Mock external dependencies
4. Test coverage reporting

### Exercise 4: TypeScript Migration
Convert a JavaScript project to TypeScript:
1. Add type definitions
2. Configure TypeScript compiler
3. Add strict type checking
4. Use generics and interfaces

## Next Steps

In the final module, we'll work on practical exercises:
- Progressive exercises building on previous modules
- Real-world projects
- Best practices and patterns
- Deployment and production considerations

---

**Practice Tip:** Experiment with different build tools and frameworks to understand their strengths and use cases!
