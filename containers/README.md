# 🐳 JavaScript Course Containers

This directory contains Podman/Docker containers for the JavaScript course, providing pre-configured environments so students can focus on learning JavaScript concepts without worrying about infrastructure setup.

## 📚 Available Containers

### Development Environment
- **Container**: `js-course-dev`
- **Purpose**: Complete JavaScript development environment
- **Includes**: Node.js 20 LTS, npm, common tools, VS Code Server
- **Usage**: `podman run -it --rm -p 3000:3000 -p 8080:8080 js-course-dev`

### Node.js Backend Development
- **Container**: `js-course-nodejs`
- **Purpose**: Node.js backend development with database
- **Includes**: Node.js, Express, MongoDB, Redis, testing tools
- **Usage**: `podman run -it --rm -p 3000:3000 -p 27017:27017 js-course-nodejs`

### Multi-Service Setup
- **File**: `docker-compose.yml`
- **Purpose**: Complete stack with Node.js, MongoDB, Redis, and monitoring
- **Includes**: Web app, database, cache, monitoring
- **Usage**: `podman-compose up -d`

## 🚀 Quick Start

### Option 1: Single Development Container
```bash
# Build the development container
podman build -f Dockerfile.dev -t js-course-dev .

# Run with port mapping
podman run -it --rm \
  -p 3000:3000 \
  -p 8080:8080 \
  -v $(pwd)/../examples:/workspace/examples \
  js-course-dev
```

### Option 2: Multi-Service Setup
```bash
# Start all services
podman-compose up -d

# View logs
podman-compose logs -f

# Stop services
podman-compose down
```

## 🔧 Configuration

### Environment Variables
- `NODE_ENV`: Node environment (default: development)
- `PORT`: Application port (default: 3000)
- `MONGODB_URI`: MongoDB connection string
- `REDIS_URL`: Redis connection string

### Volume Mounts
- `./examples:/workspace/examples`: Mount examples directory
- `./data:/workspace/data`: Mount data directory
- `./projects:/workspace/projects`: Mount student projects

## 📝 Usage Examples

### Development Mode
```bash
# Start development container
podman run -it --rm \
  -p 3000:3000 \
  -p 8080:8080 \
  -v $(pwd)/../examples:/workspace/examples \
  js-course-dev

# Inside container, create a Node.js project
npm init -y
npm install express
node index.js
```

### Production Mode
```bash
# Start web application
podman run -d \
  -p 3000:3000 \
  -v $(pwd)/data:/workspace/data \
  js-course-nodejs
```

### Multi-Service Setup
```bash
# Start all services
podman-compose up -d

# Access services
# - Node.js app: http://localhost:3000
# - MongoDB: localhost:27017
# - Redis: localhost:6379
# - VS Code Server: http://localhost:8080
```

## 🎯 Course Integration

### Module 1-3: JavaScript Fundamentals
- Use `js-course-dev` container
- Focus on JavaScript language features
- No database required

### Module 4-5: Node.js Backend Development
- Use `js-course-nodejs` container
- MongoDB pre-configured
- Ready for backend development

### Module 6-7: Modern Ecosystem
- Use multi-service setup
- Complete stack available
- Real-world development environment

## 🔍 Troubleshooting

### Database Connection Issues
```bash
# Check if MongoDB is running
podman exec -it js-course-nodejs mongosh --eval "db.runCommand('ping')"

# Check Redis connection
podman exec -it js-course-nodejs redis-cli ping
```

### Port Conflicts
```bash
# Check what's using port 3000
lsof -i :3000

# Use different port
podman run -it --rm -p 3001:3000 js-course-dev
```

### Volume Mount Issues
```bash
# Check volume mounts
podman inspect js-course-dev | grep -A 10 "Mounts"

# Fix permissions
podman exec -it js-course-dev chown -R node:node /workspace
```

## 📚 Learning Path

### Beginner (Modules 1-3)
1. Start with `js-course-dev` container
2. Focus on JavaScript language basics
3. Use Node.js REPL for interactive learning

### Intermediate (Modules 4-5)
1. Switch to `js-course-nodejs` container
2. Learn Node.js backend development
3. Practice with examples

### Advanced (Modules 6-7)
1. Use multi-service setup for complex projects
2. Build real-world applications
3. Learn modern JavaScript ecosystem

## 🎓 Benefits

- **No Setup Hassles**: Pre-configured environment
- **Consistent Experience**: Same environment for all students
- **Focus on Learning**: No infrastructure distractions
- **Real-World Ready**: Production-like environment
- **Easy Cleanup**: Containers can be easily removed

## 📞 Support

All containers are tested and verified to work with the course materials.
For issues, check the troubleshooting section or refer to the course documentation.
