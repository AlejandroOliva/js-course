# Module 5: Node.js Web Development

## HTTP Module and Creating Servers

### Basic HTTP Server

```javascript
const http = require('http');

// Create a basic HTTP server
const server = http.createServer((req, res) => {
    // Set response headers
    res.writeHead(200, {
        'Content-Type': 'text/html',
        'Access-Control-Allow-Origin': '*'
    });
    
    // Handle different routes
    if (req.url === '/') {
        res.end('<h1>Welcome to our server!</h1>');
    } else if (req.url === '/about') {
        res.end('<h1>About Us</h1><p>This is our about page</p>');
    } else if (req.url === '/api/users') {
        res.writeHead(200, { 'Content-Type': 'application/json' });
        res.end(JSON.stringify([
            { id: 1, name: 'John Doe', email: 'john@example.com' },
            { id: 2, name: 'Jane Smith', email: 'jane@example.com' }
        ]));
    } else {
        res.writeHead(404, { 'Content-Type': 'text/html' });
        res.end('<h1>404 - Page Not Found</h1>');
    }
});

// Start the server
const PORT = process.env.PORT || 3000;
server.listen(PORT, () => {
    console.log(`Server running on http://localhost:${PORT}`);
});
```

### Handling Different HTTP Methods

```javascript
const http = require('http');
const url = require('url');

const server = http.createServer((req, res) => {
    const parsedUrl = url.parse(req.url, true);
    const path = parsedUrl.pathname;
    const method = req.method;
    
    // Set CORS headers
    res.setHeader('Access-Control-Allow-Origin', '*');
    res.setHeader('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE');
    res.setHeader('Access-Control-Allow-Headers', 'Content-Type');
    
    // Handle preflight requests
    if (method === 'OPTIONS') {
        res.writeHead(200);
        res.end();
        return;
    }
    
    // Route handling
    if (path === '/api/users') {
        switch (method) {
            case 'GET':
                handleGetUsers(req, res);
                break;
            case 'POST':
                handleCreateUser(req, res);
                break;
            case 'PUT':
                handleUpdateUser(req, res);
                break;
            case 'DELETE':
                handleDeleteUser(req, res);
                break;
            default:
                res.writeHead(405);
                res.end('Method Not Allowed');
        }
    } else {
        res.writeHead(404);
        res.end('Not Found');
    }
});

// Mock data
let users = [
    { id: 1, name: 'John Doe', email: 'john@example.com' },
    { id: 2, name: 'Jane Smith', email: 'jane@example.com' }
];

function handleGetUsers(req, res) {
    res.writeHead(200, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify(users));
}

function handleCreateUser(req, res) {
    let body = '';
    
    req.on('data', chunk => {
        body += chunk.toString();
    });
    
    req.on('end', () => {
        try {
            const newUser = JSON.parse(body);
            newUser.id = users.length + 1;
            users.push(newUser);
            
            res.writeHead(201, { 'Content-Type': 'application/json' });
            res.end(JSON.stringify(newUser));
        } catch (error) {
            res.writeHead(400);
            res.end('Invalid JSON');
        }
    });
}

function handleUpdateUser(req, res) {
    const parsedUrl = url.parse(req.url, true);
    const userId = parseInt(parsedUrl.query.id);
    
    let body = '';
    req.on('data', chunk => {
        body += chunk.toString();
    });
    
    req.on('end', () => {
        try {
            const updatedUser = JSON.parse(body);
            const userIndex = users.findIndex(user => user.id === userId);
            
            if (userIndex === -1) {
                res.writeHead(404);
                res.end('User not found');
                return;
            }
            
            users[userIndex] = { ...users[userIndex], ...updatedUser };
            
            res.writeHead(200, { 'Content-Type': 'application/json' });
            res.end(JSON.stringify(users[userIndex]));
        } catch (error) {
            res.writeHead(400);
            res.end('Invalid JSON');
        }
    });
}

function handleDeleteUser(req, res) {
    const parsedUrl = url.parse(req.url, true);
    const userId = parseInt(parsedUrl.query.id);
    
    const userIndex = users.findIndex(user => user.id === userId);
    
    if (userIndex === -1) {
        res.writeHead(404);
        res.end('User not found');
        return;
    }
    
    users.splice(userIndex, 1);
    res.writeHead(204);
    res.end();
}

server.listen(3000, () => {
    console.log('Server running on http://localhost:3000');
});
```

## Express.js Framework

### Setting Up Express

```bash
npm install express
```

```javascript
const express = require('express');
const app = express();
const PORT = process.env.PORT || 3000;

// Middleware
app.use(express.json()); // Parse JSON bodies
app.use(express.urlencoded({ extended: true })); // Parse URL-encoded bodies

// Basic route
app.get('/', (req, res) => {
    res.send('<h1>Welcome to Express!</h1>');
});

// JSON response
app.get('/api/status', (req, res) => {
    res.json({
        status: 'OK',
        timestamp: new Date().toISOString(),
        uptime: process.uptime()
    });
});

// Route parameters
app.get('/api/users/:id', (req, res) => {
    const userId = req.params.id;
    res.json({ message: `User ID: ${userId}` });
});

// Query parameters
app.get('/api/search', (req, res) => {
    const { q, limit = 10, offset = 0 } = req.query;
    res.json({
        query: q,
        limit: parseInt(limit),
        offset: parseInt(offset),
        results: []
    });
});

// POST request
app.post('/api/users', (req, res) => {
    const { name, email } = req.body;
    
    if (!name || !email) {
        return res.status(400).json({
            error: 'Name and email are required'
        });
    }
    
    const newUser = {
        id: Date.now(),
        name,
        email,
        createdAt: new Date().toISOString()
    };
    
    res.status(201).json(newUser);
});

// Error handling middleware
app.use((err, req, res, next) => {
    console.error(err.stack);
    res.status(500).json({
        error: 'Something went wrong!'
    });
});

// 404 handler
app.use((req, res) => {
    res.status(404).json({
        error: 'Route not found'
    });
});

app.listen(PORT, () => {
    console.log(`Server running on http://localhost:${PORT}`);
});
```

### Middleware and Routing

```javascript
const express = require('express');
const app = express();

// Custom middleware
function logger(req, res, next) {
    console.log(`${new Date().toISOString()} - ${req.method} ${req.path}`);
    next();
}

function authenticate(req, res, next) {
    const token = req.headers.authorization;
    
    if (!token) {
        return res.status(401).json({ error: 'No token provided' });
    }
    
    // Simple token validation (in real app, use JWT)
    if (token !== 'Bearer valid-token') {
        return res.status(401).json({ error: 'Invalid token' });
    }
    
    req.user = { id: 1, name: 'John Doe' };
    next();
}

// Apply middleware globally
app.use(logger);
app.use(express.json());

// Route-specific middleware
app.get('/api/protected', authenticate, (req, res) => {
    res.json({
        message: 'This is a protected route',
        user: req.user
    });
});

// Router for organizing routes
const userRouter = express.Router();

userRouter.get('/', (req, res) => {
    res.json({ message: 'Get all users' });
});

userRouter.get('/:id', (req, res) => {
    res.json({ message: `Get user ${req.params.id}` });
});

userRouter.post('/', (req, res) => {
    res.json({ message: 'Create user' });
});

userRouter.put('/:id', (req, res) => {
    res.json({ message: `Update user ${req.params.id}` });
});

userRouter.delete('/:id', (req, res) => {
    res.json({ message: `Delete user ${req.params.id}` });
});

// Mount router
app.use('/api/users', userRouter);

// Static file serving
app.use('/static', express.static('public'));

app.listen(3000, () => {
    console.log('Server running on http://localhost:3000');
});
```

### Advanced Express Features

```javascript
const express = require('express');
const app = express();

// Template engine (EJS)
app.set('view engine', 'ejs');
app.set('views', './views');

// Serve static files
app.use(express.static('public'));

// Body parsing middleware
app.use(express.json({ limit: '10mb' }));
app.use(express.urlencoded({ extended: true, limit: '10mb' }));

// CORS middleware
app.use((req, res, next) => {
    res.header('Access-Control-Allow-Origin', '*');
    res.header('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE, OPTIONS');
    res.header('Access-Control-Allow-Headers', 'Origin, X-Requested-With, Content-Type, Accept, Authorization');
    
    if (req.method === 'OPTIONS') {
        res.sendStatus(200);
    } else {
        next();
    }
});

// Rate limiting middleware
const rateLimit = require('express-rate-limit');
const limiter = rateLimit({
    windowMs: 15 * 60 * 1000, // 15 minutes
    max: 100 // limit each IP to 100 requests per windowMs
});
app.use('/api/', limiter);

// Request logging
app.use((req, res, next) => {
    console.log(`${req.method} ${req.path} - ${req.ip}`);
    next();
});

// Routes
app.get('/', (req, res) => {
    res.render('index', { title: 'Home', message: 'Welcome!' });
});

app.get('/api/data', (req, res) => {
    res.json({
        data: 'Some data',
        timestamp: new Date().toISOString()
    });
});

// Error handling
app.use((err, req, res, next) => {
    console.error(err.stack);
    
    if (err.type === 'entity.parse.failed') {
        return res.status(400).json({ error: 'Invalid JSON' });
    }
    
    res.status(500).json({ error: 'Internal server error' });
});

// 404 handler
app.use((req, res) => {
    res.status(404).json({ error: 'Not found' });
});

app.listen(3000, () => {
    console.log('Server running on http://localhost:3000');
});
```

## Working with Databases

### MongoDB with Mongoose

```bash
npm install mongoose
```

```javascript
const mongoose = require('mongoose');

// Connect to MongoDB
mongoose.connect('mongodb://localhost:27017/myapp', {
    useNewUrlParser: true,
    useUnifiedTopology: true
});

// Define schema
const userSchema = new mongoose.Schema({
    name: {
        type: String,
        required: true,
        trim: true
    },
    email: {
        type: String,
        required: true,
        unique: true,
        lowercase: true
    },
    age: {
        type: Number,
        min: 0,
        max: 120
    },
    createdAt: {
        type: Date,
        default: Date.now
    }
});

// Create model
const User = mongoose.model('User', userSchema);

// Express routes with MongoDB
const express = require('express');
const app = express();

app.use(express.json());

// Create user
app.post('/api/users', async (req, res) => {
    try {
        const user = new User(req.body);
        await user.save();
        res.status(201).json(user);
    } catch (error) {
        res.status(400).json({ error: error.message });
    }
});

// Get all users
app.get('/api/users', async (req, res) => {
    try {
        const users = await User.find();
        res.json(users);
    } catch (error) {
        res.status(500).json({ error: error.message });
    }
});

// Get user by ID
app.get('/api/users/:id', async (req, res) => {
    try {
        const user = await User.findById(req.params.id);
        if (!user) {
            return res.status(404).json({ error: 'User not found' });
        }
        res.json(user);
    } catch (error) {
        res.status(500).json({ error: error.message });
    }
});

// Update user
app.put('/api/users/:id', async (req, res) => {
    try {
        const user = await User.findByIdAndUpdate(
            req.params.id,
            req.body,
            { new: true, runValidators: true }
        );
        if (!user) {
            return res.status(404).json({ error: 'User not found' });
        }
        res.json(user);
    } catch (error) {
        res.status(400).json({ error: error.message });
    }
});

// Delete user
app.delete('/api/users/:id', async (req, res) => {
    try {
        const user = await User.findByIdAndDelete(req.params.id);
        if (!user) {
            return res.status(404).json({ error: 'User not found' });
        }
        res.status(204).send();
    } catch (error) {
        res.status(500).json({ error: error.message });
    }
});

app.listen(3000, () => {
    console.log('Server running on http://localhost:3000');
});
```

### PostgreSQL with pg

```bash
npm install pg
```

```javascript
const { Pool } = require('pg');

// Database connection
const pool = new Pool({
    user: 'postgres',
    host: 'localhost',
    database: 'myapp',
    password: 'password',
    port: 5432,
});

// Express routes with PostgreSQL
const express = require('express');
const app = express();

app.use(express.json());

// Create user
app.post('/api/users', async (req, res) => {
    try {
        const { name, email, age } = req.body;
        const result = await pool.query(
            'INSERT INTO users (name, email, age) VALUES ($1, $2, $3) RETURNING *',
            [name, email, age]
        );
        res.status(201).json(result.rows[0]);
    } catch (error) {
        res.status(400).json({ error: error.message });
    }
});

// Get all users
app.get('/api/users', async (req, res) => {
    try {
        const result = await pool.query('SELECT * FROM users ORDER BY created_at DESC');
        res.json(result.rows);
    } catch (error) {
        res.status(500).json({ error: error.message });
    }
});

// Get user by ID
app.get('/api/users/:id', async (req, res) => {
    try {
        const result = await pool.query('SELECT * FROM users WHERE id = $1', [req.params.id]);
        if (result.rows.length === 0) {
            return res.status(404).json({ error: 'User not found' });
        }
        res.json(result.rows[0]);
    } catch (error) {
        res.status(500).json({ error: error.message });
    }
});

// Update user
app.put('/api/users/:id', async (req, res) => {
    try {
        const { name, email, age } = req.body;
        const result = await pool.query(
            'UPDATE users SET name = $1, email = $2, age = $3 WHERE id = $4 RETURNING *',
            [name, email, age, req.params.id]
        );
        if (result.rows.length === 0) {
            return res.status(404).json({ error: 'User not found' });
        }
        res.json(result.rows[0]);
    } catch (error) {
        res.status(400).json({ error: error.message });
    }
});

// Delete user
app.delete('/api/users/:id', async (req, res) => {
    try {
        const result = await pool.query('DELETE FROM users WHERE id = $1 RETURNING *', [req.params.id]);
        if (result.rows.length === 0) {
            return res.status(404).json({ error: 'User not found' });
        }
        res.status(204).send();
    } catch (error) {
        res.status(500).json({ error: error.message });
    }
});

app.listen(3000, () => {
    console.log('Server running on http://localhost:3000');
});
```

## Authentication and Security

### JWT Authentication

```bash
npm install jsonwebtoken bcryptjs
```

```javascript
const jwt = require('jsonwebtoken');
const bcrypt = require('bcryptjs');
const express = require('express');
const app = express();

app.use(express.json());

const JWT_SECRET = process.env.JWT_SECRET || 'your-secret-key';

// Mock user database
const users = [
    {
        id: 1,
        username: 'admin',
        email: 'admin@example.com',
        password: '$2a$10$92IXUNpkjO0rOQ5byMi.Ye4oKoEa3Ro9llC/.og/at2.uheWG/igi' // password
    }
];

// Register user
app.post('/api/register', async (req, res) => {
    try {
        const { username, email, password } = req.body;
        
        // Check if user already exists
        const existingUser = users.find(user => user.email === email);
        if (existingUser) {
            return res.status(400).json({ error: 'User already exists' });
        }
        
        // Hash password
        const hashedPassword = await bcrypt.hash(password, 10);
        
        // Create user
        const newUser = {
            id: users.length + 1,
            username,
            email,
            password: hashedPassword
        };
        
        users.push(newUser);
        
        // Generate JWT token
        const token = jwt.sign(
            { userId: newUser.id, email: newUser.email },
            JWT_SECRET,
            { expiresIn: '24h' }
        );
        
        res.status(201).json({
            message: 'User created successfully',
            token,
            user: {
                id: newUser.id,
                username: newUser.username,
                email: newUser.email
            }
        });
    } catch (error) {
        res.status(500).json({ error: error.message });
    }
});

// Login user
app.post('/api/login', async (req, res) => {
    try {
        const { email, password } = req.body;
        
        // Find user
        const user = users.find(user => user.email === email);
        if (!user) {
            return res.status(401).json({ error: 'Invalid credentials' });
        }
        
        // Check password
        const isValidPassword = await bcrypt.compare(password, user.password);
        if (!isValidPassword) {
            return res.status(401).json({ error: 'Invalid credentials' });
        }
        
        // Generate JWT token
        const token = jwt.sign(
            { userId: user.id, email: user.email },
            JWT_SECRET,
            { expiresIn: '24h' }
        );
        
        res.json({
            message: 'Login successful',
            token,
            user: {
                id: user.id,
                username: user.username,
                email: user.email
            }
        });
    } catch (error) {
        res.status(500).json({ error: error.message });
    }
});

// Middleware to verify JWT token
function authenticateToken(req, res, next) {
    const authHeader = req.headers['authorization'];
    const token = authHeader && authHeader.split(' ')[1];
    
    if (!token) {
        return res.status(401).json({ error: 'Access token required' });
    }
    
    jwt.verify(token, JWT_SECRET, (err, user) => {
        if (err) {
            return res.status(403).json({ error: 'Invalid token' });
        }
        req.user = user;
        next();
    });
}

// Protected route
app.get('/api/profile', authenticateToken, (req, res) => {
    const user = users.find(user => user.id === req.user.userId);
    if (!user) {
        return res.status(404).json({ error: 'User not found' });
    }
    
    res.json({
        id: user.id,
        username: user.username,
        email: user.email
    });
});

app.listen(3000, () => {
    console.log('Server running on http://localhost:3000');
});
```

### Security Best Practices

```javascript
const express = require('express');
const helmet = require('helmet');
const rateLimit = require('express-rate-limit');
const cors = require('cors');

const app = express();

// Security middleware
app.use(helmet()); // Set security headers

// CORS configuration
app.use(cors({
    origin: process.env.ALLOWED_ORIGINS?.split(',') || ['http://localhost:3000'],
    credentials: true
}));

// Rate limiting
const limiter = rateLimit({
    windowMs: 15 * 60 * 1000, // 15 minutes
    max: 100, // limit each IP to 100 requests per windowMs
    message: 'Too many requests from this IP'
});
app.use('/api/', limiter);

// Input validation middleware
function validateUser(req, res, next) {
    const { name, email, age } = req.body;
    
    if (!name || typeof name !== 'string' || name.trim().length < 2) {
        return res.status(400).json({ error: 'Name must be at least 2 characters' });
    }
    
    if (!email || !email.includes('@')) {
        return res.status(400).json({ error: 'Valid email is required' });
    }
    
    if (age && (typeof age !== 'number' || age < 0 || age > 120)) {
        return res.status(400).json({ error: 'Age must be between 0 and 120' });
    }
    
    next();
}

// Sanitize input
function sanitizeInput(req, res, next) {
    if (req.body) {
        Object.keys(req.body).forEach(key => {
            if (typeof req.body[key] === 'string') {
                req.body[key] = req.body[key].trim();
            }
        });
    }
    next();
}

app.use(express.json({ limit: '10mb' }));
app.use(sanitizeInput);

// Routes with validation
app.post('/api/users', validateUser, (req, res) => {
    // Process validated data
    res.json({ message: 'User created successfully' });
});

// Error handling
app.use((err, req, res, next) => {
    console.error(err.stack);
    
    // Don't leak error details in production
    const isDevelopment = process.env.NODE_ENV === 'development';
    
    res.status(500).json({
        error: isDevelopment ? err.message : 'Internal server error',
        ...(isDevelopment && { stack: err.stack })
    });
});

app.listen(3000, () => {
    console.log('Server running on http://localhost:3000');
});
```

## Exercises

### Exercise 1: RESTful API
Create a complete REST API for a blog system:
1. Users (CRUD operations)
2. Posts (CRUD operations with user association)
3. Comments (CRUD operations with post and user association)
4. Authentication and authorization

### Exercise 2: File Upload API
Create an API that handles file uploads:
1. Accept image files
2. Validate file types and sizes
3. Store files securely
4. Provide download endpoints

### Exercise 3: Real-time Chat
Create a real-time chat application:
1. WebSocket connections
2. Room-based messaging
3. User presence indicators
4. Message history

### Exercise 4: E-commerce API
Create an e-commerce API with:
1. Product catalog
2. Shopping cart functionality
3. Order processing
4. Payment integration (mock)

## Next Steps

In the next module, we'll explore the modern JavaScript ecosystem:
- ES6+ features and modern syntax
- Build tools and bundlers
- Testing frameworks
- TypeScript introduction
- Frontend frameworks overview
- Full-stack development patterns

---

**Practice Tip:** Build small APIs and test them with tools like Postman or curl to understand HTTP methods and status codes!
