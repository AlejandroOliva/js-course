# Module 7: Practical Exercises

## Progressive Exercises

### Exercise 1: Temperature Converter CLI Tool

**Objective:** Build a command-line tool that converts temperatures between Celsius, Fahrenheit, and Kelvin.

**Requirements:**
- Accept input from command line arguments
- Support multiple temperature scales
- Handle invalid inputs gracefully
- Provide help documentation

**Implementation:**

```javascript
// temperature-converter.js
#!/usr/bin/env node

const args = process.argv.slice(2);

function showHelp() {
    console.log(`
Temperature Converter CLI Tool

Usage: node temperature-converter.js <temperature> <from-scale> <to-scale>

Scales: C (Celsius), F (Fahrenheit), K (Kelvin)

Examples:
  node temperature-converter.js 32 F C    # Convert 32°F to Celsius
  node temperature-converter.js 0 C K    # Convert 0°C to Kelvin
  node temperature-converter.js 273.15 K F # Convert 273.15K to Fahrenheit

Options:
  --help, -h    Show this help message
`);
}

function convertTemperature(temp, fromScale, toScale) {
    const scales = ['C', 'F', 'K'];
    
    if (!scales.includes(fromScale) || !scales.includes(toScale)) {
        throw new Error('Invalid scale. Use C, F, or K');
    }
    
    if (fromScale === toScale) {
        return temp;
    }
    
    // Convert to Celsius first
    let celsius;
    switch (fromScale) {
        case 'C':
            celsius = temp;
            break;
        case 'F':
            celsius = (temp - 32) * 5/9;
            break;
        case 'K':
            celsius = temp - 273.15;
            break;
    }
    
    // Convert from Celsius to target scale
    switch (toScale) {
        case 'C':
            return celsius;
        case 'F':
            return celsius * 9/5 + 32;
        case 'K':
            return celsius + 273.15;
    }
}

function main() {
    if (args.length === 0 || args.includes('--help') || args.includes('-h')) {
        showHelp();
        return;
    }
    
    if (args.length !== 3) {
        console.error('Error: Please provide temperature, from-scale, and to-scale');
        console.log('Use --help for usage information');
        process.exit(1);
    }
    
    const [tempStr, fromScale, toScale] = args;
    const temp = parseFloat(tempStr);
    
    if (isNaN(temp)) {
        console.error('Error: Temperature must be a valid number');
        process.exit(1);
    }
    
    try {
        const result = convertTemperature(temp, fromScale.toUpperCase(), toScale.toUpperCase());
        console.log(`${temp}°${fromScale.toUpperCase()} = ${result.toFixed(2)}°${toScale.toUpperCase()}`);
    } catch (error) {
        console.error(`Error: ${error.message}`);
        process.exit(1);
    }
}

main();
```

**Testing:**
```bash
node temperature-converter.js 32 F C
node temperature-converter.js 0 C K
node temperature-converter.js --help
```

### Exercise 2: File Organizer

**Objective:** Create a tool that organizes files in a directory by their extensions.

**Requirements:**
- Scan a directory for files
- Create subdirectories based on file extensions
- Move files to appropriate directories
- Provide dry-run option
- Handle errors gracefully

**Implementation:**

```javascript
// file-organizer.js
#!/usr/bin/env node

const fs = require('fs');
const path = require('path');

function showHelp() {
    console.log(`
File Organizer CLI Tool

Usage: node file-organizer.js <directory> [options]

Options:
  --dry-run, -d    Show what would be done without actually moving files
  --backup, -b     Create backup before organizing
  --help, -h       Show this help message

Examples:
  node file-organizer.js ./downloads
  node file-organizer.js ./downloads --dry-run
  node file-organizer.js ./downloads --backup
`);
}

function getFileExtension(filename) {
    const ext = path.extname(filename).toLowerCase();
    return ext || 'no-extension';
}

function createDirectory(dirPath) {
    if (!fs.existsSync(dirPath)) {
        fs.mkdirSync(dirPath, { recursive: true });
        console.log(`Created directory: ${dirPath}`);
    }
}

function organizeFiles(directory, options = {}) {
    const { dryRun = false, backup = false } = options;
    
    if (!fs.existsSync(directory)) {
        throw new Error(`Directory ${directory} does not exist`);
    }
    
    const files = fs.readdirSync(directory);
    const fileStats = files.map(file => {
        const filePath = path.join(directory, file);
        const stats = fs.statSync(filePath);
        return { name: file, path: filePath, isFile: stats.isFile() };
    }).filter(item => item.isFile);
    
    const organizedFiles = {};
    
    // Group files by extension
    fileStats.forEach(file => {
        const ext = getFileExtension(file.name);
        if (!organizedFiles[ext]) {
            organizedFiles[ext] = [];
        }
        organizedFiles[ext].push(file);
    });
    
    console.log(`Found ${fileStats.length} files to organize:`);
    Object.keys(organizedFiles).forEach(ext => {
        console.log(`  ${ext}: ${organizedFiles[ext].length} files`);
    });
    
    if (dryRun) {
        console.log('\nDry run - no files will be moved:');
        Object.keys(organizedFiles).forEach(ext => {
            const extDir = path.join(directory, ext);
            console.log(`Would create directory: ${extDir}`);
            organizedFiles[ext].forEach(file => {
                const newPath = path.join(extDir, file.name);
                console.log(`Would move: ${file.path} -> ${newPath}`);
            });
        });
        return;
    }
    
    // Create backup if requested
    if (backup) {
        const backupDir = path.join(directory, 'backup-' + Date.now());
        console.log(`Creating backup in: ${backupDir}`);
        fs.mkdirSync(backupDir);
        fileStats.forEach(file => {
            fs.copyFileSync(file.path, path.join(backupDir, file.name));
        });
    }
    
    // Organize files
    Object.keys(organizedFiles).forEach(ext => {
        const extDir = path.join(directory, ext);
        createDirectory(extDir);
        
        organizedFiles[ext].forEach(file => {
            const newPath = path.join(extDir, file.name);
            fs.renameSync(file.path, newPath);
            console.log(`Moved: ${file.name} -> ${ext}/${file.name}`);
        });
    });
    
    console.log('\nFile organization completed!');
}

function main() {
    const args = process.argv.slice(2);
    
    if (args.length === 0 || args.includes('--help') || args.includes('-h')) {
        showHelp();
        return;
    }
    
    const directory = args[0];
    const options = {
        dryRun: args.includes('--dry-run') || args.includes('-d'),
        backup: args.includes('--backup') || args.includes('-b')
    };
    
    try {
        organizeFiles(directory, options);
    } catch (error) {
        console.error(`Error: ${error.message}`);
        process.exit(1);
    }
}

main();
```

### Exercise 3: REST API with Authentication

**Objective:** Build a complete REST API for a task management system with user authentication.

**Requirements:**
- User registration and login
- JWT-based authentication
- CRUD operations for tasks
- Input validation
- Error handling
- Database integration

**Implementation:**

```javascript
// server.js
const express = require('express');
const jwt = require('jsonwebtoken');
const bcrypt = require('bcryptjs');
const { body, validationResult } = require('express-validator');

const app = express();
app.use(express.json());

const JWT_SECRET = process.env.JWT_SECRET || 'your-secret-key';

// Mock database
let users = [];
let tasks = [];
let nextUserId = 1;
let nextTaskId = 1;

// Middleware
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

// Validation middleware
const validateUser = [
    body('username').isLength({ min: 3 }).withMessage('Username must be at least 3 characters'),
    body('email').isEmail().withMessage('Must be a valid email'),
    body('password').isLength({ min: 6 }).withMessage('Password must be at least 6 characters')
];

const validateTask = [
    body('title').notEmpty().withMessage('Title is required'),
    body('description').optional().isString(),
    body('priority').optional().isIn(['low', 'medium', 'high']).withMessage('Priority must be low, medium, or high')
];

// Routes

// User registration
app.post('/api/register', validateUser, async (req, res) => {
    try {
        const errors = validationResult(req);
        if (!errors.isEmpty()) {
            return res.status(400).json({ errors: errors.array() });
        }
        
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
            id: nextUserId++,
            username,
            email,
            password: hashedPassword,
            createdAt: new Date().toISOString()
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

// User login
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

// Get user profile
app.get('/api/profile', authenticateToken, (req, res) => {
    const user = users.find(user => user.id === req.user.userId);
    if (!user) {
        return res.status(404).json({ error: 'User not found' });
    }
    
    res.json({
        id: user.id,
        username: user.username,
        email: user.email,
        createdAt: user.createdAt
    });
});

// Task routes

// Get all tasks for authenticated user
app.get('/api/tasks', authenticateToken, (req, res) => {
    const userTasks = tasks.filter(task => task.userId === req.user.userId);
    res.json(userTasks);
});

// Create new task
app.post('/api/tasks', authenticateToken, validateTask, (req, res) => {
    try {
        const errors = validationResult(req);
        if (!errors.isEmpty()) {
            return res.status(400).json({ errors: errors.array() });
        }
        
        const { title, description, priority = 'medium' } = req.body;
        
        const newTask = {
            id: nextTaskId++,
            title,
            description,
            priority,
            completed: false,
            userId: req.user.userId,
            createdAt: new Date().toISOString(),
            updatedAt: new Date().toISOString()
        };
        
        tasks.push(newTask);
        res.status(201).json(newTask);
    } catch (error) {
        res.status(500).json({ error: error.message });
    }
});

// Get task by ID
app.get('/api/tasks/:id', authenticateToken, (req, res) => {
    const task = tasks.find(task => task.id === parseInt(req.params.id) && task.userId === req.user.userId);
    
    if (!task) {
        return res.status(404).json({ error: 'Task not found' });
    }
    
    res.json(task);
});

// Update task
app.put('/api/tasks/:id', authenticateToken, validateTask, (req, res) => {
    try {
        const errors = validationResult(req);
        if (!errors.isEmpty()) {
            return res.status(400).json({ errors: errors.array() });
        }
        
        const taskIndex = tasks.findIndex(task => task.id === parseInt(req.params.id) && task.userId === req.user.userId);
        
        if (taskIndex === -1) {
            return res.status(404).json({ error: 'Task not found' });
        }
        
        const { title, description, priority, completed } = req.body;
        
        tasks[taskIndex] = {
            ...tasks[taskIndex],
            title: title || tasks[taskIndex].title,
            description: description || tasks[taskIndex].description,
            priority: priority || tasks[taskIndex].priority,
            completed: completed !== undefined ? completed : tasks[taskIndex].completed,
            updatedAt: new Date().toISOString()
        };
        
        res.json(tasks[taskIndex]);
    } catch (error) {
        res.status(500).json({ error: error.message });
    }
});

// Delete task
app.delete('/api/tasks/:id', authenticateToken, (req, res) => {
    const taskIndex = tasks.findIndex(task => task.id === parseInt(req.params.id) && task.userId === req.user.userId);
    
    if (taskIndex === -1) {
        return res.status(404).json({ error: 'Task not found' });
    }
    
    tasks.splice(taskIndex, 1);
    res.status(204).send();
});

// Error handling middleware
app.use((err, req, res, next) => {
    console.error(err.stack);
    res.status(500).json({ error: 'Something went wrong!' });
});

// 404 handler
app.use((req, res) => {
    res.status(404).json({ error: 'Route not found' });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
    console.log(`Server running on http://localhost:${PORT}`);
});
```

### Exercise 4: Real-time Chat Application

**Objective:** Build a real-time chat application using WebSockets.

**Requirements:**
- WebSocket server
- Room-based messaging
- User presence indicators
- Message history
- Simple web client

**Implementation:**

```javascript
// chat-server.js
const express = require('express');
const http = require('http');
const socketIo = require('socket.io');
const path = require('path');

const app = express();
const server = http.createServer(app);
const io = socketIo(server);

// Serve static files
app.use(express.static(path.join(__dirname, 'public')));

// Store connected users and messages
const users = new Map();
const messages = new Map();

// Socket.io connection handling
io.on('connection', (socket) => {
    console.log('User connected:', socket.id);
    
    // User joins a room
    socket.on('join-room', (data) => {
        const { username, room } = data;
        
        // Store user information
        users.set(socket.id, { username, room, socketId: socket.id });
        
        // Join the room
        socket.join(room);
        
        // Notify others in the room
        socket.to(room).emit('user-joined', {
            username,
            message: `${username} joined the room`
        });
        
        // Send room info to the user
        const roomUsers = Array.from(users.values())
            .filter(user => user.room === room)
            .map(user => user.username);
        
        socket.emit('room-info', {
            room,
            users: roomUsers,
            messages: messages.get(room) || []
        });
        
        console.log(`${username} joined room ${room}`);
    });
    
    // Handle new messages
    socket.on('send-message', (data) => {
        const user = users.get(socket.id);
        if (!user) return;
        
        const message = {
            id: Date.now(),
            username: user.username,
            text: data.text,
            timestamp: new Date().toISOString(),
            room: user.room
        };
        
        // Store message
        if (!messages.has(user.room)) {
            messages.set(user.room, []);
        }
        messages.get(user.room).push(message);
        
        // Send message to all users in the room
        io.to(user.room).emit('new-message', message);
        
        console.log(`Message from ${user.username} in ${user.room}: ${data.text}`);
    });
    
    // Handle typing indicators
    socket.on('typing', (data) => {
        const user = users.get(socket.id);
        if (!user) return;
        
        socket.to(user.room).emit('user-typing', {
            username: user.username,
            isTyping: data.isTyping
        });
    });
    
    // Handle disconnection
    socket.on('disconnect', () => {
        const user = users.get(socket.id);
        if (user) {
            // Notify others in the room
            socket.to(user.room).emit('user-left', {
                username: user.username,
                message: `${user.username} left the room`
            });
            
            users.delete(socket.id);
            console.log(`${user.username} disconnected from room ${user.room}`);
        }
    });
});

const PORT = process.env.PORT || 3000;
server.listen(PORT, () => {
    console.log(`Chat server running on http://localhost:${PORT}`);
});
```

```html
<!-- public/index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Real-time Chat</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
        }
        .chat-container {
            border: 1px solid #ccc;
            height: 400px;
            overflow-y: auto;
            padding: 10px;
            margin-bottom: 10px;
        }
        .message {
            margin-bottom: 10px;
            padding: 5px;
            border-radius: 5px;
        }
        .message.own {
            background-color: #e3f2fd;
            text-align: right;
        }
        .message.other {
            background-color: #f5f5f5;
        }
        .input-container {
            display: flex;
            gap: 10px;
        }
        input[type="text"] {
            flex: 1;
            padding: 10px;
            border: 1px solid #ccc;
            border-radius: 5px;
        }
        button {
            padding: 10px 20px;
            background-color: #007bff;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        button:hover {
            background-color: #0056b3;
        }
        .join-form {
            margin-bottom: 20px;
        }
        .join-form input {
            margin-right: 10px;
            padding: 8px;
        }
        .typing-indicator {
            font-style: italic;
            color: #666;
        }
    </style>
</head>
<body>
    <h1>Real-time Chat</h1>
    
    <div id="join-form" class="join-form">
        <input type="text" id="username" placeholder="Enter your username" required>
        <input type="text" id="room" placeholder="Enter room name" required>
        <button onclick="joinRoom()">Join Room</button>
    </div>
    
    <div id="chat-container" class="chat-container" style="display: none;">
        <div id="messages"></div>
        <div id="typing-indicator" class="typing-indicator"></div>
    </div>
    
    <div id="input-container" class="input-container" style="display: none;">
        <input type="text" id="message-input" placeholder="Type your message..." onkeypress="handleKeyPress(event)">
        <button onclick="sendMessage()">Send</button>
    </div>
    
    <script src="/socket.io/socket.io.js"></script>
    <script>
        const socket = io();
        let currentUsername = '';
        let currentRoom = '';
        
        function joinRoom() {
            const username = document.getElementById('username').value;
            const room = document.getElementById('room').value;
            
            if (!username || !room) {
                alert('Please enter both username and room name');
                return;
            }
            
            currentUsername = username;
            currentRoom = room;
            
            socket.emit('join-room', { username, room });
            
            document.getElementById('join-form').style.display = 'none';
            document.getElementById('chat-container').style.display = 'block';
            document.getElementById('input-container').style.display = 'flex';
        }
        
        function sendMessage() {
            const messageInput = document.getElementById('message-input');
            const text = messageInput.value.trim();
            
            if (text) {
                socket.emit('send-message', { text });
                messageInput.value = '';
            }
        }
        
        function handleKeyPress(event) {
            if (event.key === 'Enter') {
                sendMessage();
            } else {
                socket.emit('typing', { isTyping: true });
                clearTimeout(window.typingTimeout);
                window.typingTimeout = setTimeout(() => {
                    socket.emit('typing', { isTyping: false });
                }, 1000);
            }
        }
        
        // Socket event listeners
        socket.on('room-info', (data) => {
            const messagesContainer = document.getElementById('messages');
            messagesContainer.innerHTML = '';
            
            data.messages.forEach(message => {
                addMessage(message);
            });
        });
        
        socket.on('new-message', (message) => {
            addMessage(message);
        });
        
        socket.on('user-joined', (data) => {
            addSystemMessage(data.message);
        });
        
        socket.on('user-left', (data) => {
            addSystemMessage(data.message);
        });
        
        socket.on('user-typing', (data) => {
            const indicator = document.getElementById('typing-indicator');
            if (data.isTyping) {
                indicator.textContent = `${data.username} is typing...`;
            } else {
                indicator.textContent = '';
            }
        });
        
        function addMessage(message) {
            const messagesContainer = document.getElementById('messages');
            const messageDiv = document.createElement('div');
            messageDiv.className = `message ${message.username === currentUsername ? 'own' : 'other'}`;
            
            const timestamp = new Date(message.timestamp).toLocaleTimeString();
            messageDiv.innerHTML = `
                <strong>${message.username}</strong> <small>(${timestamp})</small><br>
                ${message.text}
            `;
            
            messagesContainer.appendChild(messageDiv);
            messagesContainer.scrollTop = messagesContainer.scrollHeight;
        }
        
        function addSystemMessage(message) {
            const messagesContainer = document.getElementById('messages');
            const messageDiv = document.createElement('div');
            messageDiv.className = 'message system';
            messageDiv.style.textAlign = 'center';
            messageDiv.style.fontStyle = 'italic';
            messageDiv.style.color = '#666';
            messageDiv.textContent = message;
            
            messagesContainer.appendChild(messageDiv);
            messagesContainer.scrollTop = messagesContainer.scrollHeight;
        }
    </script>
</body>
</html>
```

## Real-World Projects

### Project 1: E-commerce API

**Objective:** Build a complete e-commerce API with products, orders, and payments.

**Features:**
- Product catalog with categories
- Shopping cart functionality
- Order processing
- User authentication
- Payment integration (mock)
- Admin panel

### Project 2: Social Media API

**Objective:** Create a social media API with posts, comments, and user interactions.

**Features:**
- User profiles and authentication
- Post creation and management
- Comments and likes system
- Follow/unfollow functionality
- Real-time notifications
- Image upload

### Project 3: Task Management System

**Objective:** Build a comprehensive task management system with teams and projects.

**Features:**
- User and team management
- Project and task organization
- File attachments
- Time tracking
- Reporting and analytics
- Real-time updates

## Best Practices and Patterns

### Code Organization

```
project/
├── src/
│   ├── controllers/     # Route handlers
│   ├── models/          # Data models
│   ├── services/        # Business logic
│   ├── middleware/      # Custom middleware
│   ├── utils/           # Utility functions
│   ├── config/          # Configuration files
│   └── routes/          # Route definitions
├── tests/               # Test files
├── docs/                # Documentation
├── public/              # Static files
└── package.json
```

### Error Handling

```javascript
// Centralized error handling
class AppError extends Error {
    constructor(message, statusCode) {
        super(message);
        this.statusCode = statusCode;
        this.isOperational = true;
        
        Error.captureStackTrace(this, this.constructor);
    }
}

// Error handling middleware
const errorHandler = (err, req, res, next) => {
    let error = { ...err };
    error.message = err.message;
    
    // Log error
    console.error(err);
    
    // Mongoose bad ObjectId
    if (err.name === 'CastError') {
        const message = 'Resource not found';
        error = new AppError(message, 404);
    }
    
    // Mongoose duplicate key
    if (err.code === 11000) {
        const message = 'Duplicate field value entered';
        error = new AppError(message, 400);
    }
    
    // Mongoose validation error
    if (err.name === 'ValidationError') {
        const message = Object.values(err.errors).map(val => val.message);
        error = new AppError(message, 400);
    }
    
    res.status(error.statusCode || 500).json({
        success: false,
        error: error.message || 'Server Error'
    });
};
```

### Security Best Practices

```javascript
// Security middleware
const securityMiddleware = (req, res, next) => {
    // Rate limiting
    const limiter = rateLimit({
        windowMs: 15 * 60 * 1000, // 15 minutes
        max: 100 // limit each IP to 100 requests per windowMs
    });
    
    // Helmet for security headers
    app.use(helmet());
    
    // CORS configuration
    app.use(cors({
        origin: process.env.ALLOWED_ORIGINS?.split(',') || ['http://localhost:3000'],
        credentials: true
    }));
    
    // Input validation
    app.use(express.json({ limit: '10mb' }));
    app.use(express.urlencoded({ extended: true, limit: '10mb' }));
    
    next();
};
```

### Testing Patterns

```javascript
// Test setup
const request = require('supertest');
const app = require('../app');

describe('User API', () => {
    let authToken;
    
    beforeAll(async () => {
        // Setup test database
        await setupTestDB();
    });
    
    afterAll(async () => {
        // Cleanup test database
        await cleanupTestDB();
    });
    
    beforeEach(async () => {
        // Create test user and get auth token
        const response = await request(app)
            .post('/api/register')
            .send({
                username: 'testuser',
                email: 'test@example.com',
                password: 'password123'
            });
        
        authToken = response.body.token;
    });
    
    describe('GET /api/users', () => {
        it('should return users for authenticated user', async () => {
            const response = await request(app)
                .get('/api/users')
                .set('Authorization', `Bearer ${authToken}`)
                .expect(200);
            
            expect(response.body).toHaveProperty('users');
            expect(Array.isArray(response.body.users)).toBe(true);
        });
        
        it('should return 401 for unauthenticated request', async () => {
            await request(app)
                .get('/api/users')
                .expect(401);
        });
    });
});
```

## Deployment and Production

### Environment Configuration

```javascript
// config/index.js
const config = {
    development: {
        port: process.env.PORT || 3000,
        database: {
            url: process.env.DATABASE_URL || 'mongodb://localhost:27017/myapp_dev'
        },
        jwt: {
            secret: process.env.JWT_SECRET || 'dev-secret',
            expiresIn: '24h'
        }
    },
    production: {
        port: process.env.PORT || 3000,
        database: {
            url: process.env.DATABASE_URL
        },
        jwt: {
            secret: process.env.JWT_SECRET,
            expiresIn: '1h'
        }
    }
};

module.exports = config[process.env.NODE_ENV || 'development'];
```

### Docker Configuration

```dockerfile
# Dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
```

```yaml
# docker-compose.yml
version: '3.8'

services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DATABASE_URL=mongodb://mongo:27017/myapp
    depends_on:
      - mongo
  
  mongo:
    image: mongo:5.0
    ports:
      - "27017:27017"
    volumes:
      - mongo_data:/data/db

volumes:
  mongo_data:
```

### Production Checklist

- [ ] Environment variables configured
- [ ] Database connection secured
- [ ] Error logging implemented
- [ ] Rate limiting enabled
- [ ] CORS properly configured
- [ ] Input validation implemented
- [ ] Authentication and authorization
- [ ] HTTPS enabled
- [ ] Monitoring and logging
- [ ] Backup strategy
- [ ] Performance optimization
- [ ] Security headers
- [ ] Dependency vulnerabilities checked

## Final Exercises

### Exercise 1: Complete CRUD API
Build a complete CRUD API for a specific domain (e.g., books, movies, products) with:
- Full authentication
- Input validation
- Error handling
- Database integration
- API documentation

### Exercise 2: Real-time Application
Create a real-time application (chat, notifications, live updates) with:
- WebSocket implementation
- User presence
- Message persistence
- Room management

### Exercise 3: Full-stack Application
Build a complete full-stack application with:
- Backend API
- Frontend interface
- Database integration
- Authentication
- Deployment

### Exercise 4: Microservices Architecture
Design and implement a microservices architecture with:
- Multiple services
- Service communication
- API gateway
- Load balancing
- Monitoring

## Conclusion

Congratulations! You've completed the comprehensive JavaScript and Node.js course. You now have the knowledge and skills to:

- Write modern JavaScript code
- Build Node.js applications
- Create web APIs and servers
- Work with databases
- Implement authentication and security
- Use modern development tools
- Deploy applications to production

Continue practicing and building projects to solidify your understanding. The JavaScript ecosystem is constantly evolving, so stay updated with new features and best practices.

---

**Remember:** The best way to learn is by building real projects. Start small and gradually increase complexity as you become more comfortable with the concepts!
