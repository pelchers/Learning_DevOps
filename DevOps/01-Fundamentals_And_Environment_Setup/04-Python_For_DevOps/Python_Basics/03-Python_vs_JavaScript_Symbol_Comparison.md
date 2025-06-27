# 🐍↔️🟨 Python vs JavaScript Symbol Comparison for DevOps

## 📖 What This File Does
This guide compares special characters and symbols between Python and JavaScript, showing how the same operations are written differently in each language. Perfect for DevOps engineers who work with both languages.

## 🎯 Learning Objectives
- Understand symbol differences between Python and JavaScript
- Learn to translate DevOps scripts between languages
- Master equivalent operations in both languages
- Recognize common patterns and avoid common mistakes

## 📋 Prerequisites
- Basic understanding of both Python and JavaScript
- Familiarity with terminal symbols
- DevOps context understanding

---

## 🔤 **Symbol Comparison: Python vs JavaScript**

### **Variable Declaration and Assignment**

#### **Python vs JavaScript Variable Assignment**

**Python:**
```python
# Simple assignment (no declaration keyword needed)
server_name = "web-01"
port = 8080
is_running = True
servers = ["web1", "web2", "db1"]

# Multiple assignment
host, port, protocol = "localhost", 8080, "http"

# Environment variables
import os
database_url = os.environ.get("DATABASE_URL", "localhost:5432")
```

**JavaScript:**
```javascript
// Variable declaration with let/const/var
const serverName = "web-01";        // Immutable
let port = 8080;                    // Mutable
var isRunning = true;               // Old style (avoid)
const servers = ["web1", "web2", "db1"];

// Destructuring assignment
const [host, portNum, protocol] = ["localhost", 8080, "http"];

// Environment variables
const databaseUrl = process.env.DATABASE_URL || "localhost:5432";
```

**Key Differences:**
- **Python**: No declaration keywords, snake_case naming
- **JavaScript**: Requires `const`/`let`, camelCase naming

**📖 What This Does**
Variable declaration creates named storage for values. Python creates variables on assignment, while JavaScript requires explicit declaration keywords to define variable scope and mutability.

**🔧 Configuration Notes**
- Python: Use snake_case (server_name) for variable names
- JavaScript: Use camelCase (serverName) for variable names  
- JavaScript: Prefer `const` for values that don't change, `let` for changing values
- Both: Use descriptive names for DevOps scripts (backup_path vs bp)

### **Equality and Comparison**

#### **Python vs JavaScript Equality**

**Python:**
```python
# Single equality operator
if server_status == "running":
    print("Server is online")

# Type matters automatically
if port == 80:           # Number comparison
    protocol = "http"

if port == "80":         # String comparison
    print("Port is string 80")

# Identity comparison
if server1 is server2:   # Same object
    print("Same server object")
```

**JavaScript:**
```javascript
// Two equality operators
if (serverStatus === "running") {    // Strict equality (type + value)
    console.log("Server is online");
}

if (serverStatus == "running") {     // Loose equality (coercion)
    console.log("Server is online");
}

// Type coercion examples
if (port == 80) {        // True for both 80 and "80"
    protocol = "http";
}

if (port === 80) {       // True only for number 80
    protocol = "http";
}

// Object comparison
if (server1 === server2) {   // Same reference
    console.log("Same server object");
}
```

**Key Differences:**
- **Python**: One `==` operator, automatic type checking
- **JavaScript**: `==` (loose) vs `===` (strict), type coercion issues

**📖 What This Does**
Equality operators compare values to determine if they're the same. Python handles type checking automatically, while JavaScript offers both loose equality (with type conversion) and strict equality (exact match).

**🔧 Configuration Notes**
- Python: `==` is safe to use, handles types correctly
- JavaScript: Always use `===` for exact comparisons in DevOps scripts
- JavaScript: `==` can cause unexpected behavior (avoid in production code)
- Both: Use comparison operators for conditional logic in automation scripts

### **String Operations**

#### **Python vs JavaScript String Handling**

**Python:**
```python
# String quotes - flexible
server = 'web-01'
message = "Server's status"
multiline = """
Multi-line
string content
"""

# String formatting
server_name = "web-01"
cpu_usage = 75.5

# F-strings (Python 3.6+)
status = f"Server {server_name} CPU: {cpu_usage}%"

# .format() method
status = "Server {} CPU: {}%".format(server_name, cpu_usage)

# % formatting (old style)
status = "Server %s CPU: %.1f%%" % (server_name, cpu_usage)

# String concatenation
full_url = base_url + "/api" + "/v1"
```

**JavaScript:**
```javascript
// String quotes - flexible  
const server = 'web-01';
const message = "Server's status";
const multiline = `
Multi-line
string content
`;

// String formatting
const serverName = "web-01";
const cpuUsage = 75.5;

// Template literals (ES6+)
const status = `Server ${serverName} CPU: ${cpuUsage}%`;

// String concatenation
const status = "Server " + serverName + " CPU: " + cpuUsage + "%";

// Array join
const fullUrl = [baseUrl, "api", "v1"].join("/");
```

**Key Differences:**
- **Python**: F-strings `f""`, multiple formatting methods
- **JavaScript**: Template literals with backticks `` ` ` ``, `${}` for variables

**📖 What This Does**
String formatting embeds variables and expressions into text. Both languages provide modern syntax for creating dynamic strings without complex concatenation, essential for log messages, API URLs, and configuration.

**🔧 Configuration Notes**
- Python: F-strings require Python 3.6+, use for all new DevOps scripts
- JavaScript: Template literals require ES6+, supported in Node.js and modern browsers
- Both: Prefer modern formatting over concatenation for readability
- Both: Essential for creating dynamic file names, timestamps, and API endpoints

### **Lists/Arrays and Objects**

#### **Python Lists vs JavaScript Arrays**

**Python:**
```python
# List creation and operations
servers = ["web-01", "web-02", "db-01"]
servers.append("cache-01")           # Add to end
servers.insert(0, "load-balancer")  # Insert at index
first_server = servers[0]            # Access by index
last_server = servers[-1]           # Negative indexing

# List slicing
web_servers = servers[0:2]           # Slice notation
web_servers = servers[:2]            # From start
remaining = servers[2:]              # To end

# List comprehension
running_servers = [s for s in servers if s.startswith("web")]

# List methods
servers.remove("cache-01")           # Remove by value
popped = servers.pop()               # Remove and return last
servers.extend(["api-01", "api-02"]) # Add multiple
```

**JavaScript:**
```javascript
// Array creation and operations
const servers = ["web-01", "web-02", "db-01"];
servers.push("cache-01");            // Add to end
servers.unshift("load-balancer");    // Add to beginning
const firstServer = servers[0];      // Access by index
const lastServer = servers[servers.length - 1]; // No negative indexing

// Array slicing
const webServers = servers.slice(0, 2);     // Slice method
const remaining = servers.slice(2);         // From index to end

// Array methods (functional)
const runningServers = servers.filter(s => s.startsWith("web"));

// Array mutation methods
servers.splice(servers.indexOf("cache-01"), 1); // Remove by value
const popped = servers.pop();                   // Remove and return last
servers.push(...["api-01", "api-02"]);         // Spread operator
```

**Key Differences:**
- **Python**: Negative indexing, slice notation `[:]`, list comprehensions
- **JavaScript**: No negative indexing, `.slice()` method, array methods, spread operator

**📖 What This Does**
Lists/Arrays store ordered collections of items like server names, ports, or configuration values. Both languages provide ways to access, modify, and process these collections, but with different syntax and capabilities.

**🔧 Configuration Notes**
- Python: Use negative indexing `[-1]` for last item, slice notation `[start:end]`
- JavaScript: Use `.length - 1` for last item, `.slice(start, end)` method
- Python: List comprehensions are powerful for filtering/transforming data
- JavaScript: Array methods like `.filter()`, `.map()` provide functional programming style
- Both: Essential for managing server lists, configuration arrays, and batch operations

#### **Python Dictionaries vs JavaScript Objects**

**Python:**
```python
# Dictionary creation
server_config = {
    "hostname": "web-01",
    "ip": "192.168.1.10",
    "port": 80,
    "status": "running"
}

# Access values
hostname = server_config["hostname"]         # Bracket notation
ip = server_config.get("ip", "unknown")     # Get with default

# Add/modify values
server_config["environment"] = "production"
server_config.update({"region": "us-east-1"})

# Dictionary methods
keys = server_config.keys()
values = server_config.values()
items = server_config.items()

# Dictionary comprehension
port_map = {k: v for k, v in server_config.items() if k.endswith("port")}
```

**JavaScript:**
```javascript
// Object creation
const serverConfig = {
    hostname: "web-01",              // No quotes needed for simple keys
    ip: "192.168.1.10",
    port: 80,
    status: "running"
};

// Access values
const hostname = serverConfig.hostname;      // Dot notation
const hostname2 = serverConfig["hostname"];  // Bracket notation
const ip = serverConfig.ip || "unknown";    // Logical OR for default

// Add/modify values
serverConfig.environment = "production";
Object.assign(serverConfig, {region: "us-east-1"});

// Object methods
const keys = Object.keys(serverConfig);
const values = Object.values(serverConfig);
const entries = Object.entries(serverConfig);

// Object manipulation
const portMap = Object.fromEntries(
    Object.entries(serverConfig).filter(([k, v]) => k.endsWith("port"))
);
```

**Key Differences:**
- **Python**: Always use quotes for keys, `.get()` method, dictionary comprehensions
- **JavaScript**: Optional quotes for keys, dot notation, `Object.*` methods

**📖 What This Does**  
Dictionaries (Python) and Objects (JavaScript) store key-value pairs for configuration data, server information, and structured data. They're essential for managing configuration files, API responses, and complex data structures in DevOps automation.

**🔧 Configuration Notes**
- Python: Use `dict.get(key, default)` for safe value access
- JavaScript: Use `obj.key || default` or `obj?.key` for safe access
- Python: Dictionary comprehensions are powerful for data transformation
- JavaScript: Object destructuring and spread operator provide flexible manipulation
- Both: Perfect for configuration management, API payloads, and structured logging
- Both: JSON serialization/deserialization works naturally with both

### **Functions and Methods**

#### **Python vs JavaScript Function Definitions**

**Python:**
```python
# Function definition
def restart_service(service_name, timeout=30):
    """Restart a system service with timeout"""
    import subprocess
    try:
        result = subprocess.run(
            ["systemctl", "restart", service_name],
            timeout=timeout,
            check=True
        )
        return True
    except subprocess.CalledProcessError:
        return False

# Lambda functions
servers = ["web-01", "web-02", "db-01"]
web_servers = list(filter(lambda x: x.startswith("web"), servers))

# Class methods
class ServerManager:
    def __init__(self, servers):
        self.servers = servers
    
    def restart_all(self):
        for server in self.servers:
            self.restart_server(server)
```

**JavaScript:**
```javascript
// Function declaration
function restartService(serviceName, timeout = 30000) {
    const { execSync } = require('child_process');
    try {
        execSync(`systemctl restart ${serviceName}`, {
            timeout: timeout
        });
        return true;
    } catch (error) {
        return false;
    }
}

// Arrow functions
const servers = ["web-01", "web-02", "db-01"];
const webServers = servers.filter(x => x.startsWith("web"));

// Function expressions
const restartServiceAsync = async (serviceName) => {
    // Async implementation
};

// Class methods
class ServerManager {
    constructor(servers) {
        this.servers = servers;
    }
    
    restartAll() {
        for (const server of this.servers) {
            this.restartServer(server);
        }
    }
}
```

**Key Differences:**
- **Python**: `def` keyword, `self` parameter, docstrings
- **JavaScript**: `function` keyword or arrow functions, `this` keyword, JSDoc comments

**📖 What This Does**  
Functions encapsulate reusable code logic for DevOps automation tasks like server management, deployment processes, and monitoring operations. Both languages support different function syntaxes but serve the same purpose of organizing and reusing code.

**🔧 Configuration Notes**
- Python: Use docstrings for function documentation, `def` for all functions
- JavaScript: Arrow functions `=>` for simple operations, regular functions for methods
- Python: `self` parameter required for class methods
- JavaScript: `this` context can change, arrow functions preserve outer context
- Both: Essential for modular DevOps scripts and automation workflows
- Both: Support default parameters, variable arguments, and return values

### **Error Handling**

#### **Python vs JavaScript Exception Handling**

**Python:**
```python
# Try/except/finally
try:
    import requests
    response = requests.get("https://api.example.com/status")
    data = response.json()
    
    if response.status_code != 200:
        raise Exception(f"API returned {response.status_code}")
        
except requests.exceptions.RequestException as e:
    print(f"Network error: {e}")
    data = None
except ValueError as e:
    print(f"JSON parsing error: {e}")
    data = None
except Exception as e:
    print(f"Unexpected error: {e}")
    data = None
finally:
    print("Request attempt completed")

# Custom exceptions
class DeploymentError(Exception):
    def __init__(self, message, exit_code):
        super().__init__(message)
        self.exit_code = exit_code

try:
    deploy_application()
except DeploymentError as e:
    print(f"Deployment failed: {e}, exit code: {e.exit_code}")
```

**JavaScript:**
```javascript
// Try/catch/finally
try {
    const axios = require('axios');
    const response = await axios.get('https://api.example.com/status');
    
    if (response.status !== 200) {
        throw new Error(`API returned ${response.status}`);
    }
    
    const data = response.data;
} catch (error) {
    if (error.code === 'ECONNREFUSED') {
        console.log(`Network error: ${error.message}`);
    } else if (error instanceof SyntaxError) {
        console.log(`JSON parsing error: ${error.message}`);
    } else {
        console.log(`Unexpected error: ${error.message}`);
    }
    data = null;
} finally {
    console.log('Request attempt completed');
}

// Custom errors
class DeploymentError extends Error {
    constructor(message, exitCode) {
        super(message);
        this.name = 'DeploymentError';
        this.exitCode = exitCode;
    }
}

try {
    await deployApplication();
} catch (error) {
    if (error instanceof DeploymentError) {
        console.log(`Deployment failed: ${error.message}, exit code: ${error.exitCode}`);
    }
}
```

**Key Differences:**
- **Python**: `except` with specific exception types, multiple except blocks
- **JavaScript**: Single `catch` block, check error types manually, `instanceof`

**📖 What This Does**  
Error handling manages failures and unexpected conditions in DevOps automation scripts. Both languages provide try/catch mechanisms to gracefully handle errors like network failures, file operations, and deployment issues without crashing the entire script.

**🔧 Configuration Notes**
- Python: Use specific exception types for different error conditions
- JavaScript: Check error properties and types manually in catch blocks
- Python: Multiple `except` blocks handle different error types separately
- JavaScript: Single `catch` block requires manual error type checking
- Both: Always use `finally` for cleanup operations (close files, connections)
- Both: Essential for robust deployment scripts and monitoring automation
- Both: Log errors appropriately for debugging and monitoring

### **Asynchronous Operations**

#### **Python vs JavaScript Async/Await**

**Python:**
```python
import asyncio
import aiohttp

# Async function definition
async def check_server_status(url):
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as response:
            return {
                "url": url,
                "status": response.status,
                "response_time": response.headers.get("response-time")
            }

# Multiple async operations
async def check_all_servers(urls):
    tasks = [check_server_status(url) for url in urls]
    results = await asyncio.gather(*tasks)
    return results

# Running async functions
if __name__ == "__main__":
    urls = ["http://web-01:8080", "http://web-02:8080"]
    results = asyncio.run(check_all_servers(urls))
    
    for result in results:
        print(f"{result['url']}: {result['status']}")
```

**JavaScript:**
```javascript
const axios = require('axios');

// Async function definition
async function checkServerStatus(url) {
    try {
        const response = await axios.get(url);
        return {
            url: url,
            status: response.status,
            responseTime: response.headers['response-time']
        };
    } catch (error) {
        return {
            url: url,
            status: 'error',
            error: error.message
        };
    }
}

// Multiple async operations
async function checkAllServers(urls) {
    const promises = urls.map(url => checkServerStatus(url));
    const results = await Promise.all(promises);
    return results;
}

// Running async functions
(async () => {
    const urls = ["http://web-01:8080", "http://web-02:8080"];
    const results = await checkAllServers(urls);
    
    results.forEach(result => {
        console.log(`${result.url}: ${result.status}`);
    });
})();
```

**Key Differences:**
- **Python**: `asyncio.gather()`, `asyncio.run()`, context managers
- **JavaScript**: `Promise.all()`, immediate async execution, built-in async support

**📖 What This Does**  
Asynchronous operations allow DevOps scripts to perform multiple tasks concurrently, like checking multiple servers, making parallel API calls, or handling multiple deployment processes simultaneously without blocking execution.

**🔧 Configuration Notes**
- Python: Requires `asyncio` module, use `await` with async functions
- JavaScript: Built-in async support, Promises are native
- Python: `asyncio.gather()` for parallel execution of multiple async tasks
- JavaScript: `Promise.all()` for parallel execution, `.map()` creates promise arrays
- Both: Essential for scalable DevOps automation and monitoring
- Both: Use for I/O operations: API calls, file operations, database queries
- Both: Improves performance when dealing with multiple servers or services

### **File Operations**

#### **Python vs JavaScript File Handling**

**Python:**
```python
import os
import json

# File reading/writing
def read_config_file(file_path):
    try:
        with open(file_path, 'r') as file:
            config = json.load(file)
        return config
    except FileNotFoundError:
        return {}
    except json.JSONDecodeError:
        return {}

def write_config_file(file_path, config):
    os.makedirs(os.path.dirname(file_path), exist_ok=True)
    with open(file_path, 'w') as file:
        json.dump(config, file, indent=2)

# File operations
config_path = "/etc/myapp/config.json"
config = read_config_file(config_path)
config["last_updated"] = "2024-01-15"
write_config_file(config_path, config)

# Directory operations
log_dir = "/var/log/myapp"
if not os.path.exists(log_dir):
    os.makedirs(log_dir)

# List files
log_files = [f for f in os.listdir(log_dir) if f.endswith('.log')]
```

**JavaScript:**
```javascript
const fs = require('fs').promises;
const path = require('path');

// File reading/writing
async function readConfigFile(filePath) {
    try {
        const content = await fs.readFile(filePath, 'utf8');
        return JSON.parse(content);
    } catch (error) {
        if (error.code === 'ENOENT') {
            return {};
        }
        throw error;
    }
}

async function writeConfigFile(filePath, config) {
    await fs.mkdir(path.dirname(filePath), { recursive: true });
    await fs.writeFile(filePath, JSON.stringify(config, null, 2));
}

// File operations
const configPath = "/etc/myapp/config.json";
const config = await readConfigFile(configPath);
config.lastUpdated = "2024-01-15";
await writeConfigFile(configPath, config);

// Directory operations
const logDir = "/var/log/myapp";
try {
    await fs.mkdir(logDir, { recursive: true });
} catch (error) {
    if (error.code !== 'EEXIST') throw error;
}

// List files
const allFiles = await fs.readdir(logDir);
const logFiles = allFiles.filter(f => f.endsWith('.log'));
```

**Key Differences:**
- **Python**: Context managers (`with`), synchronous by default
- **JavaScript**: Promises/async, `.promises` API, error code checking

**📖 What This Does**  
File operations manage configuration files, logs, deployment artifacts, and other file-based data in DevOps workflows. Both languages provide ways to read, write, and manipulate files, but with different approaches to error handling and async operations.

**🔧 Configuration Notes**
- Python: Use `with open()` for automatic file closing and error handling
- JavaScript: Use `fs.promises` for async file operations in Node.js
- Python: Synchronous by default, use `aiofiles` for async file operations
- JavaScript: Async by default in modern Node.js, better for I/O heavy operations
- Both: Essential for configuration management, log processing, and artifact handling
- Both: Always handle file not found and permission errors gracefully
- Both: Use JSON parsing for configuration files and API data

### **Environment Variables and System Commands**

#### **Python vs JavaScript System Integration**

**Python:**
```python
import os
import subprocess
from pathlib import Path

# Environment variables
database_url = os.environ.get("DATABASE_URL", "localhost:5432")
environment = os.getenv("ENVIRONMENT", "development")

# Set environment variables
os.environ["BACKUP_DIR"] = "/var/backups"

# System commands
def run_command(command, shell=False):
    try:
        result = subprocess.run(
            command if shell else command.split(),
            capture_output=True,
            text=True,
            check=True,
            shell=shell
        )
        return {
            "success": True,
            "stdout": result.stdout,
            "stderr": result.stderr,
            "exit_code": result.returncode
        }
    except subprocess.CalledProcessError as e:
        return {
            "success": False,
            "stdout": e.stdout,
            "stderr": e.stderr,
            "exit_code": e.returncode
        }

# Usage examples
result = run_command("systemctl status nginx")
if result["success"]:
    print("Nginx is running")

# Path operations
config_path = Path("/etc") / "nginx" / "nginx.conf"
if config_path.exists():
    content = config_path.read_text()
```

**JavaScript:**
```javascript
const { spawn, exec } = require('child_process');
const { promisify } = require('util');
const execAsync = promisify(exec);

// Environment variables
const databaseUrl = process.env.DATABASE_URL || "localhost:5432";
const environment = process.env.ENVIRONMENT || "development";

// Set environment variables
process.env.BACKUP_DIR = "/var/backups";

// System commands
async function runCommand(command) {
    try {
        const { stdout, stderr } = await execAsync(command);
        return {
            success: true,
            stdout: stdout,
            stderr: stderr,
            exitCode: 0
        };
    } catch (error) {
        return {
            success: false,
            stdout: error.stdout || "",
            stderr: error.stderr || "",
            exitCode: error.code
        };
    }
}

// Usage examples
const result = await runCommand("systemctl status nginx");
if (result.success) {
    console.log("Nginx is running");
}

// Path operations
const path = require('path');
const fs = require('fs').promises;

const configPath = path.join("/etc", "nginx", "nginx.conf");
try {
    const content = await fs.readFile(configPath, 'utf8');
} catch (error) {
    console.log("Config file not found");
}
```

**Key Differences:**
- **Python**: `os.environ`, `subprocess` module, `pathlib` for paths
- **JavaScript**: `process.env`, `child_process` module, `path` module

**📖 What This Does**  
Environment variables and system commands integrate DevOps scripts with the operating system, allowing access to configuration settings, execution of shell commands, and interaction with system services and infrastructure tools.

**🔧 Configuration Notes**
- Python: `os.environ.get('VAR', 'default')` for safe environment variable access
- JavaScript: `process.env.VAR || 'default'` for environment variables with defaults
- Python: `subprocess.run()` provides comprehensive command execution control
- JavaScript: `child_process.exec()` for simple commands, `spawn()` for complex operations
- Both: Essential for deployment scripts, system administration, and infrastructure automation
- Both: Always validate and sanitize command inputs to prevent security issues
- Both: Use environment variables for configuration instead of hardcoding values

---

## 🎯 **DevOps Script Translation Examples**

### **Server Health Check Script**

**Python Version:**
```python
#!/usr/bin/env python3
import requests
import subprocess
import json
from datetime import datetime

def check_server_health():
    """Check server health and return status"""
    health_data = {
        "timestamp": datetime.now().isoformat(),
        "services": {},
        "system": {}
    }
    
    # Check services
    services = ["nginx", "postgresql", "redis"]
    for service in services:
        try:
            result = subprocess.run(
                ["systemctl", "is-active", service],
                capture_output=True,
                text=True
            )
            health_data["services"][service] = result.stdout.strip()
        except Exception as e:
            health_data["services"][service] = "error"
    
    # Check API endpoint
    try:
        response = requests.get("http://localhost:8080/health", timeout=5)
        health_data["api"] = {
            "status": response.status_code,
            "response_time": response.elapsed.total_seconds()
        }
    except Exception as e:
        health_data["api"] = {"status": "error", "error": str(e)}
    
    return health_data

if __name__ == "__main__":
    health = check_server_health()
    print(json.dumps(health, indent=2))
```

**JavaScript Version:**
```javascript
#!/usr/bin/env node
const { exec } = require('child_process');
const { promisify } = require('util');
const axios = require('axios');

const execAsync = promisify(exec);

async function checkServerHealth() {
    const healthData = {
        timestamp: new Date().toISOString(),
        services: {},
        system: {}
    };
    
    // Check services
    const services = ["nginx", "postgresql", "redis"];
    for (const service of services) {
        try {
            const { stdout } = await execAsync(`systemctl is-active ${service}`);
            healthData.services[service] = stdout.trim();
        } catch (error) {
            healthData.services[service] = "error";
        }
    }
    
    // Check API endpoint
    try {
        const startTime = Date.now();
        const response = await axios.get("http://localhost:8080/health", {
            timeout: 5000
        });
        const responseTime = (Date.now() - startTime) / 1000;
        
        healthData.api = {
            status: response.status,
            responseTime: responseTime
        };
    } catch (error) {
        healthData.api = {
            status: "error",
            error: error.message
        };
    }
    
    return healthData;
}

(async () => {
    const health = await checkServerHealth();
    console.log(JSON.stringify(health, null, 2));
})();
```

### **Deployment Script**

**Python Version:**
```python
#!/usr/bin/env python3
import os
import sys
import subprocess
import shutil
from pathlib import Path

class Deployer:
    def __init__(self, app_name, environment):
        self.app_name = app_name
        self.environment = environment
        self.deploy_dir = Path(f"/opt/{app_name}")
        self.backup_dir = Path(f"/var/backups/{app_name}")
    
    def backup_current(self):
        """Backup current deployment"""
        if self.deploy_dir.exists():
            timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
            backup_path = self.backup_dir / f"backup_{timestamp}"
            backup_path.parent.mkdir(parents=True, exist_ok=True)
            shutil.copytree(self.deploy_dir, backup_path)
            print(f"Backup created: {backup_path}")
    
    def deploy(self, artifact_path):
        """Deploy new version"""
        try:
            # Backup current version
            self.backup_current()
            
            # Extract new version
            subprocess.run(["tar", "-xzf", artifact_path, "-C", str(self.deploy_dir)], check=True)
            
            # Install dependencies
            subprocess.run(["pip", "install", "-r", "requirements.txt"], 
                         cwd=self.deploy_dir, check=True)
            
            # Restart services
            subprocess.run(["systemctl", "restart", self.app_name], check=True)
            
            print(f"Deployment successful for {self.app_name}")
            return True
            
        except Exception as e:
            print(f"Deployment failed: {e}")
            return False

if __name__ == "__main__":
    if len(sys.argv) != 4:
        print("Usage: deploy.py <app_name> <environment> <artifact_path>")
        sys.exit(1)
    
    deployer = Deployer(sys.argv[1], sys.argv[2])
    success = deployer.deploy(sys.argv[3])
    sys.exit(0 if success else 1)
```

**JavaScript Version:**
```javascript
#!/usr/bin/env node
const fs = require('fs').promises;
const path = require('path');
const { exec } = require('child_process');
const { promisify } = require('util');

const execAsync = promisify(exec);

class Deployer {
    constructor(appName, environment) {
        this.appName = appName;
        this.environment = environment;
        this.deployDir = `/opt/${appName}`;
        this.backupDir = `/var/backups/${appName}`;
    }
    
    async backupCurrent() {
        try {
            await fs.access(this.deployDir);
            const timestamp = new Date().toISOString().replace(/[:.]/g, '-');
            const backupPath = path.join(this.backupDir, `backup_${timestamp}`);
            
            await fs.mkdir(path.dirname(backupPath), { recursive: true });
            await execAsync(`cp -r ${this.deployDir} ${backupPath}`);
            console.log(`Backup created: ${backupPath}`);
        } catch (error) {
            console.log("No existing deployment to backup");
        }
    }
    
    async deploy(artifactPath) {
        try {
            // Backup current version
            await this.backupCurrent();
            
            // Extract new version
            await execAsync(`tar -xzf ${artifactPath} -C ${this.deployDir}`);
            
            // Install dependencies
            await execAsync("npm install", { cwd: this.deployDir });
            
            // Restart services
            await execAsync(`systemctl restart ${this.appName}`);
            
            console.log(`Deployment successful for ${this.appName}`);
            return true;
            
        } catch (error) {
            console.log(`Deployment failed: ${error.message}`);
            return false;
        }
    }
}

(async () => {
    if (process.argv.length !== 5) {
        console.log("Usage: node deploy.js <app_name> <environment> <artifact_path>");
        process.exit(1);
    }
    
    const [,, appName, environment, artifactPath] = process.argv;
    const deployer = new Deployer(appName, environment);
    const success = await deployer.deploy(artifactPath);
    process.exit(success ? 0 : 1);
})();
```

---

## 🎯 **Quick Translation Reference**

| **Operation** | **Python** | **JavaScript** |
|---------------|------------|----------------|
| **Variable Declaration** | `name = "value"` | `const name = "value";` |
| **String Formatting** | `f"Hello {name}"` | `` `Hello ${name}` `` |
| **Array/List** | `[1, 2, 3]` | `[1, 2, 3]` |
| **Dictionary/Object** | `{"key": "value"}` | `{key: "value"}` |
| **Function** | `def func():` | `function func() {}` |
| **Async Function** | `async def func():` | `async function func() {}` |
| **Exception Handling** | `try/except/finally` | `try/catch/finally` |
| **Import/Require** | `import module` | `const module = require()` |
| **Environment Variable** | `os.environ["VAR"]` | `process.env.VAR` |
| **Command Execution** | `subprocess.run()` | `child_process.exec()` |
| **File Operations** | `open(file)` | `fs.readFile()` |
| **JSON Parse** | `json.loads()` | `JSON.parse()` |
| **JSON Stringify** | `json.dumps()` | `JSON.stringify()` |

---

## 💡 **Common Migration Pitfalls**

### **Python to JavaScript**
1. **Indentation vs Braces**: Python uses indentation, JavaScript uses `{}`
2. **Variable Declaration**: JavaScript requires `const`/`let`
3. **Equality**: Python `==` vs JavaScript `===`
4. **String Formatting**: Python f-strings vs JavaScript template literals
5. **Exception Types**: Python specific exceptions vs JavaScript generic Error

### **JavaScript to Python**
1. **Semicolons**: Not required in Python
2. **CamelCase vs snake_case**: Different naming conventions
3. **Array Methods**: Different method names and behaviors
4. **Async/Await**: Different module systems and syntax
5. **Type Coercion**: Python is stricter about types

---

📄 **File Path:** `/DevOps/01-Fundamentals_And_Environment_Setup/04-Python_For_DevOps/Python_Basics/03-Python_vs_JavaScript_Symbol_Comparison.md` 