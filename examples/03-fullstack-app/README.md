# Example 3: Full-Stack Web Application

A complete full-stack web application with a React frontend and Node.js backend, featuring real-time chat functionality.

## Features

- User authentication and registration
- Real-time chat with WebSockets
- User profiles and avatars
- Message history
- Online user indicators
- Responsive design
- File upload for avatars
- Emoji support

## Architecture

- **Frontend**: React with Socket.io client
- **Backend**: Node.js with Express and Socket.io
- **Database**: MongoDB with Mongoose
- **Real-time**: WebSocket connections
- **Authentication**: JWT tokens
- **File Storage**: Local file system (can be extended to cloud storage)

## Installation

### Backend Setup
```bash
cd examples/03-fullstack-app/backend
npm install
```

### Frontend Setup
```bash
cd examples/03-fullstack-app/frontend
npm install
```

## Setup

1. **Backend Configuration**
   Create `backend/.env`:
   ```env
   NODE_ENV=development
   PORT=5000
   MONGODB_URI=mongodb://localhost:27017/chat-app
   JWT_SECRET=your-super-secret-jwt-key
   UPLOAD_PATH=uploads/
   ```

2. **Frontend Configuration**
   Create `frontend/.env`:
   ```env
   REACT_APP_API_URL=http://localhost:5000
   REACT_APP_SOCKET_URL=http://localhost:5000
   ```

## Usage

### Development Mode
```bash
# Terminal 1 - Backend
cd backend
npm run dev

# Terminal 2 - Frontend
cd frontend
npm start
```

### Production Mode
```bash
# Build frontend
cd frontend
npm run build

# Start backend (serves frontend)
cd backend
npm start
```

## Project Structure

```
03-fullstack-app/
├── backend/
│   ├── src/
│   │   ├── controllers/     # Route handlers
│   │   ├── models/          # Database models
│   │   ├── middleware/       # Custom middleware
│   │   ├── routes/          # Route definitions
│   │   ├── socket/          # Socket.io handlers
│   │   ├── utils/           # Utility functions
│   │   └── app.js           # Express app setup
│   ├── uploads/             # File uploads directory
│   ├── .env.example         # Environment variables example
│   ├── package.json         # Backend dependencies
│   └── README.md           # Backend documentation
├── frontend/
│   ├── src/
│   │   ├── components/      # React components
│   │   ├── pages/           # Page components
│   │   ├── hooks/           # Custom hooks
│   │   ├── services/        # API services
│   │   ├── utils/           # Utility functions
│   │   ├── context/         # React context
│   │   └── App.js           # Main App component
│   ├── public/              # Static files
│   ├── .env.example         # Environment variables example
│   ├── package.json         # Frontend dependencies
│   └── README.md           # Frontend documentation
└── README.md               # This file
```

## Implementation

### Backend Implementation

#### package.json
```json
{
  "name": "chat-app-backend",
  "version": "1.0.0",
  "description": "Backend for full-stack chat application",
  "main": "src/app.js",
  "scripts": {
    "start": "node src/app.js",
    "dev": "nodemon src/app.js"
  },
  "dependencies": {
    "express": "^4.18.0",
    "socket.io": "^4.7.0",
    "mongoose": "^7.0.0",
    "jsonwebtoken": "^9.0.0",
    "bcryptjs": "^2.4.3",
    "multer": "^1.4.5",
    "cors": "^2.8.5",
    "helmet": "^7.0.0",
    "dotenv": "^16.0.0",
    "express-validator": "^7.0.0"
  },
  "devDependencies": {
    "nodemon": "^2.0.20"
  }
}
```

#### src/models/User.js
```javascript
const mongoose = require('mongoose');
const bcrypt = require('bcryptjs');

const userSchema = new mongoose.Schema({
    username: {
        type: String,
        required: true,
        unique: true,
        trim: true,
        minlength: 3,
        maxlength: 30
    },
    email: {
        type: String,
        required: true,
        unique: true,
        lowercase: true,
        trim: true
    },
    password: {
        type: String,
        required: true,
        minlength: 6
    },
    avatar: {
        type: String,
        default: null
    },
    status: {
        type: String,
        enum: ['online', 'offline', 'away'],
        default: 'offline'
    },
    lastSeen: {
        type: Date,
        default: Date.now
    }
}, {
    timestamps: true
});

// Hash password before saving
userSchema.pre('save', async function(next) {
    if (!this.isModified('password')) return next();
    
    try {
        const salt = await bcrypt.genSalt(10);
        this.password = await bcrypt.hash(this.password, salt);
        next();
    } catch (error) {
        next(error);
    }
});

// Compare password method
userSchema.methods.comparePassword = async function(candidatePassword) {
    return bcrypt.compare(candidatePassword, this.password);
};

// Remove password from JSON output
userSchema.methods.toJSON = function() {
    const user = this.toObject();
    delete user.password;
    return user;
};

module.exports = mongoose.model('User', userSchema);
```

#### src/models/Message.js
```javascript
const mongoose = require('mongoose');

const messageSchema = new mongoose.Schema({
    content: {
        type: String,
        required: true,
        maxlength: 1000
    },
    sender: {
        type: mongoose.Schema.Types.ObjectId,
        ref: 'User',
        required: true
    },
    room: {
        type: String,
        required: true,
        default: 'general'
    },
    type: {
        type: String,
        enum: ['text', 'image', 'file'],
        default: 'text'
    },
    readBy: [{
        user: {
            type: mongoose.Schema.Types.ObjectId,
            ref: 'User'
        },
        readAt: {
            type: Date,
            default: Date.now
        }
    }]
}, {
    timestamps: true
});

// Populate sender information
messageSchema.pre('find', function() {
    this.populate('sender', 'username avatar');
});

module.exports = mongoose.model('Message', messageSchema);
```

#### src/socket/socketHandlers.js
```javascript
const jwt = require('jsonwebtoken');
const User = require('../models/User');
const Message = require('../models/Message');

const authenticateSocket = async (socket, next) => {
    try {
        const token = socket.handshake.auth.token;
        
        if (!token) {
            return next(new Error('Authentication error'));
        }
        
        const decoded = jwt.verify(token, process.env.JWT_SECRET);
        const user = await User.findById(decoded.userId);
        
        if (!user) {
            return next(new Error('Authentication error'));
        }
        
        socket.userId = user._id.toString();
        socket.user = user;
        next();
    } catch (error) {
        next(new Error('Authentication error'));
    }
};

const handleConnection = (io) => {
    return (socket) => {
        console.log(`User ${socket.user.username} connected`);
        
        // Update user status
        User.findByIdAndUpdate(socket.userId, {
            status: 'online',
            lastSeen: new Date()
        }).exec();
        
        // Join user to general room
        socket.join('general');
        
        // Notify others about user coming online
        socket.to('general').emit('user-online', {
            userId: socket.userId,
            username: socket.user.username,
            avatar: socket.user.avatar
        });
        
        // Send online users list
        User.find({ status: 'online' }, 'username avatar')
            .then(onlineUsers => {
                socket.emit('online-users', onlineUsers);
            });
        
        // Handle new messages
        socket.on('send-message', async (data) => {
            try {
                const { content, room = 'general' } = data;
                
                const message = new Message({
                    content,
                    sender: socket.userId,
                    room
                });
                
                await message.save();
                
                // Broadcast message to room
                io.to(room).emit('new-message', {
                    _id: message._id,
                    content: message.content,
                    sender: {
                        _id: socket.userId,
                        username: socket.user.username,
                        avatar: socket.user.avatar
                    },
                    room: message.room,
                    createdAt: message.createdAt
                });
            } catch (error) {
                socket.emit('error', { message: 'Failed to send message' });
            }
        });
        
        // Handle typing indicators
        socket.on('typing', (data) => {
            socket.to(data.room || 'general').emit('user-typing', {
                userId: socket.userId,
                username: socket.user.username,
                isTyping: data.isTyping
            });
        });
        
        // Handle room changes
        socket.on('join-room', (room) => {
            socket.leave('general');
            socket.join(room);
            socket.emit('joined-room', room);
        });
        
        // Handle disconnection
        socket.on('disconnect', async () => {
            console.log(`User ${socket.user.username} disconnected`);
            
            // Update user status
            await User.findByIdAndUpdate(socket.userId, {
                status: 'offline',
                lastSeen: new Date()
            });
            
            // Notify others about user going offline
            socket.to('general').emit('user-offline', {
                userId: socket.userId,
                username: socket.user.username
            });
        });
    };
};

module.exports = { authenticateSocket, handleConnection };
```

### Frontend Implementation

#### package.json
```json
{
  "name": "chat-app-frontend",
  "version": "1.0.0",
  "private": true,
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-router-dom": "^6.8.0",
    "socket.io-client": "^4.7.0",
    "axios": "^1.3.0",
    "react-hook-form": "^7.43.0",
    "react-hot-toast": "^2.4.0",
    "emoji-picker-react": "^4.4.0",
    "date-fns": "^2.29.0"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
  "devDependencies": {
    "react-scripts": "5.0.1"
  }
}
```

#### src/context/AuthContext.js
```javascript
import React, { createContext, useContext, useState, useEffect } from 'react';
import { authService } from '../services/authService';

const AuthContext = createContext();

export const useAuth = () => {
    const context = useContext(AuthContext);
    if (!context) {
        throw new Error('useAuth must be used within an AuthProvider');
    }
    return context;
};

export const AuthProvider = ({ children }) => {
    const [user, setUser] = useState(null);
    const [loading, setLoading] = useState(true);

    useEffect(() => {
        const token = localStorage.getItem('token');
        if (token) {
            authService.getProfile()
                .then(userData => {
                    setUser(userData.user);
                })
                .catch(() => {
                    localStorage.removeItem('token');
                })
                .finally(() => {
                    setLoading(false);
                });
        } else {
            setLoading(false);
        }
    }, []);

    const login = async (email, password) => {
        try {
            const response = await authService.login(email, password);
            localStorage.setItem('token', response.token);
            setUser(response.user);
            return response;
        } catch (error) {
            throw error;
        }
    };

    const register = async (userData) => {
        try {
            const response = await authService.register(userData);
            localStorage.setItem('token', response.token);
            setUser(response.user);
            return response;
        } catch (error) {
            throw error;
        }
    };

    const logout = () => {
        localStorage.removeItem('token');
        setUser(null);
    };

    const value = {
        user,
        login,
        register,
        logout,
        loading
    };

    return (
        <AuthContext.Provider value={value}>
            {children}
        </AuthContext.Provider>
    );
};
```

#### src/hooks/useSocket.js
```javascript
import { useEffect, useState } from 'react';
import { io } from 'socket.io-client';

export const useSocket = () => {
    const [socket, setSocket] = useState(null);
    const [connected, setConnected] = useState(false);

    useEffect(() => {
        const token = localStorage.getItem('token');
        
        if (token) {
            const newSocket = io(process.env.REACT_APP_SOCKET_URL, {
                auth: {
                    token
                }
            });

            newSocket.on('connect', () => {
                setConnected(true);
            });

            newSocket.on('disconnect', () => {
                setConnected(false);
            });

            setSocket(newSocket);

            return () => {
                newSocket.close();
            };
        }
    }, []);

    return { socket, connected };
};
```

#### src/components/Chat.js
```javascript
import React, { useState, useEffect, useRef } from 'react';
import { useSocket } from '../hooks/useSocket';
import { messageService } from '../services/messageService';
import { useAuth } from '../context/AuthContext';
import toast from 'react-hot-toast';

const Chat = () => {
    const [messages, setMessages] = useState([]);
    const [newMessage, setNewMessage] = useState('');
    const [onlineUsers, setOnlineUsers] = useState([]);
    const [typingUsers, setTypingUsers] = useState([]);
    const { socket, connected } = useSocket();
    const { user } = useAuth();
    const messagesEndRef = useRef(null);
    const typingTimeoutRef = useRef(null);

    useEffect(() => {
        if (socket) {
            // Load message history
            messageService.getMessages()
                .then(data => {
                    setMessages(data.messages);
                })
                .catch(error => {
                    toast.error('Failed to load messages');
                });

            // Socket event listeners
            socket.on('new-message', (message) => {
                setMessages(prev => [...prev, message]);
            });

            socket.on('online-users', (users) => {
                setOnlineUsers(users);
            });

            socket.on('user-online', (user) => {
                setOnlineUsers(prev => [...prev, user]);
                toast.success(`${user.username} is now online`);
            });

            socket.on('user-offline', (user) => {
                setOnlineUsers(prev => prev.filter(u => u._id !== user.userId));
                toast(`${user.username} is now offline`);
            });

            socket.on('user-typing', (data) => {
                if (data.isTyping) {
                    setTypingUsers(prev => [...prev.filter(u => u.userId !== data.userId), data]);
                } else {
                    setTypingUsers(prev => prev.filter(u => u.userId !== data.userId));
                }
            });

            return () => {
                socket.off('new-message');
                socket.off('online-users');
                socket.off('user-online');
                socket.off('user-offline');
                socket.off('user-typing');
            };
        }
    }, [socket]);

    useEffect(() => {
        scrollToBottom();
    }, [messages]);

    const scrollToBottom = () => {
        messagesEndRef.current?.scrollIntoView({ behavior: 'smooth' });
    };

    const handleSendMessage = (e) => {
        e.preventDefault();
        
        if (newMessage.trim() && socket) {
            socket.emit('send-message', {
                content: newMessage.trim(),
                room: 'general'
            });
            setNewMessage('');
        }
    };

    const handleTyping = (e) => {
        setNewMessage(e.target.value);
        
        if (socket) {
            socket.emit('typing', { isTyping: true, room: 'general' });
            
            if (typingTimeoutRef.current) {
                clearTimeout(typingTimeoutRef.current);
            }
            
            typingTimeoutRef.current = setTimeout(() => {
                socket.emit('typing', { isTyping: false, room: 'general' });
            }, 1000);
        }
    };

    const formatTime = (timestamp) => {
        return new Date(timestamp).toLocaleTimeString([], {
            hour: '2-digit',
            minute: '2-digit'
        });
    };

    return (
        <div className="chat-container">
            <div className="chat-header">
                <h2>General Chat</h2>
                <div className="connection-status">
                    <span className={`status-indicator ${connected ? 'online' : 'offline'}`}></span>
                    {connected ? 'Connected' : 'Disconnected'}
                </div>
            </div>

            <div className="online-users">
                <h3>Online Users ({onlineUsers.length})</h3>
                <div className="users-list">
                    {onlineUsers.map(user => (
                        <div key={user._id} className="user-item">
                            <img 
                                src={user.avatar || '/default-avatar.png'} 
                                alt={user.username}
                                className="user-avatar"
                            />
                            <span>{user.username}</span>
                        </div>
                    ))}
                </div>
            </div>

            <div className="messages-container">
                {messages.map(message => (
                    <div 
                        key={message._id} 
                        className={`message ${message.sender._id === user.id ? 'own' : 'other'}`}
                    >
                        <div className="message-content">
                            <div className="message-header">
                                <img 
                                    src={message.sender.avatar || '/default-avatar.png'} 
                                    alt={message.sender.username}
                                    className="message-avatar"
                                />
                                <span className="message-sender">{message.sender.username}</span>
                                <span className="message-time">{formatTime(message.createdAt)}</span>
                            </div>
                            <div className="message-text">{message.content}</div>
                        </div>
                    </div>
                ))}
                
                {typingUsers.length > 0 && (
                    <div className="typing-indicator">
                        {typingUsers.map(user => user.username).join(', ')} is typing...
                    </div>
                )}
                
                <div ref={messagesEndRef} />
            </div>

            <form onSubmit={handleSendMessage} className="message-form">
                <input
                    type="text"
                    value={newMessage}
                    onChange={handleTyping}
                    placeholder="Type your message..."
                    className="message-input"
                />
                <button type="submit" className="send-button" disabled={!connected}>
                    Send
                </button>
            </form>
        </div>
    );
};

export default Chat;
```

## Key Learning Points

1. **Full-Stack Architecture**: Frontend and backend integration
2. **Real-time Communication**: WebSocket implementation with Socket.io
3. **Authentication Flow**: JWT tokens across frontend and backend
4. **State Management**: React context for global state
5. **Custom Hooks**: Reusable logic with React hooks
6. **File Upload**: Handling file uploads and storage
7. **Responsive Design**: Mobile-friendly interface
8. **Error Handling**: Comprehensive error handling and user feedback

## Extensions

- Add private messaging
- Implement message reactions
- Add file sharing
- Create chat rooms
- Add message search
- Implement push notifications
- Add voice/video calling
- Create mobile app with React Native

## Deployment

### Backend Deployment
```bash
# Build and deploy backend
cd backend
npm run build
# Deploy to your preferred platform (Heroku, AWS, etc.)
```

### Frontend Deployment
```bash
# Build frontend
cd frontend
npm run build
# Deploy to Netlify, Vercel, or serve from backend
```

This full-stack application demonstrates the complete development cycle from backend API development to frontend user interface, showcasing modern web development practices and real-time communication.
