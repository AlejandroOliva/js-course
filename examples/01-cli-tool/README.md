# Example 1: Simple CLI Tool

A command-line tool for managing a personal task list with file persistence.

## Features

- Add tasks
- List all tasks
- Mark tasks as complete
- Delete tasks
- Save tasks to file
- Load tasks from file

## Installation

```bash
cd examples/01-cli-tool
npm install
```

## Usage

```bash
# Add a task
node cli.js add "Buy groceries"

# List all tasks
node cli.js list

# Mark task as complete
node cli.js complete 1

# Delete a task
node cli.js delete 1

# Show help
node cli.js --help
```

## Project Structure

```
01-cli-tool/
├── cli.js           # Main CLI application
├── task-manager.js  # Task management logic
├── package.json     # Project configuration
└── README.md        # This file
```

## Implementation

### package.json
```json
{
  "name": "task-cli-tool",
  "version": "1.0.0",
  "description": "A simple CLI tool for managing tasks",
  "main": "cli.js",
  "bin": {
    "task-cli": "./cli.js"
  },
  "scripts": {
    "start": "node cli.js"
  },
  "keywords": ["cli", "tasks", "productivity"],
  "author": "Your Name",
  "license": "MIT"
}
```

### task-manager.js
```javascript
const fs = require('fs');
const path = require('path');

class TaskManager {
    constructor(dataFile = 'tasks.json') {
        this.dataFile = dataFile;
        this.tasks = this.loadTasks();
    }
    
    loadTasks() {
        try {
            if (fs.existsSync(this.dataFile)) {
                const data = fs.readFileSync(this.dataFile, 'utf8');
                return JSON.parse(data);
            }
        } catch (error) {
            console.error('Error loading tasks:', error.message);
        }
        return [];
    }
    
    saveTasks() {
        try {
            fs.writeFileSync(this.dataFile, JSON.stringify(this.tasks, null, 2));
        } catch (error) {
            console.error('Error saving tasks:', error.message);
        }
    }
    
    addTask(description) {
        const task = {
            id: Date.now(),
            description,
            completed: false,
            createdAt: new Date().toISOString()
        };
        
        this.tasks.push(task);
        this.saveTasks();
        return task;
    }
    
    listTasks() {
        return this.tasks;
    }
    
    completeTask(id) {
        const task = this.tasks.find(t => t.id === id);
        if (task) {
            task.completed = true;
            task.completedAt = new Date().toISOString();
            this.saveTasks();
            return task;
        }
        return null;
    }
    
    deleteTask(id) {
        const index = this.tasks.findIndex(t => t.id === id);
        if (index !== -1) {
            const deletedTask = this.tasks.splice(index, 1)[0];
            this.saveTasks();
            return deletedTask;
        }
        return null;
    }
    
    getTaskById(id) {
        return this.tasks.find(t => t.id === id);
    }
}

module.exports = TaskManager;
```

### cli.js
```javascript
#!/usr/bin/env node

const TaskManager = require('./task-manager');

class TaskCLI {
    constructor() {
        this.taskManager = new TaskManager();
    }
    
    showHelp() {
        console.log(`
Task CLI Tool

Usage: node cli.js <command> [arguments]

Commands:
  add <description>     Add a new task
  list                   List all tasks
  complete <id>          Mark task as complete
  delete <id>            Delete a task
  help                   Show this help message

Examples:
  node cli.js add "Buy groceries"
  node cli.js list
  node cli.js complete 1
  node cli.js delete 1
`);
    }
    
    addTask(description) {
        if (!description) {
            console.error('Error: Please provide a task description');
            return;
        }
        
        const task = this.taskManager.addTask(description);
        console.log(`✅ Task added: ${task.description} (ID: ${task.id})`);
    }
    
    listTasks() {
        const tasks = this.taskManager.listTasks();
        
        if (tasks.length === 0) {
            console.log('No tasks found.');
            return;
        }
        
        console.log('\n📋 Task List:');
        console.log('─'.repeat(50));
        
        tasks.forEach(task => {
            const status = task.completed ? '✅' : '⏳';
            const completedAt = task.completed ? ` (completed: ${new Date(task.completedAt).toLocaleDateString()})` : '';
            console.log(`${status} [${task.id}] ${task.description}${completedAt}`);
        });
        
        console.log(`\nTotal: ${tasks.length} tasks`);
    }
    
    completeTask(id) {
        const taskId = parseInt(id);
        if (isNaN(taskId)) {
            console.error('Error: Please provide a valid task ID');
            return;
        }
        
        const task = this.taskManager.completeTask(taskId);
        if (task) {
            console.log(`✅ Task completed: ${task.description}`);
        } else {
            console.error(`Error: Task with ID ${taskId} not found`);
        }
    }
    
    deleteTask(id) {
        const taskId = parseInt(id);
        if (isNaN(taskId)) {
            console.error('Error: Please provide a valid task ID');
            return;
        }
        
        const task = this.taskManager.deleteTask(taskId);
        if (task) {
            console.log(`🗑️  Task deleted: ${task.description}`);
        } else {
            console.error(`Error: Task with ID ${taskId} not found`);
        }
    }
    
    run() {
        const args = process.argv.slice(2);
        const command = args[0];
        
        switch (command) {
            case 'add':
                this.addTask(args.slice(1).join(' '));
                break;
            case 'list':
                this.listTasks();
                break;
            case 'complete':
                this.completeTask(args[1]);
                break;
            case 'delete':
                this.deleteTask(args[1]);
                break;
            case 'help':
            case '--help':
            case '-h':
                this.showHelp();
                break;
            default:
                if (!command) {
                    this.showHelp();
                } else {
                    console.error(`Unknown command: ${command}`);
                    this.showHelp();
                }
        }
    }
}

// Run the CLI
const cli = new TaskCLI();
cli.run();
```

## Testing

```bash
# Test adding tasks
node cli.js add "Learn JavaScript"
node cli.js add "Build a project"
node cli.js add "Deploy to production"

# Test listing tasks
node cli.js list

# Test completing a task
node cli.js complete 1

# Test deleting a task
node cli.js delete 2

# Test final list
node cli.js list
```

## Key Learning Points

1. **File System Operations**: Reading and writing JSON files
2. **Command Line Arguments**: Parsing and handling CLI arguments
3. **Object-Oriented Programming**: Using classes for organization
4. **Error Handling**: Graceful error handling and user feedback
5. **Data Persistence**: Saving and loading data between sessions

## Extensions

- Add due dates to tasks
- Implement task categories
- Add search functionality
- Create a web interface
- Add task priorities
- Implement task dependencies
