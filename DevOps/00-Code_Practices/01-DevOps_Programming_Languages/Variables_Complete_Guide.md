# 🔧 Variables Complete Guide for DevOps

A comprehensive guide to understanding variables across all programming languages used in DevOps environments.

---

## 📚 Table of Contents

1. [What Are Variables?](#what-are-variables)
2. [Variable Declaration & Assignment](#variable-declaration--assignment)
3. [Variable Types & Type Systems](#variable-types--type-systems)
4. [Variable Scope & Lifetime](#variable-scope--lifetime)
5. [Variable Naming Conventions](#variable-naming-conventions)
6. [Memory Management & Variables](#memory-management--variables)
7. [Variable Best Practices](#variable-best-practices)
8. [Language-Specific Deep Dives](#language-specific-deep-dives)
9. [DevOps-Specific Use Cases](#devops-specific-use-cases)
10. [Common Pitfalls & Troubleshooting](#common-pitfalls--troubleshooting)

---

## 🎯 What Are Variables?

**Variables** are named containers that store data values in computer memory. Think of them as labeled boxes where you can put information that your program needs to remember and use later.

### **Core Concepts:**

- **Storage**: Variables hold data in memory
- **Reference**: Variables provide a name to access stored data
- **Mutability**: Some variables can change, others cannot
- **Lifetime**: Variables exist for a certain period during program execution
- **Scope**: Variables are accessible in certain parts of your code

### **Real-World Analogy:**
Variables are like labeled storage containers in a warehouse:
- The **label** is the variable name (`serverName`)
- The **container** is the memory location
- The **contents** are the data value (`"web-01"`)
- The **warehouse section** is the scope (where you can access it)

---

## 📝 Variable Declaration & Assignment

### **Declaration vs Assignment vs Initialization**

```python
# Python Examples
server_name           # ❌ Error: using undeclared variable
server_name = None    # ✅ Declaration + Assignment (initialization)
server_name = "web-01" # ✅ Assignment to existing variable
```

```javascript
// JavaScript Examples
let serverName;              // ✅ Declaration only (undefined)
serverName = "web-01";       // ✅ Assignment to declared variable
let port = 8080;             // ✅ Declaration + Assignment (initialization)
const apiKey = "secret";     // ✅ Declaration + Assignment (required for const)
```

```go
// Go Examples
var serverName string        // ✅ Declaration (zero value: "")
serverName = "web-01"        // ✅ Assignment
var port int = 8080          // ✅ Declaration + Assignment
apiKey := "secret"           // ✅ Short declaration + Assignment
```

### **Declaration Patterns by Language**

| Language | Declaration | Assignment | Comments |
|----------|-------------|------------|----------|
| Python | `variable_name` | `= value` | Dynamic typing, no declaration keyword |
| JavaScript | `let/const/var name` | `= value` | Explicit declaration keywords |
| Bash | `VARIABLE_NAME` | `="value"` | No declaration keyword, all strings |
| Go | `var name type` | `= value` | Static typing, explicit types |
| YAML | `key:` | `value` | Key-value pairs, no declaration |

---

## 🏷️ Variable Types & Type Systems

### **Static vs Dynamic Typing**

**Static Typing** (Go, Java, C++):
- Types are checked at compile time
- Variables must be declared with specific types
- Type errors caught before runtime
- Better performance and IDE support

**Dynamic Typing** (Python, JavaScript):
- Types are checked at runtime
- Variables can hold any type of value
- More flexibility but potential runtime errors
- Faster development but requires more testing

### **Type Categories**

#### **Primitive Types**
```python
# Python - Dynamic typing
number = 42                    # int
price = 19.99                  # float
name = "DevOps"                # str
is_active = True               # bool
nothing = None                 # NoneType
```

```javascript
// JavaScript - Dynamic typing
let number = 42;               // number
let price = 19.99;             // number
let name = "DevOps";           // string
let isActive = true;           // boolean
let nothing = null;            // object (null)
let undefined_var;             // undefined
```

```go
// Go - Static typing
var number int = 42            // int
var price float64 = 19.99      // float64
var name string = "DevOps"     // string
var isActive bool = true       // bool
var nothing *string = nil      // pointer (nullable)
```

#### **Collection Types - Arrays vs Objects Deep Dive**

### **🔢 Arrays/Lists - Ordered Collections**

**When to Use Arrays/Lists:**
- Storing multiple items of the same type
- Order matters (first server, second server, etc.)
- Need to iterate through items
- Numerical indexing makes sense
- Unknown number of items

```python
# Python - Lists for ordered collections
server_names = ["web-01", "web-02", "web-03"]        # Server hostnames
port_numbers = [8080, 8081, 8082]                    # Corresponding ports
deployment_steps = [                                  # Ordered process
    "pull_code",
    "build_image", 
    "run_tests",
    "deploy_staging",
    "deploy_production"
]

# DevOps Use Cases for Lists:
backup_files = [                                      # File processing order
    "/backup/db_20231201.sql",
    "/backup/db_20231202.sql", 
    "/backup/db_20231203.sql"
]

log_levels = ["DEBUG", "INFO", "WARNING", "ERROR"]   # Priority order matters
```

```javascript
// JavaScript - Arrays for ordered collections
const serverNames = ["web-01", "web-02", "web-03"];
const portNumbers = [8080, 8081, 8082];
const deploymentSteps = [
    "pull_code",
    "build_image",
    "run_tests", 
    "deploy_staging",
    "deploy_production"
];

// DevOps Use Cases for Arrays:
const environments = ["dev", "staging", "prod"];     // Deployment pipeline order
const healthCheckUrls = [                            // Check in sequence
    "http://web-01:8080/health",
    "http://web-02:8080/health",
    "http://web-03:8080/health"
];
```

### **🗂️ Objects/Dictionaries - Key-Value Collections**

**When to Use Objects/Dictionaries:**
- Storing related properties about one thing
- Need named access (not numeric)
- Different data types for different properties
- Configuration and settings
- Lookup tables and mappings

```python
# Python - Dictionaries for structured data
server_config = {                                    # Single server's config
    "hostname": "web-01",
    "ip_address": "192.168.1.10",
    "port": 8080,
    "ssl_enabled": True,
    "cpu_cores": 4,
    "memory_gb": 16
}

# DevOps Use Cases for Dictionaries:
environment_vars = {                                 # Configuration mapping
    "DATABASE_URL": "postgresql://localhost:5432/app",
    "REDIS_URL": "redis://localhost:6379",
    "LOG_LEVEL": "INFO",
    "DEBUG": False
}

service_ports = {                                    # Service lookup
    "web": 8080,
    "api": 3000,
    "database": 5432,
    "redis": 6379,
    "nginx": 80
}

deployment_config = {                                # Complex configuration
    "image": "myapp:1.2.3",
    "replicas": 3,
    "resources": {
        "cpu": "500m",
        "memory": "1Gi"
    },
    "env_vars": environment_vars,
    "volumes": ["/data", "/logs"]                    # Mixed: object contains array
}
```

```javascript
// JavaScript - Objects for structured data
const serverConfig = {
    hostname: "web-01",
    ipAddress: "192.168.1.10", 
    port: 8080,
    sslEnabled: true,
    cpuCores: 4,
    memoryGb: 16
};

// DevOps Use Cases for Objects:
const environmentVars = {
    DATABASE_URL: "postgresql://localhost:5432/app",
    REDIS_URL: "redis://localhost:6379",
    LOG_LEVEL: "INFO",
    DEBUG: false
};

const servicePorts = {
    web: 8080,
    api: 3000,
    database: 5432,
    redis: 6379,
    nginx: 80
};
```

### **🔄 Choosing Collection vs Singular**

#### **Use Collections When:**
```python
# Multiple related items
servers = ["web-01", "web-02", "web-03"]            # ✅ Multiple servers
user_roles = ["admin", "editor", "viewer"]          # ✅ Multiple roles
backup_times = ["02:00", "14:00", "22:00"]          # ✅ Multiple backup times

# NOT for single items with multiple properties
server = {                                           # ✅ Single server with properties
    "name": "web-01",
    "roles": ["web", "api"],                        # ✅ This server's multiple roles
    "backup_time": "02:00"                          # ✅ This server's backup time
}
```

#### **Use Singular When:**
```python
# Single configuration object
database_config = {                                 # ✅ One database configuration
    "host": "localhost",
    "port": 5432,
    "name": "myapp",
    "ssl": True
}

# NOT multiple unrelated singles
# database_host = "localhost"                       # ❌ Related data split apart
# database_port = 5432                              # ❌ Hard to manage
# database_name = "myapp"                           # ❌ No clear relationship
# database_ssl = True
```

### **🏗️ Nested Collections - Complex Data Structures**

```python
# Python - Complex nested structures for DevOps
infrastructure = {
    "environments": {                                # Object of objects
        "production": {
            "servers": [                             # Array within object
                {
                    "name": "prod-web-01",
                    "services": ["nginx", "app"],    # Array within object within array
                    "monitoring": {
                        "enabled": True,
                        "alerts": ["cpu", "memory", "disk"]  # Array within object within object
                    }
                },
                {
                    "name": "prod-web-02", 
                    "services": ["nginx", "app"],
                    "monitoring": {
                        "enabled": True,
                        "alerts": ["cpu", "memory"]
                    }
                }
            ],
            "load_balancer": {
                "type": "nginx",
                "upstream_servers": ["prod-web-01:8080", "prod-web-02:8080"]
            }
        },
        "staging": {
            "servers": [
                {
                    "name": "staging-web-01",
                    "services": ["nginx", "app"]
                }
            ]
        }
    },
    "global_config": {
        "backup_schedule": ["02:00", "14:00"],
        "log_retention_days": 30,
        "monitoring_endpoints": [
            "https://prod-web-01/health",
            "https://prod-web-02/health"
        ]
    }
}

# Accessing nested data
prod_servers = infrastructure["environments"]["production"]["servers"]
first_server_name = prod_servers[0]["name"]                    # "prod-web-01"
first_server_alerts = prod_servers[0]["monitoring"]["alerts"]  # ["cpu", "memory", "disk"]
```

```yaml
# YAML - Natural nested structure representation
infrastructure:
  environments:
    production:
      servers:
        - name: prod-web-01
          services: [nginx, app]
          monitoring:
            enabled: true
            alerts: [cpu, memory, disk]
        - name: prod-web-02
          services: [nginx, app] 
          monitoring:
            enabled: true
            alerts: [cpu, memory]
      load_balancer:
        type: nginx
        upstream_servers:
          - prod-web-01:8080
          - prod-web-02:8080
    staging:
      servers:
        - name: staging-web-01
          services: [nginx, app]
  global_config:
    backup_schedule: ["02:00", "14:00"]
    log_retention_days: 30
    monitoring_endpoints:
      - https://prod-web-01/health
      - https://prod-web-02/health
```

### **🔍 Advanced Collection Types**

#### **Sets - Unique Collections**
```python
# Python - Sets for unique items
installed_packages = {"nginx", "docker", "git", "python3"}
required_packages = {"nginx", "docker", "nodejs", "python3"}

# Set operations for DevOps
missing_packages = required_packages - installed_packages    # {"nodejs"}
common_packages = installed_packages & required_packages    # {"nginx", "docker", "python3"}
all_packages = installed_packages | required_packages       # Union of all

# Use cases:
unique_error_codes = {404, 500, 502, 503}                  # No duplicates
server_roles = {"web", "database", "cache"}                # Unique roles
```

```javascript
// JavaScript - Sets for unique items
const installedPackages = new Set(["nginx", "docker", "git", "python3"]);
const requiredPackages = new Set(["nginx", "docker", "nodejs", "python3"]);

// Set operations
const missingPackages = new Set([...requiredPackages].filter(pkg => !installedPackages.has(pkg)));
const commonPackages = new Set([...installedPackages].filter(pkg => requiredPackages.has(pkg)));
```

#### **Maps - Advanced Key-Value**
```javascript
// JavaScript - Maps vs Objects
const serviceConfig = new Map([               // Map: any key type
    ["web", {port: 8080, replicas: 3}],
    ["api", {port: 3000, replicas: 2}],
    [404, "Not Found Error Page"]             // Number key (not possible with objects)
]);

const serviceObject = {                       // Object: string keys only
    web: {port: 8080, replicas: 3},
    api: {port: 3000, replicas: 2}
    // 404: "error"                          // Would become "404" (string)
};

// Map advantages:
serviceConfig.size;                           // Built-in size
serviceConfig.has("web");                     // Built-in existence check
for (const [service, config] of serviceConfig) {  // Easy iteration
    console.log(`${service}: ${config.port}`);
}
```

#### **Tuples - Immutable Ordered Collections**
```python
# Python - Tuples for fixed data
server_location = ("us-west-2", "availability-zone-a")     # Geographic data
database_credentials = ("username", "password", "host")    # Related but different types
version_info = (1, 2, 3)                                  # Semantic versioning

# Unpacking tuples
region, az = server_location
major, minor, patch = version_info

# Use cases:
coordinates = (37.7749, -122.4194)                        # Latitude, longitude
rgb_color = (255, 0, 0)                                   # Red, green, blue values
```

### **📊 Collection Performance Considerations**

```python
# Python - Performance differences
import time

# List operations
servers = ["web-01", "web-02", "web-03"] * 1000           # 3000 items

# O(n) - slow for large lists
start = time.time()
if "web-500" in servers:                                   # Linear search
    print("Found")
print(f"List search: {time.time() - start}")

# Set operations  
server_set = set(servers)                                  # Convert to set

# O(1) - fast regardless of size
start = time.time() 
if "web-500" in server_set:                               # Hash lookup
    print("Found")
print(f"Set search: {time.time() - start}")

# Dictionary operations
server_configs = {f"web-{i:02d}": {"port": 8080+i} for i in range(1000)}

# O(1) - fast key lookup
start = time.time()
config = server_configs.get("web-500", {})               # Hash lookup
print(f"Dict lookup: {time.time() - start}")
```

### **🎯 Real-World DevOps Collection Patterns**

#### **Configuration Management**
```python
# Ansible-style inventory structure
inventory = {
    "all": {
        "children": {
            "webservers": {
                "hosts": {
                    "web-01": {"ansible_host": "192.168.1.10"},
                    "web-02": {"ansible_host": "192.168.1.11"}
                },
                "vars": {
                    "http_port": 80,
                    "max_clients": 200
                }
            },
            "databases": {
                "hosts": {
                    "db-01": {"ansible_host": "192.168.1.20"}
                },
                "vars": {
                    "mysql_port": 3306,
                    "max_connections": 100
                }
            }
        }
    }
}
```

#### **Kubernetes-style Resources**
```python
# Kubernetes deployment structure
deployment = {
    "apiVersion": "apps/v1",
    "kind": "Deployment",
    "metadata": {
        "name": "web-app",
        "labels": {                                        # Object for key-value labels
            "app": "web",
            "version": "1.0"
        }
    },
    "spec": {
        "replicas": 3,
        "selector": {
            "matchLabels": {
                "app": "web"
            }
        },
        "template": {
            "metadata": {
                "labels": {
                    "app": "web"
                }
            },
            "spec": {
                "containers": [                            # Array for multiple containers
                    {
                        "name": "web",
                        "image": "nginx:1.20",
                        "ports": [                         # Array for multiple ports
                            {"containerPort": 80}
                        ],
                        "env": [                           # Array for environment variables
                            {"name": "ENV", "value": "production"},
                            {"name": "DEBUG", "value": "false"}
                        ]
                    }
                ]
            }
        }
    }
}
```

#### **Monitoring Data Structures**
```python
# Time-series monitoring data
metrics = {
    "timestamp": "2023-12-01T10:00:00Z",
    "server": "web-01",
    "metrics": {                                          # Object for related metrics
        "cpu_percent": 45.2,
        "memory_percent": 67.8,
        "disk_usage_percent": 23.1,
        "network_bytes_in": 1024000,
        "network_bytes_out": 2048000
    },
    "services": [                                         # Array for multiple services
        {
            "name": "nginx",
            "status": "running",
            "pid": 1234,
            "memory_mb": 45
        },
        {
            "name": "app",
            "status": "running", 
            "pid": 5678,
            "memory_mb": 128
        }
    ],
    "alerts": [                                           # Array for multiple alerts
        {
            "level": "warning",
            "message": "Memory usage above 60%",
            "timestamp": "2023-12-01T09:55:00Z"
        }
    ]
}

# Processing monitoring data
def process_metrics(metrics_data):
    server = metrics_data["server"]                       # Singular: one server
    cpu = metrics_data["metrics"]["cpu_percent"]          # Singular: one CPU value
    
    # Iterate through collections
    for service in metrics_data["services"]:             # Multiple: many services
        if service["status"] != "running":
            alert = {
                "level": "critical",
                "message": f"Service {service['name']} is down",
                "server": server
            }
            metrics_data["alerts"].append(alert)         # Add to collection
    
    return metrics_data
```

---

## 🌐 Variable Scope & Lifetime

### **Scope Types**

#### **Global Scope**
```python
# Python - Global scope
API_VERSION = "v1"  # Global variable, accessible everywhere

def deploy():
    print(f"Deploying API {API_VERSION}")  # Can access global
```

```javascript
// JavaScript - Global scope
const API_VERSION = "v1";  // Global in browser (window) or Node.js (global)

function deploy() {
    console.log(`Deploying API ${API_VERSION}`);  // Can access global
}
```

#### **Function/Local Scope**
```python
# Python - Function scope
def configure_server():
    server_config = {"port": 8080}  # Local to function
    return server_config

# print(server_config)  # ❌ Error: not accessible outside function
```

```javascript
// JavaScript - Function scope
function configureServer() {
    let serverConfig = {port: 8080};  // Local to function
    return serverConfig;
}

// console.log(serverConfig);  // ❌ Error: not accessible outside function
```

#### **Block Scope**
```javascript
// JavaScript - Block scope (let/const)
if (true) {
    let blockScoped = "only here";    // Block scope
    var functionScoped = "broader";   // Function scope
}

// console.log(blockScoped);      // ❌ Error: block scoped
console.log(functionScoped);      // ✅ Works: function scoped
```

```go
// Go - Block scope
func main() {
    if true {
        blockVar := "only here"  // Block scope
        fmt.Println(blockVar)    // ✅ Works inside block
    }
    // fmt.Println(blockVar)     // ❌ Error: not accessible outside block
}
```

### **Variable Lifetime**

```python
# Python - Variable lifetime
def process_data():
    # Variable created when function starts
    temp_data = []
    
    for i in range(1000):
        temp_data.append(i)  # Variable grows in memory
    
    return len(temp_data)
    # Variable destroyed when function ends (garbage collected)
```

---

## 📛 Variable Naming Conventions

### **Naming Styles by Language**

```python
# Python - snake_case
server_name = "web-01"
max_retry_count = 3
database_connection_string = "postgresql://..."
API_VERSION = "v1"  # UPPER_SNAKE_CASE for constants
```

```javascript
// JavaScript - camelCase
let serverName = "web-01";
let maxRetryCount = 3;
let databaseConnectionString = "postgresql://...";
const API_VERSION = "v1";  // UPPER_SNAKE_CASE for constants
```

```bash
#!/bin/bash
# Bash - UPPER_SNAKE_CASE
SERVER_NAME="web-01"
MAX_RETRY_COUNT=3
DATABASE_CONNECTION_STRING="postgresql://..."
```

```go
// Go - camelCase (private) / PascalCase (public)
var serverName string = "web-01"      // private (unexported)
var MaxRetryCount int = 3             // public (exported)
const DatabaseURL = "postgresql://..." // public constant
```

### **Naming Best Practices**

#### **✅ Good Names**
```python
# Descriptive and clear
user_count = 100
database_connection_timeout = 30
is_server_healthy = True
config_file_path = "/etc/app/config.yml"
```

#### **❌ Poor Names**
```python
# Unclear and confusing
x = 100          # What does x represent?
data = True      # What kind of data?
temp = "/etc/app/config.yml"  # Temp what?
flag = 30        # Flag for what?
```

#### **DevOps-Specific Naming**
```python
# Infrastructure
server_instances = ["web-01", "web-02", "web-03"]
load_balancer_ip = "10.0.1.100"
ssl_certificate_path = "/etc/ssl/certs/app.pem"

# Deployment
deployment_environment = "production"
build_number = "1.2.3-456"
rollback_enabled = True

# Monitoring
cpu_usage_percent = 75.5
memory_available_mb = 2048
disk_free_space_gb = 100.7
```

---

## 🧠 Memory Management & Variables

### **Stack vs Heap**

```python
# Python - Everything is an object (heap allocated)
number = 42              # Integer object in heap
name = "DevOps"          # String object in heap
servers = ["web-01"]     # List object in heap

# Python handles memory automatically (garbage collection)
```

```javascript
// JavaScript - Primitives vs Objects
let number = 42;              // Primitive (stack or optimized)
let name = "DevOps";          // Primitive (stack or optimized)
let servers = ["web-01"];     // Object (heap)

// JavaScript has automatic garbage collection
```

```go
// Go - More explicit memory management
func example() {
    number := 42              // Stack allocated
    name := "DevOps"          // String (optimized)
    servers := []string{"web-01"}  // Slice header on stack, data on heap
    
    serverPtr := &servers[0]  // Pointer to heap memory
} // Stack variables cleaned up when function exits
```

### **Memory Considerations in DevOps**

```python
# Python - Be careful with large data structures
def process_logs():
    all_logs = []  # This could grow very large!
    
    for log_file in log_files:
        with open(log_file) as f:
            all_logs.extend(f.readlines())  # Memory usage grows
    
    # Better: process one file at a time
    for log_file in log_files:
        with open(log_file) as f:
            for line in f:  # Process line by line
                process_line(line)
```

---

## 🎯 Language-Specific Deep Dives

### **Python Variables**

```python
# Dynamic typing and mutability
server_name = "web-01"        # str (immutable)
server_name = "web-02"        # Creates new string object

server_list = ["web-01"]      # list (mutable)
server_list.append("web-02")  # Modifies existing list object

# Global vs local with same name
counter = 0  # Global

def increment():
    global counter  # Must declare to modify global
    counter += 1

def local_counter():
    counter = 10    # Creates local variable, doesn't affect global
    return counter

# Class variables vs instance variables
class Server:
    default_port = 80  # Class variable (shared)
    
    def __init__(self, name):
        self.name = name      # Instance variable (per object)
        self.port = 8080      # Instance variable
```

### **JavaScript Variables**

```javascript
// Hoisting behavior
console.log(hoistedVar);  // undefined (not error!)
var hoistedVar = "value";

// console.log(notHoisted);  // ❌ Error: Cannot access before initialization
let notHoisted = "value";

// Temporal dead zone
function example() {
    // console.log(tempVar);  // ❌ Error: temporal dead zone
    let tempVar = "value";
    console.log(tempVar);     // ✅ Works
}

// Closure and variable capture
function createCounter() {
    let count = 0;  // Captured by closure
    
    return function() {
        count++;    // Accesses outer variable
        return count;
    };
}

const counter = createCounter();
console.log(counter());  // 1
console.log(counter());  // 2
```

### **Go Variables**

```go
// Zero values
var serverName string     // "" (empty string)
var port int              // 0
var isActive bool         // false
var config *Config        // nil

// Short declaration vs var
func example() {
    // These are equivalent:
    var name string = "web-01"
    name := "web-01"
    
    // But different scope rules:
    if true {
        name := "web-02"  // New variable, shadows outer
        fmt.Println(name) // "web-02"
    }
    fmt.Println(name)     // "web-01"
}

// Blank identifier
func getData() (string, error) {
    return "data", nil
}

func main() {
    data, _ := getData()  // Ignore error (blank identifier)
    fmt.Println(data)
}
```

### **Bash Variables**

```bash
#!/bin/bash

# Variable assignment (no spaces around =)
SERVER_NAME="web-01"      # ✅ Correct
# SERVER_NAME = "web-01"  # ❌ Error: spaces not allowed

# Environment variables vs shell variables
SHELL_VAR="local"         # Shell variable
export ENV_VAR="global"   # Environment variable (inherited by child processes)

# Parameter expansion
DEFAULT_PORT="8080"
PORT=${PORT:-$DEFAULT_PORT}        # Use PORT if set, otherwise DEFAULT_PORT
PORT=${PORT:="8080"}               # Set PORT to 8080 if unset/empty

# Arrays (bash 4+)
SERVERS=("web-01" "web-02" "web-03")
echo ${SERVERS[0]}                 # First element
echo ${SERVERS[@]}                 # All elements
echo ${#SERVERS[@]}                # Array length

# Command substitution
CURRENT_DATE=$(date)
FILE_COUNT=`ls | wc -l`           # Older syntax
```

---

## 🚀 DevOps-Specific Use Cases

### **Configuration Management**

```python
# Python - Configuration variables
import os

# Environment-based configuration
DATABASE_URL = os.getenv('DATABASE_URL', 'sqlite:///default.db')
DEBUG_MODE = os.getenv('DEBUG', 'False').lower() == 'true'
LOG_LEVEL = os.getenv('LOG_LEVEL', 'INFO')

# Configuration classes
class Config:
    SECRET_KEY = os.getenv('SECRET_KEY', 'dev-secret')
    DATABASE_URL = os.getenv('DATABASE_URL', 'sqlite:///app.db')

class ProductionConfig(Config):
    DEBUG = False
    DATABASE_URL = os.getenv('DATABASE_URL')  # Required in production

class DevelopmentConfig(Config):
    DEBUG = True
```

```yaml
# YAML - Configuration variables
# config.yml
database:
  host: ${DB_HOST:-localhost}     # Environment variable with default
  port: ${DB_PORT:-5432}
  name: ${DB_NAME}                # Required environment variable

app:
  debug: ${DEBUG:-false}
  log_level: ${LOG_LEVEL:-info}
  workers: ${WORKER_COUNT:-4}

features:
  enable_monitoring: true
  enable_caching: ${ENABLE_CACHE:-false}
```

### **Deployment Scripts**

```bash
#!/bin/bash
# Deployment script with variables

# Script configuration
readonly SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
readonly APP_NAME="my-app"
readonly TIMESTAMP=$(date +"%Y%m%d_%H%M%S")

# Environment variables with validation
ENVIRONMENT=${ENVIRONMENT:?"Environment must be set (dev|staging|prod)"}
BUILD_NUMBER=${BUILD_NUMBER:?"Build number must be set"}
DEPLOY_USER=${DEPLOY_USER:-"deploy"}

# Derived variables
BACKUP_DIR="/backups/${APP_NAME}_${TIMESTAMP}"
DEPLOYMENT_DIR="/opt/${APP_NAME}"
LOG_FILE="/var/log/${APP_NAME}/deploy_${TIMESTAMP}.log"

# Function-scoped variables
deploy_application() {
    local deploy_temp_dir="/tmp/${APP_NAME}_deploy"
    local previous_version=$(get_current_version)
    
    echo "Deploying ${APP_NAME} v${BUILD_NUMBER} to ${ENVIRONMENT}"
    echo "Previous version: ${previous_version}"
}
```

### **Infrastructure as Code**

```hcl
# Terraform - Variable definitions
variable "environment" {
  description = "Environment name (dev, staging, prod)"
  type        = string
  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Environment must be dev, staging, or prod."
  }
}

variable "instance_count" {
  description = "Number of instances to create"
  type        = number
  default     = 1
}

# Local values (computed variables)
locals {
  name_prefix = "${var.environment}-${var.project_name}"
  
  common_tags = {
    Environment = var.environment
    Project     = var.project_name
    ManagedBy   = "terraform"
    Owner       = var.team_name
  }
  
  # Conditional variables
  instance_type = var.environment == "prod" ? "t3.large" : "t3.micro"
}

# Resource using variables
resource "aws_instance" "app" {
  count         = var.instance_count
  ami           = var.ami_id
  instance_type = local.instance_type
  
  tags = merge(local.common_tags, {
    Name = "${local.name_prefix}-app-${count.index + 1}"
  })
}
```

### **Monitoring and Alerting**

```python
# Python - Monitoring variables
import time
from dataclasses import dataclass

@dataclass
class SystemMetrics:
    timestamp: float
    cpu_percent: float
    memory_used_mb: int
    disk_free_gb: float
    active_connections: int

# Threshold variables
CPU_WARNING_THRESHOLD = 70.0
CPU_CRITICAL_THRESHOLD = 90.0
MEMORY_WARNING_THRESHOLD = 80.0
DISK_SPACE_CRITICAL_GB = 10.0

def check_system_health():
    metrics = get_current_metrics()
    
    alerts = []
    
    # CPU check
    if metrics.cpu_percent > CPU_CRITICAL_THRESHOLD:
        alerts.append(f"CRITICAL: CPU usage {metrics.cpu_percent}%")
    elif metrics.cpu_percent > CPU_WARNING_THRESHOLD:
        alerts.append(f"WARNING: CPU usage {metrics.cpu_percent}%")
    
    # Memory check
    memory_percent = (metrics.memory_used_mb / TOTAL_MEMORY_MB) * 100
    if memory_percent > MEMORY_WARNING_THRESHOLD:
        alerts.append(f"WARNING: Memory usage {memory_percent:.1f}%")
    
    return alerts
```

---

## ⚠️ Common Pitfalls & Troubleshooting

### **Scope Issues**

```python
# Python - Common scope pitfall
counter = 0

def broken_increment():
    counter = counter + 1  # ❌ UnboundLocalError!
    return counter

def fixed_increment():
    global counter         # ✅ Declare global
    counter = counter + 1
    return counter

# Better: return new value instead of modifying global
def better_increment(current_value):
    return current_value + 1

counter = better_increment(counter)
```

```javascript
// JavaScript - Closure pitfall
var functions = [];

for (var i = 0; i < 3; i++) {
    functions.push(function() {
        console.log(i);  // ❌ Always prints 3!
    });
}

// Fixed with let (block scope)
for (let i = 0; i < 3; i++) {
    functions.push(function() {
        console.log(i);  // ✅ Prints 0, 1, 2
    });
}

// Fixed with closure
for (var i = 0; i < 3; i++) {
    functions.push((function(index) {
        return function() {
            console.log(index);  // ✅ Prints 0, 1, 2
        };
    })(i));
}
```

### **Type Confusion**

```python
# Python - String vs number confusion
port = "8080"          # String from environment variable
new_port = port + 1    # ❌ TypeError: can't concatenate str and int

# Fix: convert types
port = int(os.getenv('PORT', '8080'))
new_port = port + 1    # ✅ Works: 8081

# Boolean confusion
enabled = "False"      # String
if enabled:            # ❌ Always true! (non-empty string)
    start_service()

# Fix: proper boolean conversion
enabled = os.getenv('ENABLED', 'false').lower() == 'true'
if enabled:            # ✅ Correctly evaluates
    start_service()
```

```bash
#!/bin/bash
# Bash - Numeric comparison confusion

COUNT="10"
if [ $COUNT > 5 ]; then    # ❌ String comparison! "10" < "5" alphabetically
    echo "Greater than 5"
fi

# Fix: numeric comparison
if [ $COUNT -gt 5 ]; then  # ✅ Numeric comparison
    echo "Greater than 5"
fi

# Fix: arithmetic context
if (( COUNT > 5 )); then   # ✅ Arithmetic comparison
    echo "Greater than 5"
fi
```

### **Memory Leaks**

```javascript
// JavaScript - Memory leak with closures
function createHandler() {
    const largeData = new Array(1000000).fill('data');  // Large array
    
    return function(event) {
        // Even if we don't use largeData, it's kept in memory
        // because of the closure
        console.log('Event handled');
    };
}

// Fix: don't capture unnecessary variables
function createHandler() {
    const largeData = new Array(1000000).fill('data');
    
    // Process data and extract only what's needed
    const summary = processData(largeData);
    
    return function(event) {
        // Only 'summary' is captured, not 'largeData'
        console.log('Event handled:', summary);
    };
}
```

### **Environment Variable Issues**

```python
# Python - Environment variable pitfalls
import os

# ❌ Common mistakes
debug = os.getenv('DEBUG')        # Could be None, "false", "0", etc.
if debug:                         # "false" is truthy!
    enable_debugging()

timeout = os.getenv('TIMEOUT')    # String, not number
connection.timeout = timeout + 5  # ❌ TypeError

# ✅ Proper handling
debug = os.getenv('DEBUG', 'false').lower() in ('true', '1', 'yes', 'on')
timeout = int(os.getenv('TIMEOUT', '30'))

# ✅ Even better: validation function
def get_bool_env(name, default=False):
    value = os.getenv(name, str(default)).lower()
    return value in ('true', '1', 'yes', 'on')

def get_int_env(name, default=0):
    try:
        return int(os.getenv(name, str(default)))
    except ValueError:
        print(f"Warning: Invalid value for {name}, using default {default}")
        return default

debug = get_bool_env('DEBUG', False)
timeout = get_int_env('TIMEOUT', 30)
```

---

## 🏆 Variable Best Practices Summary

### **✅ Do's**

1. **Use descriptive names**: `user_count` not `x`
2. **Follow language conventions**: snake_case in Python, camelCase in JavaScript
3. **Initialize variables**: Don't use uninitialized variables
4. **Use appropriate scope**: Keep variables as local as possible
5. **Validate input**: Check environment variables and user input
6. **Use constants for fixed values**: `MAX_RETRIES = 3`
7. **Handle None/null values**: Check before using
8. **Use type hints** (where available): `def func(name: str) -> int:`

### **❌ Don'ts**

1. **Don't use global variables unnecessarily**: Prefer function parameters
2. **Don't ignore variable scope**: Understand where variables are accessible
3. **Don't assume types**: Validate and convert as needed
4. **Don't create memory leaks**: Be careful with closures and references
5. **Don't use misleading names**: `flag` could mean anything
6. **Don't hardcode values**: Use configuration variables instead
7. **Don't forget error handling**: Environment variables might not exist

---

📄 **File Path**: `/DevOps/00-Code_Practices/01-DevOps_Programming_Languages/Variables_Complete_Guide.md`

📖 **What This File Does**: Provides comprehensive coverage of variables across all programming languages used in DevOps, including theory, practical examples, and troubleshooting.

🔧 **Configuration Notes**: No configuration needed - this is a reference document. Use it alongside the syntax comparison file for complete understanding of variables in DevOps contexts. 