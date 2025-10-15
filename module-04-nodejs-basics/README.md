# Module 4: Node.js Basics

## What is Node.js?

Node.js is a JavaScript runtime built on Chrome's V8 JavaScript engine. It allows you to run JavaScript on the server-side, outside of web browsers.

### Key Features:
- **Event-driven** - Uses an event loop for non-blocking I/O operations
- **Non-blocking** - Asynchronous operations don't block the main thread
- **Single-threaded** - Uses a single main thread with event loop
- **Cross-platform** - Runs on Windows, macOS, and Linux
- **Large ecosystem** - Access to npm's vast package repository

### When to Use Node.js:
- **Web servers and APIs** - RESTful services, real-time applications
- **CLI tools** - Command-line applications and scripts
- **Microservices** - Lightweight, scalable services
- **Real-time applications** - Chat apps, gaming servers, live updates
- **Data streaming** - Processing large files or data streams

## Installing Node.js

### Option 1: Official Installer
1. Visit [nodejs.org](https://nodejs.org)
2. Download the LTS (Long Term Support) version
3. Run the installer and follow the setup wizard

### Option 2: Package Managers

**macOS (Homebrew):**
```bash
brew install node
```

**Ubuntu/Debian:**
```bash
curl -fsSL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
sudo apt-get install -y nodejs
```

**Windows (Chocolatey):**
```bash
choco install nodejs
```

### Verify Installation
```bash
node --version
npm --version
```

## Creating Your First Node.js Project

### 1. Initialize a Project

```bash
mkdir my-node-project
cd my-node-project
npm init -y
```

This creates a `package.json` file:
```json
{
  "name": "my-node-project",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "keywords": [],
  "author": "",
  "license": "ISC"
}
```

### 2. Create Your First Script

```javascript
// index.js
console.log("Hello from Node.js!");
console.log("Current directory:", process.cwd());
console.log("Node.js version:", process.version);
console.log("Platform:", process.platform);

// Access command line arguments
console.log("Command line arguments:", process.argv);

// Environment variables
console.log("NODE_ENV:", process.env.NODE_ENV || "development");
```

### 3. Run Your Script

```bash
node index.js
```

## Understanding npm and Package Management

### Package.json Explained

```json
{
  "name": "my-project",
  "version": "1.0.0",
  "description": "A sample Node.js project",
  "main": "index.js",
  "scripts": {
    "start": "node index.js",
    "dev": "nodemon index.js",
    "test": "jest",
    "build": "webpack"
  },
  "keywords": ["nodejs", "javascript"],
  "author": "Your Name",
  "license": "MIT",
  "dependencies": {
    "express": "^4.18.0",
    "lodash": "^4.17.21"
  },
  "devDependencies": {
    "nodemon": "^2.0.20",
    "jest": "^29.0.0"
  },
  "engines": {
    "node": ">=18.0.0"
  }
}
```

### Installing Packages

```bash
# Install a package
npm install express

# Install as dev dependency
npm install --save-dev nodemon

# Install globally
npm install -g nodemon

# Install specific version
npm install express@4.17.1

# Install from package.json
npm install
```

### Package Scripts

```bash
# Run scripts defined in package.json
npm start
npm run dev
npm test
npm run build

# Custom scripts
npm run lint
npm run deploy
```

## File System Operations

### Reading Files

```javascript
const fs = require('fs');

// Synchronous file reading
try {
    const data = fs.readFileSync('data.txt', 'utf8');
    console.log(data);
} catch (error) {
    console.error('Error reading file:', error.message);
}

// Asynchronous file reading
fs.readFile('data.txt', 'utf8', (error, data) => {
    if (error) {
        console.error('Error reading file:', error.message);
        return;
    }
    console.log(data);
});

// Promise-based file reading (Node.js 14+)
const fsPromises = require('fs').promises;

async function readFileAsync(filename) {
    try {
        const data = await fsPromises.readFile(filename, 'utf8');
        console.log(data);
        return data;
    } catch (error) {
        console.error('Error reading file:', error.message);
        throw error;
    }
}
```

### Writing Files

```javascript
const fs = require('fs');

// Synchronous file writing
try {
    fs.writeFileSync('output.txt', 'Hello, World!', 'utf8');
    console.log('File written successfully');
} catch (error) {
    console.error('Error writing file:', error.message);
}

// Asynchronous file writing
fs.writeFile('output.txt', 'Hello, World!', 'utf8', (error) => {
    if (error) {
        console.error('Error writing file:', error.message);
        return;
    }
    console.log('File written successfully');
});

// Promise-based file writing
async function writeFileAsync(filename, content) {
    try {
        await fsPromises.writeFile(filename, content, 'utf8');
        console.log('File written successfully');
    } catch (error) {
        console.error('Error writing file:', error.message);
        throw error;
    }
}
```

### Directory Operations

```javascript
const fs = require('fs');
const path = require('path');

// Create directory
fs.mkdir('new-directory', (error) => {
    if (error) {
        console.error('Error creating directory:', error.message);
        return;
    }
    console.log('Directory created successfully');
});

// Read directory contents
fs.readdir('.', (error, files) => {
    if (error) {
        console.error('Error reading directory:', error.message);
        return;
    }
    console.log('Files:', files);
});

// Check if file/directory exists
fs.access('file.txt', fs.constants.F_OK, (error) => {
    if (error) {
        console.log('File does not exist');
    } else {
        console.log('File exists');
    }
});

// Get file stats
fs.stat('file.txt', (error, stats) => {
    if (error) {
        console.error('Error getting file stats:', error.message);
        return;
    }
    console.log('File size:', stats.size);
    console.log('Is file:', stats.isFile());
    console.log('Is directory:', stats.isDirectory());
    console.log('Created:', stats.birthtime);
    console.log('Modified:', stats.mtime);
});
```

### Working with Paths

```javascript
const path = require('path');

// Join paths
const fullPath = path.join('/users', 'john', 'documents', 'file.txt');
console.log(fullPath); // /users/john/documents/file.txt

// Get directory name
const dirname = path.dirname('/users/john/file.txt');
console.log(dirname); // /users/john

// Get filename
const basename = path.basename('/users/john/file.txt');
console.log(basename); // file.txt

// Get file extension
const extname = path.extname('/users/john/file.txt');
console.log(extname); // .txt

// Resolve absolute path
const absolutePath = path.resolve('file.txt');
console.log(absolutePath); // /current/working/directory/file.txt

// Check if path is absolute
console.log(path.isAbsolute('/users/john/file.txt')); // true
console.log(path.isAbsolute('file.txt')); // false
```

## Building a CLI Application

### Basic CLI Structure

```javascript
// cli.js
#!/usr/bin/env node

const fs = require('fs');
const path = require('path');

// Parse command line arguments
const args = process.argv.slice(2);
const command = args[0];
const filename = args[1];

function showHelp() {
    console.log(`
Usage: node cli.js <command> [filename]

Commands:
  create <filename>  Create a new file
  read <filename>    Read file contents
  delete <filename>  Delete a file
  list               List all files in current directory
  help               Show this help message

Examples:
  node cli.js create myfile.txt
  node cli.js read myfile.txt
  node cli.js list
`);
}

function createFile(filename) {
    if (!filename) {
        console.error('Error: Please provide a filename');
        return;
    }
    
    try {
        fs.writeFileSync(filename, '');
        console.log(`File '${filename}' created successfully`);
    } catch (error) {
        console.error(`Error creating file: ${error.message}`);
    }
}

function readFile(filename) {
    if (!filename) {
        console.error('Error: Please provide a filename');
        return;
    }
    
    try {
        const content = fs.readFileSync(filename, 'utf8');
        console.log(`Contents of '${filename}':`);
        console.log(content);
    } catch (error) {
        console.error(`Error reading file: ${error.message}`);
    }
}

function deleteFile(filename) {
    if (!filename) {
        console.error('Error: Please provide a filename');
        return;
    }
    
    try {
        fs.unlinkSync(filename);
        console.log(`File '${filename}' deleted successfully`);
    } catch (error) {
        console.error(`Error deleting file: ${error.message}`);
    }
}

function listFiles() {
    try {
        const files = fs.readdirSync('.');
        console.log('Files in current directory:');
        files.forEach(file => {
            const stats = fs.statSync(file);
            const type = stats.isDirectory() ? '[DIR]' : '[FILE]';
            console.log(`${type} ${file}`);
        });
    } catch (error) {
        console.error(`Error listing files: ${error.message}`);
    }
}

// Main command handler
switch (command) {
    case 'create':
        createFile(filename);
        break;
    case 'read':
        readFile(filename);
        break;
    case 'delete':
        deleteFile(filename);
        break;
    case 'list':
        listFiles();
        break;
    case 'help':
    case '--help':
    case '-h':
        showHelp();
        break;
    default:
        console.error(`Unknown command: ${command}`);
        showHelp();
}
```

### Making CLI Executable

```json
// package.json
{
  "name": "my-cli-tool",
  "version": "1.0.0",
  "description": "A simple CLI tool",
  "main": "cli.js",
  "bin": {
    "mycli": "./cli.js"
  },
  "scripts": {
    "start": "node cli.js"
  }
}
```

```bash
# Make executable
chmod +x cli.js

# Install globally for development
npm link

# Now you can run it from anywhere
mycli create test.txt
mycli read test.txt
mycli list
```

### Advanced CLI with Commander.js

```bash
npm install commander
```

```javascript
// advanced-cli.js
#!/usr/bin/env node

const { Command } = require('commander');
const fs = require('fs');
const path = require('path');

const program = new Command();

program
    .name('file-manager')
    .description('A simple file management CLI tool')
    .version('1.0.0');

program
    .command('create')
    .description('Create a new file')
    .argument('<filename>', 'name of the file to create')
    .option('-c, --content <content>', 'initial content for the file')
    .action((filename, options) => {
        try {
            const content = options.content || '';
            fs.writeFileSync(filename, content);
            console.log(`✅ File '${filename}' created successfully`);
        } catch (error) {
            console.error(`❌ Error creating file: ${error.message}`);
        }
    });

program
    .command('read')
    .description('Read file contents')
    .argument('<filename>', 'name of the file to read')
    .option('-l, --lines <number>', 'number of lines to read', '10')
    .action((filename, options) => {
        try {
            const content = fs.readFileSync(filename, 'utf8');
            const lines = content.split('\n');
            const linesToShow = parseInt(options.lines);
            const output = lines.slice(0, linesToShow).join('\n');
            
            console.log(`📄 Contents of '${filename}' (first ${linesToShow} lines):`);
            console.log('─'.repeat(50));
            console.log(output);
        } catch (error) {
            console.error(`❌ Error reading file: ${error.message}`);
        }
    });

program
    .command('list')
    .description('List files in current directory')
    .option('-a, --all', 'show hidden files')
    .option('-l, --long', 'show detailed information')
    .action((options) => {
        try {
            const files = fs.readdirSync('.');
            const filteredFiles = options.all ? files : files.filter(file => !file.startsWith('.'));
            
            console.log('📁 Files in current directory:');
            console.log('─'.repeat(50));
            
            filteredFiles.forEach(file => {
                const stats = fs.statSync(file);
                const type = stats.isDirectory() ? '📁' : '📄';
                const size = stats.size;
                const modified = stats.mtime.toLocaleDateString();
                
                if (options.long) {
                    console.log(`${type} ${file.padEnd(20)} ${size.toString().padStart(8)} bytes ${modified}`);
                } else {
                    console.log(`${type} ${file}`);
                }
            });
        } catch (error) {
            console.error(`❌ Error listing files: ${error.message}`);
        }
    });

program.parse();
```

## Environment Variables and Configuration

### Using Environment Variables

```javascript
// Access environment variables
console.log('NODE_ENV:', process.env.NODE_ENV);
console.log('PORT:', process.env.PORT || 3000);
console.log('DATABASE_URL:', process.env.DATABASE_URL);

// Set environment variables
process.env.MY_VARIABLE = 'my-value';
console.log('MY_VARIABLE:', process.env.MY_VARIABLE);
```

### .env Files with dotenv

```bash
npm install dotenv
```

```javascript
// Load environment variables from .env file
require('dotenv').config();

console.log('Database URL:', process.env.DATABASE_URL);
console.log('API Key:', process.env.API_KEY);
```

```bash
# .env file
NODE_ENV=development
PORT=3000
DATABASE_URL=postgresql://user:password@localhost:5432/mydb
API_KEY=your-secret-api-key
JWT_SECRET=your-jwt-secret
```

## Exercises

### Exercise 1: File Organizer CLI
Create a CLI tool that:
1. Scans a directory for files
2. Organizes them by extension into subdirectories
3. Provides options for dry-run and backup

### Exercise 2: Log Analyzer
Create a tool that:
1. Reads log files
2. Parses common log formats
3. Provides statistics (errors, warnings, response times)
4. Exports results to JSON/CSV

### Exercise 3: Configuration Manager
Create a tool that:
1. Manages application configuration files
2. Supports different environments (dev, staging, prod)
3. Validates configuration schemas
4. Provides merge and diff functionality

### Exercise 4: Simple Web Server
Create a basic HTTP server that:
1. Serves static files
2. Handles different MIME types
3. Provides directory listing
4. Supports basic routing

## Next Steps

In the next module, we'll dive into web development with Node.js:
- HTTP module and creating servers
- Express.js framework
- Middleware and routing
- Working with databases
- Building RESTful APIs
- Authentication and security

---

**Practice Tip:** Build small CLI tools to practice file operations and command-line argument parsing!
