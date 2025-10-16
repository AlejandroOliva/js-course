# 🎓 Complete JavaScript & Node.js Course - From Zero to Expert

Welcome to the complete JavaScript and Node.js course! This course will take you from basic concepts to building full-stack web applications and APIs.

## 📚 Course Index

### **[Module 1: Introduction to JavaScript](module-01-introduction/README.md)**

* What is JavaScript?
* Why JavaScript?
* Setting up your development environment
* JavaScript runtime environments
* Basic syntax and variables

### **[Module 2: JavaScript Fundamentals](module-02-fundamentals/README.md)**

* Data types and structures
* Functions and scope
* Objects and arrays
* Control flow and loops
* Error handling

### **[Module 3: Advanced JavaScript Concepts](module-03-advanced-concepts/README.md)**

* Closures and higher-order functions
* Promises and async/await
* Modules and imports/exports
* Classes and inheritance
* Prototypes and this keyword

### **[Module 4: Node.js Basics](module-04-nodejs-basics/README.md)**

* What is Node.js?
* Installing Node.js
* Creating your first Node.js project
* Understanding npm and package management
* File system operations
* Building a CLI application

### **[Module 5: Node.js Web Development](module-05-nodejs-web-development/README.md)**

* HTTP module and creating servers
* Express.js framework
* Middleware and routing
* Working with databases (MongoDB/PostgreSQL)
* RESTful APIs
* Authentication and security

### **[Module 6: Modern JavaScript Ecosystem](module-06-modern-ecosystem/README.md)**

* ES6+ features
* Build tools (Webpack, Vite)
* Testing frameworks (Jest, Mocha)
* TypeScript introduction
* Frontend frameworks overview (React, Vue, Angular)
* Full-stack development

### **[Module 7: Practical Exercises](module-07-practical-exercises/README.md)**

* Progressive exercises
* Real projects
* Commented solutions

## 🚀 How to Use This Course

### Option 1: Using Containers (Recommended)
Each module has a pre-configured container environment:

```bash
# Build and run the development container
cd containers
podman build -f Dockerfile.dev -t js-course-dev .
podman run -it --rm -p 3000:3000 -p 8080:8080 js-course-dev

# Or use the multi-service setup
podman-compose up -d
```

### Option 2: Local Development
1. **Read each module in order** - The course is designed to be progressive
2. **Practice each example** - Run all code in your terminal or browser console
3. **Do the exercises** - Practice is fundamental
4. **Get extra practice** - Use platforms like Codewars, LeetCode, or freeCodeCamp
5. **Experiment** - Don't be afraid to try variations and build your own projects

## 📋 Prerequisites

* Basic programming knowledge (any language)
* Command line/terminal familiarity
* Node.js 18+ installed (see Module 1 for installation)
  * **Recommended**: Use ASDF version manager for easy Node.js management
* A modern web browser
* Willingness to learn 😊

## 🎯 Course Objective

Upon completing this course, you will be able to:

* ✅ Write JavaScript code confidently
* ✅ Build Node.js applications
* ✅ Create web servers and APIs
* ✅ Work with databases
* ✅ Understand modern JavaScript features
* ✅ Apply asynchronous programming principles
* ✅ Build full-stack applications

---

## 📖 Let's Begin

👉 **Go to Module 1: Introduction to JavaScript**

---

## 📚 Reference Guides

* **START-HERE.md** - Complete start guide with study plans
* **QUICK-GUIDE.md** - Syntax and command cheat sheet
* **ADDITIONAL-RESOURCES.md** - Links, books, videos, community

---

## 💻 Practical Examples

The `examples/` directory contains ready-to-run projects:

1. **Simple CLI Tool** (`examples/01-cli-tool/`)
2. **REST API** (`examples/02-rest-api/`)
3. **Full-Stack Web App** (`examples/03-fullstack-app/`)

---

_Remember that practice is the key to learning! Don't hesitate to experiment and make mistakes._
