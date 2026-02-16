# 📚 Node.js & Express.js Interview Questions
## Complete Guide: Basic to Advanced

---

## Table of Contents
1. [Node.js Basics](#nodejs-basics)
2. [Node.js Intermediate](#nodejs-intermediate)
3. [Node.js Advanced](#nodejs-advanced)
4. [Express.js Basics](#expressjs-basics)
5. [Express.js Intermediate](#expressjs-intermediate)
6. [Express.js Advanced](#expressjs-advanced)
7. [Practical Coding Questions](#practical-coding-questions)
8. [Best Practices](#best-practices)

---

# Node.js Basics

## 1. What is Node.js?

**Answer:**
Node.js is a JavaScript runtime built on Chrome's V8 JavaScript engine. It allows you to run JavaScript on the server side.

**Key Features:**
- Event-driven
- Non-blocking I/O
- Single-threaded with event loop
- Built on V8 engine
- Cross-platform

**Example:**
```javascript
// Simple Node.js server
const http = require('http');

const server = http.createServer((req, res) => {
    res.writeHead(200, {'Content-Type': 'text/plain'});
    res.end('Hello World!');
});

server.listen(3000, () => {
    console.log('Server running on port 3000');
});
```

---

## 2. What is NPM?

**Answer:**
NPM (Node Package Manager) is the default package manager for Node.js. It helps install, manage, and share packages.

**Key Commands:**
```bash
npm init                 # Initialize package.json
npm install express      # Install package locally
npm install -g nodemon   # Install globally
npm install --save-dev jest  # Install as dev dependency
npm uninstall package-name   # Remove package
npm update              # Update packages
npm list                # List installed packages
```

**package.json Example:**
```json
{
  "name": "my-app",
  "version": "1.0.0",
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js",
    "test": "jest"
  },
  "dependencies": {
    "express": "^4.18.0"
  },
  "devDependencies": {
    "nodemon": "^2.0.20"
  }
}
```

---

## 3. What is the Event Loop in Node.js?

**Answer:**
The Event Loop is what allows Node.js to perform non-blocking I/O operations despite JavaScript being single-threaded.

**How it Works:**
1. Execute synchronous code
2. Execute microtasks (Promises, process.nextTick)
3. Execute timer callbacks (setTimeout, setInterval)
4. Execute I/O callbacks
5. Execute setImmediate callbacks
6. Execute close callbacks

**Example:**
```javascript
console.log('1 - Start');

setTimeout(() => {
    console.log('2 - setTimeout');
}, 0);

Promise.resolve().then(() => {
    console.log('3 - Promise');
});

process.nextTick(() => {
    console.log('4 - nextTick');
});

console.log('5 - End');

// Output:
// 1 - Start
// 5 - End
// 4 - nextTick
// 3 - Promise
// 2 - setTimeout
```

---

## 4. What is the difference between `require()` and `import`?

**Answer:**

**CommonJS (require):**
```javascript
// Synchronous loading
const express = require('express');
const { sum } = require('./utils');

// Dynamic imports possible
const moduleName = 'express';
const express = require(moduleName);
```

**ES6 Modules (import):**
```javascript
// Static imports (must be at top)
import express from 'express';
import { sum } from './utils.js';

// Named imports
import { readFile, writeFile } from 'fs';

// Import everything
import * as fs from 'fs';

// Dynamic imports (async)
const module = await import('./module.js');
```

**Differences:**
| Feature | require() | import |
|---------|-----------|--------|
| Type | CommonJS | ES6 |
| Loading | Synchronous | Asynchronous |
| Usage | Runtime | Compile time |
| Dynamic | Yes | Limited |

---

## 5. What are Callbacks in Node.js?

**Answer:**
A callback is a function passed as an argument to another function, executed after an operation completes.

**Example:**
```javascript
// Error-first callback pattern
const fs = require('fs');

fs.readFile('file.txt', 'utf8', (err, data) => {
    if (err) {
        console.error('Error reading file:', err);
        return;
    }
    console.log('File content:', data);
});

// Custom callback example
function fetchUser(id, callback) {
    setTimeout(() => {
        const user = { id, name: 'John' };
        callback(null, user);
    }, 1000);
}

fetchUser(1, (err, user) => {
    if (err) return console.error(err);
    console.log('User:', user);
});
```

**Callback Hell (Problem):**
```javascript
// Nested callbacks - hard to read
getData(function(a) {
    getMoreData(a, function(b) {
        getEvenMoreData(b, function(c) {
            getFinalData(c, function(d) {
                console.log(d);
            });
        });
    });
});
```

---

## 6. What are Promises?

**Answer:**
Promises are objects representing the eventual completion or failure of an asynchronous operation.

**States:**
- Pending
- Fulfilled (resolved)
- Rejected

**Example:**
```javascript
// Creating a Promise
const myPromise = new Promise((resolve, reject) => {
    setTimeout(() => {
        const success = true;
        if (success) {
            resolve('Operation successful!');
        } else {
            reject('Operation failed!');
        }
    }, 1000);
});

// Using Promise
myPromise
    .then(result => {
        console.log(result);
        return 'Next step';
    })
    .then(data => {
        console.log(data);
    })
    .catch(error => {
        console.error(error);
    })
    .finally(() => {
        console.log('Promise completed');
    });

// Chaining Promises
function fetchUser() {
    return new Promise((resolve) => {
        setTimeout(() => resolve({ id: 1, name: 'John' }), 1000);
    });
}

function fetchPosts(userId) {
    return new Promise((resolve) => {
        setTimeout(() => resolve(['Post 1', 'Post 2']), 1000);
    });
}

fetchUser()
    .then(user => {
        console.log('User:', user);
        return fetchPosts(user.id);
    })
    .then(posts => {
        console.log('Posts:', posts);
    })
    .catch(error => {
        console.error('Error:', error);
    });
```

---

## 7. What is Async/Await?

**Answer:**
Async/await is syntactic sugar over Promises, making asynchronous code look synchronous.

**Example:**
```javascript
// Using async/await
async function getUserData() {
    try {
        const user = await fetchUser();
        console.log('User:', user);
        
        const posts = await fetchPosts(user.id);
        console.log('Posts:', posts);
        
        return { user, posts };
    } catch (error) {
        console.error('Error:', error);
        throw error;
    }
}

// Call async function
getUserData()
    .then(data => console.log('Data:', data))
    .catch(err => console.error('Error:', err));

// Multiple async operations
async function fetchAllData() {
    // Sequential (slower)
    const user1 = await fetchUser(1);
    const user2 = await fetchUser(2);
    
    // Parallel (faster)
    const [user1, user2] = await Promise.all([
        fetchUser(1),
        fetchUser(2)
    ]);
    
    return [user1, user2];
}

// Error handling
async function safeOperation() {
    try {
        const result = await riskyOperation();
        return result;
    } catch (error) {
        console.error('Operation failed:', error);
        return null;
    }
}
```

---

## 8. What are Modules in Node.js?

**Answer:**
Modules are reusable blocks of code that can be imported and used in other files.

**Types:**
1. Core modules (built-in)
2. Local modules (custom)
3. Third-party modules (npm)

**Example:**
```javascript
// math.js - Creating a module
function add(a, b) {
    return a + b;
}

function subtract(a, b) {
    return a - b;
}

// Exporting
module.exports = { add, subtract };
// OR
exports.add = add;
exports.subtract = subtract;

// app.js - Using the module
const math = require('./math');
console.log(math.add(5, 3));      // 8
console.log(math.subtract(5, 3)); // 2

// Destructuring
const { add, subtract } = require('./math');
console.log(add(5, 3));           // 8

// Core modules
const fs = require('fs');
const http = require('http');
const path = require('path');

// Third-party modules
const express = require('express');
const axios = require('axios');
```

---

## 9. What is `module.exports` vs `exports`?

**Answer:**

**module.exports:**
```javascript
// Can export anything
module.exports = function() {
    console.log('Hello');
};

// OR
module.exports = {
    name: 'John',
    age: 30
};

// OR
class User {
    constructor(name) {
        this.name = name;
    }
}
module.exports = User;
```

**exports:**
```javascript
// Only for adding properties
exports.name = 'John';
exports.age = 30;

// This WON'T work (breaks reference)
exports = { name: 'John' };  // ❌ Wrong!

// This WILL work
exports.user = { name: 'John' };  // ✅ Correct
```

**Key Difference:**
- `module.exports` is the actual object returned
- `exports` is just a reference to `module.exports`
- Reassigning `exports` breaks the reference

---

## 10. What is the `global` object?

**Answer:**
The global object in Node.js is similar to `window` in browsers. Variables and functions defined globally are accessible everywhere.

**Example:**
```javascript
// Global variables
console.log(__dirname);   // Current directory
console.log(__filename);  // Current file
console.log(process);     // Process information

// Global functions
console.log();
setTimeout();
setInterval();
setImmediate();

// Custom global (avoid in production)
global.myGlobalVar = 'Hello';
console.log(myGlobalVar);  // Accessible everywhere

// Better approach - use modules
// config.js
module.exports = {
    appName: 'MyApp',
    version: '1.0.0'
};
```

---

# Node.js Intermediate

## 11. What is the `Buffer` class?

**Answer:**
Buffer is used to handle binary data in Node.js. It's a global class.

**Example:**
```javascript
// Creating buffers
const buf1 = Buffer.from('Hello');
const buf2 = Buffer.alloc(10);           // 10 bytes of zeros
const buf3 = Buffer.allocUnsafe(10);     // Faster, uninitialized

// Reading buffer
console.log(buf1.toString());            // 'Hello'
console.log(buf1.toString('hex'));       // '48656c6c6f'
console.log(buf1.length);                // 5

// Writing to buffer
const buf = Buffer.alloc(10);
buf.write('Hello');

// Buffer operations
const buf4 = Buffer.concat([buf1, buf2]);
const buf5 = buf1.slice(0, 2);

// Converting
const json = JSON.stringify({ name: 'John' });
const jsonBuffer = Buffer.from(json);
const backToString = jsonBuffer.toString();
```

---

## 12. What are Streams in Node.js?

**Answer:**
Streams are collections of data that might not be available all at once. Used for reading/writing data in chunks.

**Types:**
1. Readable
2. Writable
3. Duplex (both)
4. Transform

**Example:**
```javascript
const fs = require('fs');

// Readable stream
const readStream = fs.createReadStream('input.txt', {
    encoding: 'utf8',
    highWaterMark: 16 * 1024  // 16KB chunks
});

readStream.on('data', (chunk) => {
    console.log('Received chunk:', chunk);
});

readStream.on('end', () => {
    console.log('Finished reading');
});

readStream.on('error', (err) => {
    console.error('Error:', err);
});

// Writable stream
const writeStream = fs.createWriteStream('output.txt');
writeStream.write('Hello ');
writeStream.write('World!');
writeStream.end();

// Piping streams
const readStream = fs.createReadStream('input.txt');
const writeStream = fs.createWriteStream('output.txt');
readStream.pipe(writeStream);

// Chaining pipes
const zlib = require('zlib');
fs.createReadStream('input.txt')
    .pipe(zlib.createGzip())
    .pipe(fs.createWriteStream('input.txt.gz'));
```

---

## 13. What is Middleware in Node.js?

**Answer:**
Middleware functions have access to request and response objects and can modify them or end the request-response cycle.

**Example:**
```javascript
// Simple middleware
function logger(req, res, next) {
    console.log(`${req.method} ${req.url}`);
    next();  // Pass control to next middleware
}

// Async middleware
async function authenticate(req, res, next) {
    try {
        const token = req.headers.authorization;
        const user = await verifyToken(token);
        req.user = user;
        next();
    } catch (error) {
        res.status(401).json({ error: 'Unauthorized' });
    }
}

// Error handling middleware
function errorHandler(err, req, res, next) {
    console.error(err.stack);
    res.status(500).json({ error: err.message });
}

// Using middleware
app.use(logger);
app.use(authenticate);
app.use(errorHandler);
```

---

## 14. What is `process.nextTick()` vs `setImmediate()`?

**Answer:**

**process.nextTick():**
- Executes before the event loop continues
- Runs before any I/O operations

**setImmediate():**
- Executes in the next iteration of event loop
- Runs after I/O operations

**Example:**
```javascript
console.log('Start');

process.nextTick(() => {
    console.log('nextTick 1');
});

setImmediate(() => {
    console.log('setImmediate 1');
});

setTimeout(() => {
    console.log('setTimeout');
}, 0);

Promise.resolve().then(() => {
    console.log('Promise');
});

console.log('End');

// Output:
// Start
// End
// nextTick 1
// Promise
// setTimeout
// setImmediate 1

// Practical use
function compute(data, callback) {
    // Use nextTick for immediate callback
    if (!data) {
        return process.nextTick(() => {
            callback(new Error('No data'));
        });
    }
    
    // Process data
    setImmediate(() => {
        callback(null, processData(data));
    });
}
```

---

## 15. How to handle errors in Node.js?

**Answer:**

**Try-Catch (Synchronous):**
```javascript
try {
    const result = JSON.parse(invalidJSON);
} catch (error) {
    console.error('Parse error:', error.message);
}
```

**Callbacks (Error-first pattern):**
```javascript
fs.readFile('file.txt', (err, data) => {
    if (err) {
        console.error('Error:', err);
        return;
    }
    console.log(data);
});
```

**Promises:**
```javascript
fetchData()
    .then(result => console.log(result))
    .catch(error => console.error('Error:', error));
```

**Async/Await:**
```javascript
async function getData() {
    try {
        const result = await fetchData();
        return result;
    } catch (error) {
        console.error('Error:', error);
        throw error;
    }
}
```

**Global Error Handlers:**
```javascript
// Uncaught exceptions
process.on('uncaughtException', (error) => {
    console.error('Uncaught Exception:', error);
    process.exit(1);
});

// Unhandled promise rejections
process.on('unhandledRejection', (reason, promise) => {
    console.error('Unhandled Rejection:', reason);
    process.exit(1);
});
```

---

## 16. What is Clustering in Node.js?

**Answer:**
Clustering allows you to create child processes (workers) that share the same server port, utilizing multiple CPU cores.

**Example:**
```javascript
const cluster = require('cluster');
const http = require('http');
const numCPUs = require('os').cpus().length;

if (cluster.isMaster) {
    console.log(`Master ${process.pid} is running`);
    
    // Fork workers
    for (let i = 0; i < numCPUs; i++) {
        cluster.fork();
    }
    
    cluster.on('exit', (worker, code, signal) => {
        console.log(`Worker ${worker.process.pid} died`);
        cluster.fork();  // Replace dead worker
    });
} else {
    // Workers share TCP connection
    http.createServer((req, res) => {
        res.writeHead(200);
        res.end('Hello from worker ' + process.pid);
    }).listen(3000);
    
    console.log(`Worker ${process.pid} started`);
}
```

---

## 17. What is the Event Emitter?

**Answer:**
EventEmitter is a class that facilitates communication between objects in Node.js. It's the foundation of Node's event-driven architecture.

**Example:**
```javascript
const EventEmitter = require('events');

// Create event emitter
class MyEmitter extends EventEmitter {}
const myEmitter = new MyEmitter();

// Register event listeners
myEmitter.on('event', (data) => {
    console.log('Event occurred:', data);
});

myEmitter.once('onceEvent', () => {
    console.log('This runs only once');
});

// Emit events
myEmitter.emit('event', { message: 'Hello' });
myEmitter.emit('onceEvent');
myEmitter.emit('onceEvent');  // Won't trigger

// Real-world example
class Order extends EventEmitter {
    placeOrder(item) {
        console.log('Order placed:', item);
        this.emit('orderPlaced', item);
    }
}

const order = new Order();

order.on('orderPlaced', (item) => {
    console.log('Sending confirmation email for:', item);
});

order.on('orderPlaced', (item) => {
    console.log('Updating inventory for:', item);
});

order.placeOrder('Laptop');

// Remove listeners
const handler = () => console.log('Event');
myEmitter.on('test', handler);
myEmitter.removeListener('test', handler);
```

---

## 18. What is `process.env`?

**Answer:**
`process.env` is an object containing environment variables.

**Example:**
```javascript
// .env file
PORT=3000
DB_HOST=localhost
DB_USER=admin
DB_PASS=secret123
NODE_ENV=production

// Using dotenv package
require('dotenv').config();

const port = process.env.PORT || 3000;
const dbConfig = {
    host: process.env.DB_HOST,
    user: process.env.DB_USER,
    password: process.env.DB_PASS
};

// Check environment
if (process.env.NODE_ENV === 'production') {
    // Production settings
} else {
    // Development settings
}

// Set environment variable
process.env.MY_VAR = 'value';

// Run with environment variables
// PORT=3000 node app.js
```

---

## 19. What is the Child Process module?

**Answer:**
The child_process module allows you to spawn child processes.

**Methods:**
- `spawn()` - Streams data
- `exec()` - Buffers data
- `execFile()` - Similar to exec
- `fork()` - Special case of spawn for Node processes

**Example:**
```javascript
const { spawn, exec, fork } = require('child_process');

// Using spawn (for large data)
const ls = spawn('ls', ['-lh', '/usr']);

ls.stdout.on('data', (data) => {
    console.log(`stdout: ${data}`);
});

ls.stderr.on('data', (data) => {
    console.error(`stderr: ${data}`);
});

ls.on('close', (code) => {
    console.log(`Child process exited with code ${code}`);
});

// Using exec (for small data)
exec('ls -lh /usr', (error, stdout, stderr) => {
    if (error) {
        console.error(`Error: ${error}`);
        return;
    }
    console.log(`Output: ${stdout}`);
});

// Using fork (for Node processes)
const child = fork('child.js');

child.on('message', (msg) => {
    console.log('Message from child:', msg);
});

child.send({ hello: 'world' });

// child.js
process.on('message', (msg) => {
    console.log('Message from parent:', msg);
    process.send({ received: true });
});
```

---

## 20. What is the difference between `readFile` and `createReadStream`?

**Answer:**

**readFile:**
- Reads entire file into memory
- Good for small files
- Simple API

**createReadStream:**
- Reads file in chunks
- Good for large files
- Memory efficient

**Example:**
```javascript
const fs = require('fs');

// readFile - loads entire file
fs.readFile('large-file.txt', 'utf8', (err, data) => {
    if (err) throw err;
    console.log(data);  // Entire file content
});

// createReadStream - chunks
const stream = fs.createReadStream('large-file.txt', {
    encoding: 'utf8',
    highWaterMark: 64 * 1024  // 64KB chunks
});

stream.on('data', (chunk) => {
    console.log('Chunk received:', chunk.length);
});

stream.on('end', () => {
    console.log('File reading completed');
});

// Performance comparison
// Large file (100MB)
// readFile: ~200MB memory usage
// createReadStream: ~64KB memory usage
```

---

# Express.js Basics

## 21. What is Express.js?

**Answer:**
Express.js is a minimal and flexible Node.js web application framework providing features for web and mobile applications.

**Features:**
- Routing
- Middleware
- Template engines
- Error handling
- Static file serving

**Example:**
```javascript
const express = require('express');
const app = express();

// Middleware
app.use(express.json());

// Routes
app.get('/', (req, res) => {
    res.send('Hello World!');
});

app.get('/api/users', (req, res) => {
    res.json([
        { id: 1, name: 'John' },
        { id: 2, name: 'Jane' }
    ]);
});

app.post('/api/users', (req, res) => {
    const user = req.body;
    res.status(201).json(user);
});

// Start server
app.listen(3000, () => {
    console.log('Server running on port 3000');
});
```

---

## 22. What is Routing in Express?

**Answer:**
Routing refers to how an application responds to client requests to particular endpoints.

**Example:**
```javascript
const express = require('express');
const app = express();

// Basic routes
app.get('/', (req, res) => {
    res.send('GET request');
});

app.post('/', (req, res) => {
    res.send('POST request');
});

app.put('/user', (req, res) => {
    res.send('PUT request');
});

app.delete('/user', (req, res) => {
    res.send('DELETE request');
});

// Route parameters
app.get('/users/:id', (req, res) => {
    const userId = req.params.id;
    res.send(`User ID: ${userId}`);
});

// Multiple parameters
app.get('/users/:userId/posts/:postId', (req, res) => {
    const { userId, postId } = req.params;
    res.json({ userId, postId });
});

// Query parameters
app.get('/search', (req, res) => {
    const { q, page, limit } = req.query;
    res.json({ q, page, limit });
    // /search?q=test&page=1&limit=10
});

// Route with regex
app.get(/.*fly$/, (req, res) => {
    res.send('Matches butterfly, dragonfly, etc.');
});
```

---

## 23. What is Middleware in Express?

**Answer:**
Middleware functions execute during the request-response cycle and have access to `req`, `res`, and `next`.

**Types:**
1. Application-level
2. Router-level
3. Error-handling
4. Built-in
5. Third-party

**Example:**
```javascript
// Application-level middleware
app.use((req, res, next) => {
    console.log('Time:', Date.now());
    next();
});

// Router-level middleware
const router = express.Router();
router.use((req, res, next) => {
    console.log('Router middleware');
    next();
});

// Route-specific middleware
app.get('/user/:id', authenticate, (req, res) => {
    res.send('User data');
});

// Built-in middleware
app.use(express.json());                    // Parse JSON
app.use(express.urlencoded({ extended: true }));  // Parse URL-encoded
app.use(express.static('public'));          // Serve static files

// Third-party middleware
const cors = require('cors');
const morgan = require('morgan');
app.use(cors());
app.use(morgan('combined'));

// Error-handling middleware (4 parameters)
app.use((err, req, res, next) => {
    console.error(err.stack);
    res.status(500).send('Something broke!');
});

// Multiple middleware
app.get('/user/:id',
    authenticate,
    authorize,
    (req, res) => {
        res.send('User data');
    }
);
```

---

## 24. What is `req` and `res` in Express?

**Answer:**

**req (Request):**
```javascript
app.get('/user/:id', (req, res) => {
    // URL parameters
    const id = req.params.id;
    
    // Query parameters
    const { page, limit } = req.query;
    // /user/1?page=2&limit=10
    
    // Request body
    const data = req.body;
    
    // Headers
    const token = req.headers.authorization;
    const contentType = req.get('Content-Type');
    
    // Cookies
    const sessionId = req.cookies.sessionId;
    
    // Request method
    console.log(req.method);  // GET, POST, etc.
    
    // Request URL
    console.log(req.url);
    console.log(req.path);
    console.log(req.originalUrl);
    
    // IP address
    console.log(req.ip);
});
```

**res (Response):**
```javascript
app.get('/api/data', (req, res) => {
    // Send JSON
    res.json({ message: 'Success', data: [] });
    
    // Send plain text
    res.send('Hello World');
    
    // Set status code
    res.status(404).send('Not Found');
    res.status(201).json({ created: true });
    
    // Set headers
    res.set('Content-Type', 'application/json');
    res.setHeader('X-Custom-Header', 'value');
    
    // Send file
    res.sendFile('/path/to/file.pdf');
    
    // Download file
    res.download('/path/to/file.pdf');
    
    // Redirect
    res.redirect('/new-url');
    res.redirect(301, '/permanent-url');
    
    // Render template
    res.render('index', { title: 'Home' });
    
    // Set cookie
    res.cookie('name', 'value', { maxAge: 900000 });
    
    // Clear cookie
    res.clearCookie('name');
    
    // Chain methods
    res.status(200)
       .set('Content-Type', 'application/json')
       .json({ success: true });
});
```

---

## 25. How to handle POST requests in Express?

**Answer:**
```javascript
const express = require('express');
const app = express();

// Parse JSON bodies
app.use(express.json());

// Parse URL-encoded bodies
app.use(express.urlencoded({ extended: true }));

// Handle POST request
app.post('/api/users', (req, res) => {
    const { name, email, age } = req.body;
    
    // Validation
    if (!name || !email) {
        return res.status(400).json({
            error: 'Name and email are required'
        });
    }
    
    // Create user
    const user = {
        id: Date.now(),
        name,
        email,
        age
    };
    
    // Save to database (example)
    // await User.create(user);
    
    res.status(201).json({
        message: 'User created',
        user
    });
});

// Form submission
app.post('/submit-form', (req, res) => {
    const formData = req.body;
    console.log(formData);
    res.redirect('/thank-you');
});

// File upload
const multer = require('multer');
const upload = multer({ dest: 'uploads/' });

app.post('/upload', upload.single('file'), (req, res) => {
    const file = req.file;
    res.json({
        message: 'File uploaded',
        filename: file.filename
    });
});
```

---

# Express.js Intermediate

## 26. How to implement authentication in Express?

**Answer:**
```javascript
const jwt = require('jsonwebtoken');
const bcrypt = require('bcrypt');

// Register
app.post('/register', async (req, res) => {
    try {
        const { email, password } = req.body;
        
        // Hash password
        const hashedPassword = await bcrypt.hash(password, 10);
        
        // Save user
        const user = await User.create({
            email,
            password: hashedPassword
        });
        
        res.status(201).json({
            message: 'User registered',
            userId: user.id
        });
    } catch (error) {
        res.status(500).json({ error: error.message });
    }
});

// Login
app.post('/login', async (req, res) => {
    try {
        const { email, password } = req.body;
        
        // Find user
        const user = await User.findOne({ email });
        if (!user) {
            return res.status(401).json({ error: 'Invalid credentials' });
        }
        
        // Check password
        const isValid = await bcrypt.compare(password, user.password);
        if (!isValid) {
            return res.status(401).json({ error: 'Invalid credentials' });
        }
        
        // Generate token
        const token = jwt.sign(
            { userId: user.id, email: user.email },
            process.env.JWT_SECRET,
            { expiresIn: '24h' }
        );
        
        res.json({ token, user: { id: user.id, email: user.email } });
    } catch (error) {
        res.status(500).json({ error: error.message });
    }
});

// Authentication middleware
function authenticate(req, res, next) {
    try {
        const token = req.headers.authorization?.split(' ')[1];
        
        if (!token) {
            return res.status(401).json({ error: 'No token provided' });
        }
        
        const decoded = jwt.verify(token, process.env.JWT_SECRET);
        req.user = decoded;
        next();
    } catch (error) {
        res.status(401).json({ error: 'Invalid token' });
    }
}

// Protected route
app.get('/profile', authenticate, (req, res) => {
    res.json({ user: req.user });
});
```

---

## 27. How to implement file upload in Express?

**Answer:**
```javascript
const express = require('express');
const multer = require('multer');
const path = require('path');

const app = express();

// Configure storage
const storage = multer.diskStorage({
    destination: (req, file, cb) => {
        cb(null, 'uploads/');
    },
    filename: (req, file, cb) => {
        const uniqueSuffix = Date.now() + '-' + Math.round(Math.random() * 1E9);
        cb(null, file.fieldname + '-' + uniqueSuffix + path.extname(file.originalname));
    }
});

// File filter
const fileFilter = (req, file, cb) => {
    const allowedTypes = ['image/jpeg', 'image/png', 'image/gif'];
    
    if (allowedTypes.includes(file.mimetype)) {
        cb(null, true);
    } else {
        cb(new Error('Invalid file type'), false);
    }
};

// Create upload middleware
const upload = multer({
    storage: storage,
    fileFilter: fileFilter,
    limits: {
        fileSize: 5 * 1024 * 1024  // 5MB limit
    }
});

// Single file upload
app.post('/upload', upload.single('file'), (req, res) => {
    if (!req.file) {
        return res.status(400).json({ error: 'No file uploaded' });
    }
    
    res.json({
        message: 'File uploaded successfully',
        file: {
            filename: req.file.filename,
            originalname: req.file.originalname,
            size: req.file.size,
            path: req.file.path
        }
    });
});

// Multiple files
app.post('/upload-multiple', upload.array('files', 5), (req, res) => {
    res.json({
        message: `${req.files.length} files uploaded`,
        files: req.files.map(file => ({
            filename: file.filename,
            size: file.size
        }))
    });
});

// Multiple fields
app.post('/upload-fields', upload.fields([
    { name: 'avatar', maxCount: 1 },
    { name: 'gallery', maxCount: 8 }
]), (req, res) => {
    res.json({
        avatar: req.files['avatar'],
        gallery: req.files['gallery']
    });
});

// Error handling
app.use((err, req, res, next) => {
    if (err instanceof multer.MulterError) {
        return res.status(400).json({ error: err.message });
    }
    res.status(500).json({ error: err.message });
});
```

---

## 28. How to implement validation in Express?

**Answer:**
```javascript
const { body, validationResult, param, query } = require('express-validator');

// Validation middleware
const validateUser = [
    body('email')
        .isEmail()
        .withMessage('Must be a valid email')
        .normalizeEmail(),
    
    body('password')
        .isLength({ min: 6 })
        .withMessage('Password must be at least 6 characters')
        .matches(/\d/)
        .withMessage('Password must contain a number'),
    
    body('age')
        .optional()
        .isInt({ min: 18, max: 100 })
        .withMessage('Age must be between 18 and 100'),
    
    body('phone')
        .optional()
        .isMobilePhone()
        .withMessage('Must be a valid phone number')
];

// Check validation results
function checkValidation(req, res, next) {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
        return res.status(400).json({ errors: errors.array() });
    }
    next();
}

// Use validation
app.post('/register', validateUser, checkValidation, (req, res) => {
    // Request is validated
    const { email, password } = req.body;
    res.json({ message: 'User registered' });
});

// Parameter validation
app.get('/users/:id',
    param('id').isInt().withMessage('ID must be an integer'),
    checkValidation,
    (req, res) => {
        res.json({ userId: req.params.id });
    }
);

// Query parameter validation
app.get('/search',
    query('q').notEmpty().withMessage('Query is required'),
    query('page').optional().isInt({ min: 1 }),
    checkValidation,
    (req, res) => {
        res.json({ query: req.query.q });
    }
);

// Custom validator
const validateUserExists = async (value) => {
    const user = await User.findById(value);
    if (!user) {
        throw new Error('User not found');
    }
    return true;
};

app.post('/follow/:userId',
    param('userId').custom(validateUserExists),
    checkValidation,
    (req, res) => {
        res.json({ message: 'Following user' });
    }
);
```

---

## 29. How to implement CORS in Express?

**Answer:**
```javascript
const express = require('express');
const cors = require('cors');

const app = express();

// Simple usage (allow all)
app.use(cors());

// Custom configuration
app.use(cors({
    origin: 'http://localhost:3000',  // Allow specific origin
    methods: ['GET', 'POST', 'PUT', 'DELETE'],
    allowedHeaders: ['Content-Type', 'Authorization'],
    credentials: true,  // Allow cookies
    maxAge: 3600  // Cache preflight for 1 hour
}));

// Multiple origins
const allowedOrigins = [
    'http://localhost:3000',
    'https://example.com',
    'https://app.example.com'
];

app.use(cors({
    origin: function(origin, callback) {
        if (!origin) return callback(null, true);
        
        if (allowedOrigins.indexOf(origin) === -1) {
            return callback(new Error('Not allowed by CORS'), false);
        }
        return callback(null, true);
    }
}));

// Dynamic CORS
app.use((req, res, next) => {
    const origin = req.headers.origin;
    if (allowedOrigins.includes(origin)) {
        res.setHeader('Access-Control-Allow-Origin', origin);
    }
    res.setHeader('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE');
    res.setHeader('Access-Control-Allow-Headers', 'Content-Type, Authorization');
    res.setHeader('Access-Control-Allow-Credentials', 'true');
    
    if (req.method === 'OPTIONS') {
        return res.sendStatus(200);
    }
    next();
});

// Route-specific CORS
app.get('/public-api', cors(), (req, res) => {
    res.json({ message: 'Public API' });
});

app.get('/private-api', 
    cors({ origin: 'https://trusted-app.com' }),
    (req, res) => {
        res.json({ message: 'Private API' });
    }
);
```

---

## 30. How to implement rate limiting in Express?

**Answer:**
```javascript
const rateLimit = require('express-rate-limit');
const RedisStore = require('rate-limit-redis');
const redis = require('redis');

// Basic rate limiting
const limiter = rateLimit({
    windowMs: 15 * 60 * 1000,  // 15 minutes
    max: 100,  // Limit each IP to 100 requests per windowMs
    message: 'Too many requests, please try again later.',
    standardHeaders: true,  // Return rate limit info in headers
    legacyHeaders: false
});

// Apply to all requests
app.use(limiter);

// Strict rate limit for authentication
const authLimiter = rateLimit({
    windowMs: 15 * 60 * 1000,
    max: 5,  // 5 attempts per 15 minutes
    skipSuccessfulRequests: true  // Don't count successful requests
});

app.post('/login', authLimiter, (req, res) => {
    // Login logic
});

// Redis-based rate limiting (distributed)
const redisClient = redis.createClient({
    host: 'localhost',
    port: 6379
});

const redisLimiter = rateLimit({
    store: new RedisStore({
        client: redisClient,
        prefix: 'rate-limit:'
    }),
    windowMs: 15 * 60 * 1000,
    max: 100
});

// Different limits for different routes
app.get('/api/data', 
    rateLimit({ windowMs: 60000, max: 30 }),
    (req, res) => {
        res.json({ data: [] });
    }
);

// Skip rate limiting for certain users
const skipLimiting = (req) => {
    return req.user && req.user.role === 'admin';
};

app.use(rateLimit({
    windowMs: 60000,
    max: 10,
    skip: skipLimiting
}));

// Custom key generator
const apiLimiter = rateLimit({
    windowMs: 60000,
    max: 10,
    keyGenerator: (req) => {
        return req.headers['x-api-key'] || req.ip;
    }
});
```

---

# Node.js Advanced

## 31. How does Node.js handle concurrency?

**Answer:**
Node.js uses an event-driven, non-blocking I/O model with a single-threaded event loop.

**Architecture:**
```
┌───────────────────────────┐
│           Thread          │
│                           │
│    ┌──────────────┐       │
│    │  Call Stack  │       │
│    └──────────────┘       │
│           ↕                │
│    ┌──────────────┐       │
│    │  Event Loop  │       │
│    └──────────────┘       │
│           ↕                │
│    ┌──────────────┐       │
│    │ Callback Queue│       │
│    └──────────────┘       │
└───────────────────────────┘
        ↕
┌───────────────────────────┐
│      Thread Pool          │
│  (for blocking operations)│
│  - File I/O               │
│  - DNS lookups            │
│  - Crypto operations      │
└───────────────────────────┘
```

**Example:**
```javascript
// Non-blocking I/O
console.log('Start');

fs.readFile('file.txt', (err, data) => {
    console.log('File read complete');
});

console.log('End');
// Output: Start, End, File read complete

// Concurrent operations
async function processMultipleFiles() {
    const files = ['file1.txt', 'file2.txt', 'file3.txt'];
    
    // Sequential (slow)
    for (const file of files) {
        await fs.promises.readFile(file);
    }
    
    // Concurrent (fast)
    await Promise.all(
        files.map(file => fs.promises.readFile(file))
    );
}

// Worker threads for CPU-intensive tasks
const { Worker } = require('worker_threads');

function runWorker(workerData) {
    return new Promise((resolve, reject) => {
        const worker = new Worker('./worker.js', { workerData });
        worker.on('message', resolve);
        worker.on('error', reject);
        worker.on('exit', (code) => {
            if (code !== 0) reject(new Error(`Worker stopped with code ${code}`));
        });
    });
}

// Process tasks concurrently
const results = await Promise.all([
    runWorker({ task: 1 }),
    runWorker({ task: 2 }),
    runWorker({ task: 3 })
]);
```

---

## 32. What are Worker Threads?

**Answer:**
Worker threads allow running JavaScript in parallel, useful for CPU-intensive operations.

**Example:**
```javascript
// main.js
const { Worker } = require('worker_threads');

function runWorker(workerData) {
    return new Promise((resolve, reject) => {
        const worker = new Worker('./worker.js', {
            workerData
        });
        
        worker.on('message', (result) => {
            console.log('Worker result:', result);
            resolve(result);
        });
        
        worker.on('error', reject);
        
        worker.on('exit', (code) => {
            if (code !== 0) {
                reject(new Error(`Worker stopped with code ${code}`));
            }
        });
    });
}

// CPU-intensive task
async function calculatePrimes() {
    const result = await runWorker({ max: 1000000 });
    console.log(`Found ${result.primes.length} primes`);
}

// worker.js
const { workerData, parentPort } = require('worker_threads');

function findPrimes(max) {
    const primes = [];
    for (let i = 2; i <= max; i++) {
        let isPrime = true;
        for (let j = 2; j <= Math.sqrt(i); j++) {
            if (i % j === 0) {
                isPrime = false;
                break;
            }
        }
        if (isPrime) primes.push(i);
    }
    return primes;
}

const primes = findPrimes(workerData.max);
parentPort.postMessage({ primes });

// Communication between workers
// main.js
const worker1 = new Worker('./worker1.js');
const worker2 = new Worker('./worker2.js');

worker1.on('message', (msg) => {
    worker2.postMessage(msg);  // Forward to worker2
});

worker2.on('message', (result) => {
    console.log('Final result:', result);
});
```

---

## 33. How to implement caching in Node.js?

**Answer:**
```javascript
const redis = require('redis');
const client = redis.createClient();

// Simple in-memory cache
class Cache {
    constructor() {
        this.cache = new Map();
    }
    
    set(key, value, ttl = 3600) {
        const expiry = Date.now() + (ttl * 1000);
        this.cache.set(key, { value, expiry });
    }
    
    get(key) {
        const item = this.cache.get(key);
        if (!item) return null;
        
        if (Date.now() > item.expiry) {
            this.cache.delete(key);
            return null;
        }
        
        return item.value;
    }
    
    delete(key) {
        this.cache.delete(key);
    }
    
    clear() {
        this.cache.clear();
    }
}

const cache = new Cache();

// Using cache
app.get('/api/users/:id', async (req, res) => {
    const userId = req.params.id;
    const cacheKey = `user:${userId}`;
    
    // Check cache
    const cached = cache.get(cacheKey);
    if (cached) {
        return res.json({ source: 'cache', data: cached });
    }
    
    // Fetch from database
    const user = await User.findById(userId);
    
    // Store in cache
    cache.set(cacheKey, user, 3600);  // 1 hour
    
    res.json({ source: 'database', data: user });
});

// Redis cache
app.get('/api/posts', async (req, res) => {
    const cacheKey = 'posts:all';
    
    // Check Redis
    client.get(cacheKey, async (err, data) => {
        if (data) {
            return res.json({
                source: 'cache',
                data: JSON.parse(data)
            });
        }
        
        // Fetch from database
        const posts = await Post.find();
        
        // Store in Redis (expire in 1 hour)
        client.setex(cacheKey, 3600, JSON.stringify(posts));
        
        res.json({ source: 'database', data: posts });
    });
});

// Cache invalidation
app.post('/api/posts', async (req, res) => {
    const post = await Post.create(req.body);
    
    // Invalidate cache
    client.del('posts:all');
    cache.delete('posts:all');
    
    res.json(post);
});

// Cache middleware
function cacheMiddleware(duration) {
    return (req, res, next) => {
        const key = `cache:${req.originalUrl}`;
        
        client.get(key, (err, data) => {
            if (data) {
                return res.json(JSON.parse(data));
            }
            
            // Override res.json to cache response
            const originalJson = res.json;
            res.json = function(body) {
                client.setex(key, duration, JSON.stringify(body));
                originalJson.call(this, body);
            };
            
            next();
        });
    };
}

// Use cache middleware
app.get('/api/data', cacheMiddleware(300), async (req, res) => {
    const data = await fetchData();
    res.json(data);
});
```

---

## 34. How to implement WebSockets in Node.js?

**Answer:**
```javascript
const express = require('express');
const http = require('http');
const WebSocket = require('ws');

const app = express();
const server = http.createServer(app);
const wss = new WebSocket.Server({ server });

// Connection handling
wss.on('connection', (ws, req) => {
    console.log('New client connected');
    
    // Send welcome message
    ws.send(JSON.stringify({
        type: 'welcome',
        message: 'Connected to server'
    }));
    
    // Receive messages
    ws.on('message', (message) => {
        console.log('Received:', message);
        
        try {
            const data = JSON.parse(message);
            
            // Handle different message types
            switch(data.type) {
                case 'chat':
                    // Broadcast to all clients
                    broadcast(data);
                    break;
                case 'private':
                    // Send to specific client
                    sendToUser(data.userId, data.message);
                    break;
                default:
                    ws.send(JSON.stringify({ error: 'Unknown type' }));
            }
        } catch (error) {
            ws.send(JSON.stringify({ error: 'Invalid message' }));
        }
    });
    
    // Handle disconnection
    ws.on('close', () => {
        console.log('Client disconnected');
    });
    
    // Handle errors
    ws.on('error', (error) => {
        console.error('WebSocket error:', error);
    });
});

// Broadcast to all clients
function broadcast(data) {
    wss.clients.forEach((client) => {
        if (client.readyState === WebSocket.OPEN) {
            client.send(JSON.stringify(data));
        }
    });
}

// Send to specific user
const users = new Map();  // Store user connections

wss.on('connection', (ws, req) => {
    ws.on('message', (message) => {
        const data = JSON.parse(message);
        
        if (data.type === 'register') {
            users.set(data.userId, ws);
        }
    });
    
    ws.on('close', () => {
        // Remove user
        for (const [userId, socket] of users.entries()) {
            if (socket === ws) {
                users.delete(userId);
                break;
            }
        }
    });
});

function sendToUser(userId, message) {
    const ws = users.get(userId);
    if (ws && ws.readyState === WebSocket.OPEN) {
        ws.send(JSON.stringify(message));
    }
}

// Heartbeat (keep alive)
function heartbeat() {
    this.isAlive = true;
}

wss.on('connection', (ws) => {
    ws.isAlive = true;
    ws.on('pong', heartbeat);
});

const interval = setInterval(() => {
    wss.clients.forEach((ws) => {
        if (ws.isAlive === false) {
            return ws.terminate();
        }
        ws.isAlive = false;
        ws.ping();
    });
}, 30000);

wss.on('close', () => {
    clearInterval(interval);
});

server.listen(3000, () => {
    console.log('Server running on port 3000');
});

// Client-side usage
// const ws = new WebSocket('ws://localhost:3000');
// ws.onopen = () => {
//     ws.send(JSON.stringify({ type: 'chat', message: 'Hello!' }));
// };
// ws.onmessage = (event) => {
//     const data = JSON.parse(event.data);
//     console.log('Received:', data);
// };
```

---

## 35. How to implement pagination in Express?

**Answer:**
```javascript
// Basic pagination
app.get('/api/posts', async (req, res) => {
    try {
        const page = parseInt(req.query.page) || 1;
        const limit = parseInt(req.query.limit) || 10;
        const skip = (page - 1) * limit;
        
        const posts = await Post.find()
            .skip(skip)
            .limit(limit)
            .sort({ createdAt: -1 });
        
        const total = await Post.countDocuments();
        
        res.json({
            data: posts,
            pagination: {
                page,
                limit,
                total,
                pages: Math.ceil(total / limit),
                hasNext: page * limit < total,
                hasPrev: page > 1
            }
        });
    } catch (error) {
        res.status(500).json({ error: error.message });
    }
});

// Cursor-based pagination (better for real-time data)
app.get('/api/posts/cursor', async (req, res) => {
    const limit = parseInt(req.query.limit) || 10;
    const cursor = req.query.cursor;
    
    const query = cursor ? { _id: { $lt: cursor } } : {};
    
    const posts = await Post.find(query)
        .sort({ _id: -1 })
        .limit(limit + 1);
    
    const hasMore = posts.length > limit;
    const results = hasMore ? posts.slice(0, -1) : posts;
    
    res.json({
        data: results,
        cursor: hasMore ? results[results.length - 1]._id : null,
        hasMore
    });
});

// Pagination middleware
function paginate(model) {
    return async (req, res, next) => {
        const page = parseInt(req.query.page) || 1;
        const limit = parseInt(req.query.limit) || 10;
        const skip = (page - 1) * limit;
        
        try {
            const data = await model.find()
                .skip(skip)
                .limit(limit);
            
            const total = await model.countDocuments();
            
            res.paginatedResults = {
                data,
                pagination: {
                    page,
                    limit,
                    total,
                    pages: Math.ceil(total / limit)
                }
            };
            
            next();
        } catch (error) {
            res.status(500).json({ error: error.message });
        }
    };
}

// Use pagination middleware
app.get('/api/users', paginate(User), (req, res) => {
    res.json(res.paginatedResults);
});

// With search and filters
app.get('/api/products', async (req, res) => {
    const page = parseInt(req.query.page) || 1;
    const limit = parseInt(req.query.limit) || 10;
    const skip = (page - 1) * limit;
    
    // Build query
    const query = {};
    
    if (req.query.category) {
        query.category = req.query.category;
    }
    
    if (req.query.search) {
        query.$text = { $search: req.query.search };
    }
    
    if (req.query.minPrice || req.query.maxPrice) {
        query.price = {};
        if (req.query.minPrice) query.price.$gte = parseFloat(req.query.minPrice);
        if (req.query.maxPrice) query.price.$lte = parseFloat(req.query.maxPrice);
    }
    
    // Sort
    const sortBy = req.query.sortBy || 'createdAt';
    const sortOrder = req.query.sortOrder === 'asc' ? 1 : -1;
    
    const products = await Product.find(query)
        .skip(skip)
        .limit(limit)
        .sort({ [sortBy]: sortOrder });
    
    const total = await Product.countDocuments(query);
    
    res.json({
        data: products,
        pagination: {
            page,
            limit,
            total,
            pages: Math.ceil(total / limit)
        },
        filters: {
            category: req.query.category,
            search: req.query.search,
            minPrice: req.query.minPrice,
            maxPrice: req.query.maxPrice
        }
    });
});
```

---

# Practical Coding Questions

## 36. Build a REST API for a Blog

**Answer:**
```javascript
const express = require('express');
const app = express();

app.use(express.json());

// In-memory storage (use database in production)
let posts = [];
let currentId = 1;

// Get all posts
app.get('/api/posts', (req, res) => {
    res.json(posts);
});

// Get single post
app.get('/api/posts/:id', (req, res) => {
    const post = posts.find(p => p.id === parseInt(req.params.id));
    
    if (!post) {
        return res.status(404).json({ error: 'Post not found' });
    }
    
    res.json(post);
});

// Create post
app.post('/api/posts', (req, res) => {
    const { title, content, author } = req.body;
    
    if (!title || !content) {
        return res.status(400).json({ error: 'Title and content required' });
    }
    
    const post = {
        id: currentId++,
        title,
        content,
        author: author || 'Anonymous',
        createdAt: new Date(),
        updatedAt: new Date()
    };
    
    posts.push(post);
    res.status(201).json(post);
});

// Update post
app.put('/api/posts/:id', (req, res) => {
    const post = posts.find(p => p.id === parseInt(req.params.id));
    
    if (!post) {
        return res.status(404).json({ error: 'Post not found' });
    }
    
    const { title, content } = req.body;
    
    if (title) post.title = title;
    if (content) post.content = content;
    post.updatedAt = new Date();
    
    res.json(post);
});

// Delete post
app.delete('/api/posts/:id', (req, res) => {
    const index = posts.findIndex(p => p.id === parseInt(req.params.id));
    
    if (index === -1) {
        return res.status(404).json({ error: 'Post not found' });
    }
    
    posts.splice(index, 1);
    res.status(204).send();
});

app.listen(3000);
```

---

## 37. Implement Rate Limiting (Custom)

**Answer:**
```javascript
class RateLimiter {
    constructor(maxRequests, windowMs) {
        this.maxRequests = maxRequests;
        this.windowMs = windowMs;
        this.requests = new Map();
    }
    
    middleware() {
        return (req, res, next) => {
            const ip = req.ip;
            const now = Date.now();
            
            if (!this.requests.has(ip)) {
                this.requests.set(ip, []);
            }
            
            const userRequests = this.requests.get(ip);
            
            // Remove old requests
            const validRequests = userRequests.filter(
                timestamp => now - timestamp < this.windowMs
            );
            
            if (validRequests.length >= this.maxRequests) {
                return res.status(429).json({
                    error: 'Too many requests',
                    retryAfter: this.windowMs / 1000
                });
            }
            
            validRequests.push(now);
            this.requests.set(ip, validRequests);
            
            next();
        };
    }
}

// Usage
const limiter = new RateLimiter(5, 60000);  // 5 requests per minute
app.use(limiter.middleware());
```

---

## 38. Build File Upload with Progress

**Answer:**
```javascript
const express = require('express');
const multer = require('multer');
const app = express();

const storage = multer.diskStorage({
    destination: 'uploads/',
    filename: (req, file, cb) => {
        cb(null, Date.now() + '-' + file.originalname);
    }
});

const upload = multer({ storage });

// Track upload progress
app.post('/upload', upload.single('file'), (req, res) => {
    res.json({
        message: 'File uploaded',
        file: req.file
    });
});

// For progress, use streams
app.post('/upload-stream', (req, res) => {
    const busboy = require('busboy');
    const bb = busboy({ headers: req.headers });
    
    let fileSize = 0;
    let uploadedSize = 0;
    
    bb.on('file', (fieldname, file, info) => {
        const saveTo = path.join('uploads', info.filename);
        const writeStream = fs.createWriteStream(saveTo);
        
        file.on('data', (data) => {
            uploadedSize += data.length;
            const progress = (uploadedSize / fileSize) * 100;
            console.log(`Upload progress: ${progress.toFixed(2)}%`);
        });
        
        file.pipe(writeStream);
    });
    
    bb.on('finish', () => {
        res.json({ message: 'Upload complete' });
    });
    
    req.pipe(bb);
});
```

---

## Best Practices

1. **Use Environment Variables**
2. **Implement Logging** (Winston, Morgan)
3. **Error Handling** (Try-catch, error middleware)
4. **Input Validation** (express-validator, Joi)
5. **Security** (Helmet, CORS, Rate limiting)
6. **Testing** (Jest, Mocha, Supertest)
7. **Documentation** (Swagger/OpenAPI)
8. **Code Organization** (MVC, Clean Architecture)
9. **Performance** (Clustering, Caching, Compression)
10. **Monitoring** (PM2, New Relic, DataDog)

---

**Good luck with your interview, Ashish!** 🚀

Study these questions and practice implementing them. Be ready to explain the concepts and write code on a whiteboard or in a coding interview!
