# Programming Language Syntax Comparison for DevOps

## 📖 What This File Covers
Comprehensive syntax comparison across all major DevOps programming languages. Understand the fundamental differences in how each language handles variables, data structures, functions, and common operations you'll encounter in DevOps workflows.

## 🎯 Learning Objectives
- Compare syntax across Python, JavaScript, Bash, Go, and configuration languages
- Understand data structure differences and when they matter
- Learn language-specific conventions and best practices
- See practical DevOps examples in each language

---

## 📚 **Programming Concepts Explained**

### **📝 Variables - Storing and Managing Data**

**What is a Variable?**
A variable is a named storage location that holds data which can be referenced and manipulated throughout your program. Think of it as a labeled box where you can store information and retrieve it later using the label (variable name).

**Key Concepts:**
- **Declaration**: Creating a variable and optionally giving it an initial value
- **Assignment**: Putting data into a variable
- **Scope**: Where in your code the variable can be accessed
- **Mutability**: Whether the variable's value can be changed after creation
  - **Mutable**: Can be changed/modified after creation (like a whiteboard you can erase and rewrite)
  - **Immutable**: Cannot be changed after creation (like writing in permanent ink)

**Examples Across Languages:**
```python
# Python - Dynamic typing, simple assignment
server_name = "web-01"        # Declaration and assignment in one step
port = 8080                   # Type inferred from value
server_name = "web-02"        # Variables are mutable by default
```

```javascript
// JavaScript - Explicit declaration keywords
let serverName = "web-01";    // 'let' creates a mutable variable (can be changed)
const port = 8080;            // 'const' creates an immutable variable (cannot be changed)
var oldStyle = "legacy";      // 'var' is older, function-scoped

serverName = "web-02";        // ✅ Allowed - let variables are mutable
// port = 9090;               // ❌ Error - const variables are immutable
```

```bash
#!/bin/bash
# Bash - All variables are strings
SERVER_NAME="web-01"          # No declaration keyword needed
PORT="8080"                   # Numbers are stored as strings
SERVER_NAME="web-02"          # Variables are mutable
```

```go
// Go - Static typing, explicit types
var serverName string = "web-01"    // Explicit type declaration
port := 8080                        // Type inference with :=
// serverName = 123                 // ❌ Error: wrong type
```

---

### **🔒 Constants - Immutable Values**

**What is a Constant?**
A constant is a variable whose value cannot be changed after it's initially set. Constants are used for values that should remain the same throughout your program's execution, like configuration values, mathematical constants, or fixed settings.

**Why Use Constants?**
- **Safety**: Prevents accidental modification of important values
- **Clarity**: Makes it clear which values are intended to be fixed
- **Performance**: Some languages optimize constants at compile time
- **Documentation**: Self-documenting code - shows what values are meant to be stable

**Examples Across Languages:**
```python
# Python - Convention-based constants (not enforced)
API_VERSION = "v1"           # UPPERCASE indicates constant (convention)
MAX_RETRIES = 3              # Still mutable, but convention says don't change
DATABASE_URL = "postgresql://localhost:5432/app"

# Python doesn't enforce immutability, relies on developer discipline
# API_VERSION = "v2"         # Technically allowed but breaks convention
```

```javascript
// JavaScript - Enforced immutability with const
const API_VERSION = "v1";           // Cannot be reassigned
const MAX_RETRIES = 3;              // Immutable primitive
const CONFIG = {                    // Object reference is constant...
    database: "localhost",
    port: 5432
};

// CONFIG = {};                     // ❌ Error: cannot reassign
CONFIG.database = "remote";         // ✅ Allowed: object contents can change
Object.freeze(CONFIG);              // Make object contents immutable too
```

```bash
#!/bin/bash
# Bash - readonly keyword for constants
readonly API_VERSION="v1"           # Cannot be changed
readonly MAX_RETRIES=3
# API_VERSION="v2"                  # ❌ Error: readonly variable
```

```go
// Go - const keyword for compile-time constants
const APIVersion = "v1"             // Compile-time constant
const MaxRetries = 3                // Must be known at compile time
const DatabaseURL = "postgresql://localhost:5432/app"

// Runtime constants using variables
var configFile = "config.yml"       // Can be set once at runtime
```

---

### **❌ Null, None, Empty - Representing Absence of Data**

**What are Null/None/Empty Values?**
These represent the absence of a value or an undefined state. Different languages have different ways to express "nothing" or "no value", and understanding these differences is crucial for handling missing data and avoiding errors.

**Key Concepts:**
- **Null/None**: Explicitly represents "no value"
- **Undefined**: Variable declared but never assigned (JavaScript)
- **Empty String**: String with no characters (`""`)
- **Zero**: Numeric zero (different from null/none)
- **False**: Boolean false (different from null/none)

**Examples and Differences:**
```python
# Python - None represents absence of value
result = None                # Explicit "no value"
api_key = None              # Not yet set
empty_string = ""           # String with no content
zero_value = 0              # Numeric zero (not the same as None)
false_value = False         # Boolean false (not the same as None)

# Checking for None
if result is None:          # Use 'is' for None comparison
    print("No result yet")

if api_key:                 # Falsy check (None, "", 0, False all fail this)
    print("API key is set")
```

```javascript
// JavaScript - Multiple "empty" values
let result = null;              // Explicit "no value"
let apiKey;                     // undefined (declared but not assigned)
let emptyString = "";           // String with no content
let zeroValue = 0;              // Numeric zero
let falseValue = false;         // Boolean false

// Different types of "emptiness"
console.log(result);            // null
console.log(apiKey);            // undefined
console.log(typeof result);     // "object" (JavaScript quirk)
console.log(typeof apiKey);     // "undefined"

// Checking for empty values
if (result === null) { }        // Strict null check
if (apiKey === undefined) { }   // Strict undefined check
if (result == null) { }         // Checks both null AND undefined
if (!apiKey) { }                // Falsy check (null, undefined, "", 0, false)
if (apiKey != null) { }         // Truthy check (not null or undefined)
```

```bash
#!/bin/bash
# Bash - Empty strings and unset variables
RESULT=""                       # Empty string
unset API_KEY                   # Unset variable (doesn't exist)

# Checking for empty/unset values
if [[ -z "$RESULT" ]]; then     # True if string is empty
    echo "Result is empty"
fi

if [[ -v API_KEY ]]; then       # True if variable is set (even if empty)
    echo "API_KEY is set"
fi

if [[ -n "$RESULT" ]]; then     # True if string is not empty
    echo "Result has content"
fi

# Default values for empty/unset variables
DATABASE_URL=${DATABASE_URL:-"localhost:5432"}  # Use default if empty/unset
PORT=${PORT:=3000}              # Set PORT to 3000 if empty/unset
```

```go
// Go - Zero values and pointers for nullable types
var result string              // Zero value: "" (empty string)
var count int                  // Zero value: 0
var enabled bool               // Zero value: false
var apiKey *string             // Zero value: nil (pointer)

// Explicit nil for nullable types
var optionalValue *int = nil   // Pointer that can be nil

// Checking for zero values and nil
if result == "" {              // Check for empty string
    fmt.Println("Result is empty")
}

if apiKey == nil {             // Check for nil pointer
    fmt.Println("API key not set")
}

// Go doesn't have "undefined" - uninitialized variables get zero values
var uninitialized string       // Automatically gets "" (not nil)
```

---

### **📊 Arrays/Lists - Collections of Data**

**What are Arrays/Lists?**
Arrays (or lists) are ordered collections of elements that can store multiple values in a single variable. Think of them as numbered containers where you can store related items and access them by their position (index).

**Key Concepts:**
- **Index**: The position of an element (usually starting from 0)
- **Length/Size**: How many elements the array contains
- **Mutability**: Whether you can add/remove/change elements
- **Type Safety**: Whether all elements must be the same type

**Indexing Examples:**
```
Array: ["web-01", "web-02", "web-03"]
Index:     0        1        2

First element: array[0] = "web-01"
Last element:  array[2] = "web-03"
```

**Examples Across Languages:**
```python
# Python - Lists (dynamic, mixed types allowed)
servers = ["web-01", "web-02", "web-03"]     # List of strings
ports = [80, 443, 3000]                      # List of numbers
mixed = ["server", 123, True, None]          # Mixed types allowed

# Accessing elements
first_server = servers[0]                     # "web-01"
last_server = servers[-1]                    # "web-03" (negative indexing)
middle_server = servers[1]                    # "web-02"

# Modifying lists
servers.append("web-04")                      # Add to end
servers.insert(1, "cache-01")               # Insert at position 1
servers.remove("web-02")                     # Remove by value
popped = servers.pop()                       # Remove and return last element

# List operations
length = len(servers)                        # Get length
servers.sort()                              # Sort in place
reversed_servers = servers[::-1]            # Create reversed copy
```

```javascript
// JavaScript - Arrays (dynamic, mixed types allowed)
const servers = ["web-01", "web-02", "web-03"];  // Array of strings
const ports = [80, 443, 3000];                   // Array of numbers
const mixed = ["server", 123, true, null];       // Mixed types allowed

// Accessing elements
const firstServer = servers[0];                   // "web-01"
const lastServer = servers[servers.length - 1];  // "web-03" (no negative indexing)
const middleServer = servers[1];                  // "web-02"

// Modifying arrays
servers.push("web-04");                          // Add to end
servers.unshift("cache-01");                    // Add to beginning
servers.splice(1, 1, "new-server");            // Remove 1 element at index 1, insert "new-server"
const popped = servers.pop();                   // Remove and return last element

// Array operations
const length = servers.length;                   // Get length
servers.sort();                                 // Sort in place
const reversedServers = [...servers].reverse(); // Create reversed copy
```

```bash
#!/bin/bash
# Bash - Arrays (strings only)
SERVERS=("web-01" "web-02" "web-03")            # Array of strings
PORTS=(80 443 3000)                             # Numbers stored as strings

# Accessing elements
FIRST_SERVER=${SERVERS[0]}                      # "web-01"
LAST_INDEX=$((${#SERVERS[@]} - 1))             # Calculate last index
LAST_SERVER=${SERVERS[$LAST_INDEX]}            # Last element

# Modifying arrays
SERVERS+=("web-04")                             # Append element
SERVERS[1]="cache-01"                          # Replace element at index 1
unset SERVERS[2]                               # Remove element (leaves gap)

# Array operations
LENGTH=${#SERVERS[@]}                          # Get length
ALL_SERVERS=${SERVERS[@]}                      # Get all elements
```

```go
// Go - Arrays and Slices (type-safe)
// Arrays have fixed size, slices are dynamic
servers := []string{"web-01", "web-02", "web-03"}  // Slice of strings
ports := []int{80, 443, 3000}                      // Slice of integers
// mixed := []interface{}{"server", 123, true}     // Possible but not recommended

// Accessing elements
firstServer := servers[0]                           // "web-01"
lastServer := servers[len(servers)-1]              // "web-03"
middleServer := servers[1]                         // "web-02"

// Modifying slices
servers = append(servers, "web-04")                // Add to end (returns new slice)
servers = append([]string{"cache-01"}, servers...) // Add to beginning
// No built-in insert/remove - need custom functions

// Slice operations
length := len(servers)                             // Get length
// sort.Strings(servers)                          // Sort (need sort package)
```

---

### **🗂️ Objects/Dictionaries/Maps - Key-Value Data**

**What are Objects/Dictionaries/Maps?**
These are collections that store data as key-value pairs, where each piece of data (value) is associated with a unique identifier (key). Instead of accessing data by numeric position like arrays, you access it by name/key.

**Key Concepts:**
- **Key**: The identifier used to access a value (like a label)
- **Value**: The data associated with a key
- **Lookup**: Finding a value using its key
- **Dynamic**: Keys and values can usually be added/removed at runtime

**Conceptual Example:**
```
Server Configuration:
Key: "name"     → Value: "web-01"
Key: "ip"       → Value: "10.0.1.10"
Key: "status"   → Value: "running"
Key: "port"     → Value: 80
```

**Examples Across Languages:**
```python
# Python - Dictionaries
server = {
    "name": "web-01",
    "ip": "10.0.1.10", 
    "status": "running",
    "port": 80
}

# Accessing values - bracket notation only
server_name = server["name"]                    # "web-01"
server_ip = server["ip"]                       # "10.0.1.10"

# Safe access with default
timeout = server.get("timeout", 30)           # Returns 30 if key doesn't exist

# Modifying dictionaries
server["status"] = "stopped"                   # Update existing key
server["cpu_usage"] = 0.75                    # Add new key

# Dictionary operations
keys = list(server.keys())                     # Get all keys
values = list(server.values())                # Get all values
items = list(server.items())                  # Get key-value pairs

# Check if key exists
if "port" in server:
    print(f"Port is {server['port']}")
```

```javascript
// JavaScript - Objects
const server = {
    name: "web-01",          // No quotes needed for simple keys
    ip: "10.0.1.10",
    status: "running", 
    port: 80
};

// Accessing values - dot notation or bracket notation
const serverName = server.name;               // "web-01" (preferred)
const serverIp = server["ip"];               // "10.0.1.10" (also works)
const dynamicKey = "status";
const status = server[dynamicKey];           // "running" (useful for dynamic keys)

// Safe access with defaults
const timeout = server.timeout || 30;        // Returns 30 if undefined
const cpu = server.cpu ?? 0;                // Nullish coalescing

// Modifying objects
server.status = "stopped";                   // Update existing property
server.cpuUsage = 0.75;                     // Add new property

// Object operations
const keys = Object.keys(server);            // Get all keys
const values = Object.values(server);        // Get all values  
const entries = Object.entries(server);      // Get key-value pairs

// Check if property exists
if (server.port !== undefined) {
    console.log(`Port is ${server.port}`);
}
```

```bash
#!/bin/bash
# Bash - Associative Arrays (Bash 4.0+)
declare -A SERVER
SERVER[name]="web-01"
SERVER[ip]="10.0.1.10"
SERVER[status]="running"
SERVER[port]="80"

# Accessing values - bracket notation only
SERVER_NAME=${SERVER[name]}                   # "web-01"
SERVER_IP=${SERVER[ip]}                      # "10.0.1.10"

# Safe access with default
TIMEOUT=${SERVER[timeout]:-30}               # Returns 30 if key doesn't exist

# Modifying associative arrays
SERVER[status]="stopped"                     # Update existing key
SERVER[cpu_usage]="0.75"                    # Add new key

# Associative array operations
for key in "${!SERVER[@]}"; do              # Iterate over keys
    echo "Key: $key, Value: ${SERVER[$key]}"
done

# Check if key exists
if [[ -v SERVER[port] ]]; then
    echo "Port is ${SERVER[port]}"
fi
```

```go
// Go - Maps
server := map[string]interface{}{
    "name":   "web-01",
    "ip":     "10.0.1.10",
    "status": "running",
    "port":   80,
}

// Better: Use specific types when possible
serverConfig := map[string]string{
    "name":   "web-01",
    "ip":     "10.0.1.10", 
    "status": "running",
}

// Accessing values - bracket notation with existence check
serverName, exists := server["name"]         // Returns value and existence bool
if exists {
    fmt.Printf("Server name: %v\n", serverName)
}

// Direct access (panics if key doesn't exist with wrong type)
serverIP := server["ip"].(string)           // Type assertion needed

// Modifying maps
server["status"] = "stopped"                 // Update existing key
server["cpu_usage"] = 0.75                  // Add new key

// Map operations
for key, value := range server {            // Iterate over key-value pairs
    fmt.Printf("Key: %s, Value: %v\n", key, value)
}

// Check if key exists
if _, exists := server["port"]; exists {
    fmt.Println("Port is configured")
}
```

---

## 📝 **Variables and Basic Data Types**

| Feature | Python | JavaScript | Bash | Go | YAML |
|---------|--------|------------|------|----|------|
| **Variable Declaration** | `server_name = "web-01"` | `const serverName = "web-01";` | `SERVER_NAME="web-01"` | `serverName := "web-01"` | `server_name: web-01` |
| **Numbers** | `port = 8080` | `const port = 8080;` | `PORT=8080` | `port := 8080` | `port: 8080` |
| **Booleans** | `is_prod = True` | `const isProd = true;` | `IS_PROD=true` | `isProd := true` | `is_prod: true` |
| **Null/Empty** | `result = None` | `let result = null;` | `RESULT=""` | `var result *string` | `result: null` |
| **Constants** | `PORT = 8080` (convention) | `const PORT = 8080;` | `readonly PORT=8080` | `const Port = 8080` | `port: 8080` |

### **Key Differences Explained:**

#### **Python:**
```python
# Python - Dynamic typing, simple assignment
environment = "production"
debug_mode = False
max_retries = 3
api_key = None

# Python naming convention: snake_case
database_host = "db.example.com"
is_production = True
```

#### **JavaScript:**
```javascript
// JavaScript - Explicit declaration keywords
const environment = "production";
let debugMode = false;
const maxRetries = 3;
let apiKey = null;

// JavaScript naming convention: camelCase
const databaseHost = "db.example.com";
const isProduction = true;
```

#### **Bash:**
```bash
#!/bin/bash
# Bash - All variables are strings, no spaces around =
ENVIRONMENT="production"
DEBUG_MODE="false"
MAX_RETRIES="3"
API_KEY=""

# Bash naming convention: UPPER_CASE
DATABASE_HOST="db.example.com"
IS_PRODUCTION="true"
```

#### **Go:**
```go
// Go - Static typing, explicit types
var environment string = "production"
debugMode := false
maxRetries := 3
var apiKey *string  // pointer for nullable

// Go naming convention: camelCase (public: PascalCase)
databaseHost := "db.example.com"
isProduction := true
```

#### **YAML:**
```yaml
# YAML - Structured configuration, indentation matters
environment: production
debug_mode: false
max_retries: 3
api_key: null

# YAML naming convention: snake_case
database_host: db.example.com
is_production: true
```

---

## 📊 **Lists/Arrays and Collections**

### **Syntax Comparison:**

| Feature | Python | JavaScript | Bash | Go | YAML |
|---------|--------|------------|------|----|------|
| **List/Array Creation** | `servers = ["web-01", "web-02"]` | `const servers = ["web-01", "web-02"];` | `SERVERS=("web-01" "web-02")` | `servers := []string{"web-01", "web-02"}` | `servers: [web-01, web-02]` |
| **Access Element** | `servers[0]` | `servers[0]` | `${SERVERS[0]}` | `servers[0]` | N/A (config only) |
| **Add Element** | `servers.append("web-03")` | `servers.push("web-03");` | `SERVERS+=("web-03")` | `servers = append(servers, "web-03")` | N/A |
| **Length** | `len(servers)` | `servers.length` | `${#SERVERS[@]}` | `len(servers)` | N/A |

### **Detailed Examples:**

#### **Python Lists:**
```python
# Python - Dynamic, mixed types allowed
servers = ["web-01", "web-02", "db-01"]
ports = [80, 443, 3000]
mixed = ["server", 8080, True, None]

# Rich built-in methods
running_servers = [s for s in servers if "web" in s]  # List comprehension
servers.append("cache-01")
servers.remove("db-01")
last_server = servers.pop()

# Negative indexing (unique to Python)
last_server = servers[-1]
second_to_last = servers[-2]

# Slicing
first_two = servers[:2]
all_but_first = servers[1:]
```

#### **JavaScript Arrays:**
```javascript
// JavaScript - Dynamic, mixed types allowed
const servers = ["web-01", "web-02", "db-01"];
const ports = [80, 443, 3000];
const mixed = ["server", 8080, true, null];

// Rich array methods
const runningServers = servers.filter(s => s.includes("web"));
servers.push("cache-01");
const index = servers.indexOf("db-01");
servers.splice(index, 1);  // Remove by index

// No negative indexing, but alternatives exist
const lastServer = servers[servers.length - 1];
const lastServer2 = servers.slice(-1)[0];  // Alternative

// Modern array methods
const serverNames = servers.map(s => s.toUpperCase());
const hasWebServer = servers.some(s => s.includes("web"));
```

#### **Bash Arrays:**
```bash
#!/bin/bash
# Bash - Arrays of strings only
SERVERS=("web-01" "web-02" "db-01")
PORTS=(80 443 3000)  # Still strings, just look like numbers

# Array operations
SERVERS+=("cache-01")  # Append
unset SERVERS[2]       # Remove element at index 2

# Access elements
echo "First server: ${SERVERS[0]}"
echo "All servers: ${SERVERS[@]}"
echo "Number of servers: ${#SERVERS[@]}"

# Loop through array
for server in "${SERVERS[@]}"; do
    echo "Processing: $server"
done

# Find in array (requires loop)
for server in "${SERVERS[@]}"; do
    if [[ "$server" == *"web"* ]]; then
        echo "Found web server: $server"
    fi
done
```

#### **Go Slices:**
```go
// Go - Statically typed, type-safe
servers := []string{"web-01", "web-02", "db-01"}
ports := []int{80, 443, 3000}
// mixed := []interface{}{"server", 8080, true}  // Possible but not recommended

// Slice operations
servers = append(servers, "cache-01")  // Returns new slice
servers = append(servers[:1], servers[2:]...)  // Remove element at index 1

// Access elements
firstServer := servers[0]
length := len(servers)

// No built-in filter, need loop or external library
var webServers []string
for _, server := range servers {
    if strings.Contains(server, "web") {
        webServers = append(webServers, server)
    }
}

// Slicing (similar to Python)
firstTwo := servers[:2]
allButFirst := servers[1:]
```

#### **YAML Arrays:**
```yaml
# YAML - Configuration only, various syntaxes
servers:
  - web-01
  - web-02
  - db-01

# Inline syntax
ports: [80, 443, 3000]

# Mixed types allowed
mixed:
  - server_name
  - 8080
  - true
  - null

# Nested arrays
server_configs:
  - name: web-01
    ports: [80, 443]
    env: production
  - name: web-02
    ports: [80, 443]
    env: staging
```

---

## 🗂️ **Dictionaries/Objects/Maps**

### **Syntax Comparison:**

| Feature | Python | JavaScript | Bash | Go | YAML |
|---------|--------|------------|------|----|------|
| **Creation** | `config = {"port": 80}` | `const config = {port: 80};` | `declare -A CONFIG; CONFIG[port]=80` | `config := map[string]int{"port": 80}` | `config: {port: 80}` |
| **Access Value** | `config["port"]` | `config.port` or `config["port"]` | `${CONFIG[port]}` | `config["port"]` | N/A |
| **Add/Update** | `config["ssl"] = True` | `config.ssl = true;` | `CONFIG[ssl]="true"` | `config["ssl"] = true` | N/A |
| **Check Key Exists** | `"port" in config` | `config.port !== undefined` | `[[ -v CONFIG[port] ]]` | `_, exists := config["port"]` | N/A |

### **Detailed Examples with Key Differences:**

#### **Python Dictionaries:**
```python
# Python - Dictionary with various data types
config = {
    "service_name": "user-api",
    "port": 3000,
    "ssl_enabled": True,
    "replicas": 3,
    "environment": "production"
}

# Key access - ONLY bracket notation
port = config["port"]                    # ✅ Works
# port = config.port                     # ❌ AttributeError

# Safe access methods
timeout = config.get("timeout", 30)     # Returns 30 if key doesn't exist
ssl = config.get("ssl_enabled", False)

# Key operations
if "port" in config:
    print(f"Port configured: {config['port']}")

# All keys, values, items
for key in config.keys():
    print(f"Key: {key}")

for key, value in config.items():
    print(f"{key}: {value}")

# Nested dictionaries
server_config = {
    "web_servers": {
        "web-01": {"ip": "10.0.1.10", "status": "running"},
        "web-02": {"ip": "10.0.1.11", "status": "stopped"}
    },
    "database": {
        "host": "db.example.com",
        "port": 5432,
        "ssl": True
    }
}

# Deep access
web01_status = server_config["web_servers"]["web-01"]["status"]
db_host = server_config["database"]["host"]
```

#### **JavaScript Objects:**
```javascript
// JavaScript - Object with flexible access
const config = {
    serviceName: "user-api",    // No quotes needed for simple keys
    port: 3000,
    sslEnabled: true,
    replicas: 3,
    environment: "production"
};

// Key access - BOTH dot and bracket notation work
const port = config.port;              // ✅ Preferred (clean)
const port2 = config["port"];          // ✅ Also works
const dynamicKey = "port";
const port3 = config[dynamicKey];      // ✅ Useful for dynamic keys

// Safe access methods
const timeout = config.timeout || 30;           // Returns 30 if undefined
const ssl = config.sslEnabled ?? false;        // Nullish coalescing

// Key operations
if (config.port !== undefined) {
    console.log(`Port configured: ${config.port}`);
}

// Modern methods
Object.keys(config).forEach(key => {
    console.log(`Key: ${key}`);
});

Object.entries(config).forEach(([key, value]) => {
    console.log(`${key}: ${value}`);
});

// Nested objects
const serverConfig = {
    webServers: {
        "web-01": {ip: "10.0.1.10", status: "running"},
        "web-02": {ip: "10.0.1.11", status: "stopped"}
    },
    database: {
        host: "db.example.com",
        port: 5432,
        ssl: true
    }
};

// Deep access - multiple ways
const web01Status = serverConfig.webServers["web-01"].status;  // Mixed notation
const dbHost = serverConfig.database.host;                     // Dot notation
const dbPort = serverConfig["database"]["port"];               // Bracket notation

// Destructuring (JavaScript-specific)
const {host, port: dbPort2, ssl} = serverConfig.database;
console.log(`DB: ${host}:${dbPort2}, SSL: ${ssl}`);
```

#### **Bash Associative Arrays:**
```bash
#!/bin/bash
# Bash - Associative arrays (maps) - Bash 4.0+
declare -A CONFIG
CONFIG[service_name]="user-api"
CONFIG[port]="3000"
CONFIG[ssl_enabled]="true"
CONFIG[replicas]="3"
CONFIG[environment]="production"

# Key access - bracket notation only
PORT=${CONFIG[port]}
SERVICE=${CONFIG[service_name]}

# Check if key exists
if [[ -v CONFIG[port] ]]; then
    echo "Port configured: ${CONFIG[port]}"
fi

# Default values
TIMEOUT=${CONFIG[timeout]:-30}  # Returns 30 if timeout not set

# Iterate over keys
for key in "${!CONFIG[@]}"; do
    echo "Key: $key, Value: ${CONFIG[$key]}"
done

# Limitation: No nested associative arrays in Bash
# Workaround: Use prefixed keys
declare -A SERVER_CONFIG
SERVER_CONFIG[web01_ip]="10.0.1.10"
SERVER_CONFIG[web01_status]="running"
SERVER_CONFIG[web02_ip]="10.0.1.11"
SERVER_CONFIG[web02_status]="stopped"
SERVER_CONFIG[db_host]="db.example.com"
SERVER_CONFIG[db_port]="5432"

# Access nested-like data
WEB01_STATUS=${SERVER_CONFIG[web01_status]}
DB_HOST=${SERVER_CONFIG[db_host]}
```

#### **Go Maps:**
```go
// Go - Strongly typed maps
config := map[string]interface{}{
    "service_name": "user-api",
    "port":         3000,
    "ssl_enabled":  true,
    "replicas":     3,
    "environment":  "production",
}

// Better: Use specific types when possible
portConfig := map[string]int{
    "http":  80,
    "https": 443,
    "app":   3000,
}

// Key access - bracket notation only
port, exists := config["port"]
if exists {
    fmt.Printf("Port configured: %v\n", port)
}

// Type assertion needed for interface{} values
if portInt, ok := port.(int); ok {
    fmt.Printf("Port as int: %d\n", portInt)
}

// Iterate over map
for key, value := range config {
    fmt.Printf("Key: %s, Value: %v\n", key, value)
}

// Nested maps
type ServerInfo struct {
    IP     string `json:"ip"`
    Status string `json:"status"`
}

serverConfig := map[string]map[string]ServerInfo{
    "web_servers": {
        "web-01": {IP: "10.0.1.10", Status: "running"},
        "web-02": {IP: "10.0.1.11", Status: "stopped"},
    },
}

// Deep access
web01Status := serverConfig["web_servers"]["web-01"].Status

// Better approach: Use structs for complex nested data
type Config struct {
    WebServers map[string]ServerInfo `json:"web_servers"`
    Database   DatabaseConfig        `json:"database"`
}

type DatabaseConfig struct {
    Host string `json:"host"`
    Port int    `json:"port"`
    SSL  bool   `json:"ssl"`
}
```

#### **YAML Objects/Maps:**
```yaml
# YAML - Structured configuration
config:
  service_name: user-api
  port: 3000
  ssl_enabled: true
  replicas: 3
  environment: production

# Nested objects
server_config:
  web_servers:
    web-01:
      ip: 10.0.1.10
      status: running
    web-02:
      ip: 10.0.1.11
      status: stopped
  database:
    host: db.example.com
    port: 5432
    ssl: true

# Mixed arrays and objects
services:
  - name: web-service
    instances:
      - host: web-01
        port: 80
      - host: web-02
        port: 80
  - name: api-service
    instances:
      - host: api-01
        port: 3000

# Inline object syntax
database_config: {host: localhost, port: 5432, ssl: false}
```

---

## 🔧 **When These Differences Matter in DevOps**

### **1. Configuration File Parsing:**

#### **Python:**
```python
import yaml
import json

# YAML parsing
with open('config.yml') as f:
    config = yaml.safe_load(f)
    
# Must use bracket notation for dynamic keys
environment = "production"
db_config = config["environments"][environment]["database"]
host = db_config["host"]  # Bracket notation required

# JSON parsing
with open('config.json') as f:
    config = json.load(f)
    port = config["services"]["api"]["port"]
```

#### **JavaScript:**
```javascript
const yaml = require('js-yaml');
const fs = require('fs');

// YAML parsing
const config = yaml.load(fs.readFileSync('config.yml', 'utf8'));

// Can use dot notation (cleaner)
const environment = "production";
const dbConfig = config.environments[environment].database;
const host = dbConfig.host;  // Dot notation works

// JSON parsing (native)
const config2 = JSON.parse(fs.readFileSync('config.json', 'utf8'));
const port = config2.services.api.port;
```

#### **Bash:**
```bash
#!/bin/bash
# Bash typically uses simple key=value or sources external files
source config.env

# Or parse JSON with jq
CONFIG_FILE="config.json"
DB_HOST=$(jq -r '.database.host' "$CONFIG_FILE")
API_PORT=$(jq -r '.services.api.port' "$CONFIG_FILE")

echo "Database: $DB_HOST"
echo "API Port: $API_PORT"
```

### **2. Environment Variable Handling:**

#### **Python:**
```python
import os

# Environment variables as dictionary
database_url = os.environ.get("DATABASE_URL", "localhost:5432")
debug_mode = os.environ.get("DEBUG", "false").lower() == "true"

# Type conversion needed
max_connections = int(os.environ.get("MAX_CONNECTIONS", "100"))
```

#### **JavaScript:**
```javascript
// Environment variables as object properties
const databaseUrl = process.env.DATABASE_URL || "localhost:5432";
const debugMode = process.env.DEBUG === "true";

// Type conversion needed
const maxConnections = parseInt(process.env.MAX_CONNECTIONS || "100");
```

#### **Bash:**
```bash
#!/bin/bash
# Environment variables as shell variables
DATABASE_URL=${DATABASE_URL:-"localhost:5432"}
DEBUG=${DEBUG:-"false"}
MAX_CONNECTIONS=${MAX_CONNECTIONS:-"100"}

# String comparison for booleans
if [[ "$DEBUG" == "true" ]]; then
    echo "Debug mode enabled"
fi
```

### **3. API Response Handling:**

#### **Python:**
```python
import requests

response = requests.get("/api/deployments")
data = response.json()

# Bracket notation required
for deployment in data["deployments"]:
    status = deployment["status"]
    service = deployment["service_name"]
    print(f"{service}: {status}")
```

#### **JavaScript:**
```javascript
const response = await fetch("/api/deployments");
const data = await response.json();

// Dot notation available (cleaner)
data.deployments.forEach(deployment => {
    const status = deployment.status;
    const service = deployment.service_name;
    console.log(`${service}: ${status}`);
});
```

---

## 🎯 **Language-Specific Best Practices**

### **Python:**
- ✅ Use `dict.get()` for safe key access
- ✅ Use list comprehensions for filtering/mapping
- ✅ Follow snake_case naming convention
- ✅ Use `in` operator for membership testing

### **JavaScript:**
- ✅ Use dot notation when possible (cleaner)
- ✅ Use `Array.prototype` methods (map, filter, reduce)
- ✅ Follow camelCase naming convention
- ✅ Use destructuring for cleaner code

### **Bash:**
- ✅ Quote all variable references: `"${VAR}"`
- ✅ Use arrays for lists, associative arrays for maps
- ✅ Follow UPPER_CASE convention for variables
- ✅ Check if variables are set: `[[ -v VAR ]]`

### **Go:**
- ✅ Use structs instead of `map[string]interface{}` when structure is known
- ✅ Always check the second return value for map access
- ✅ Use typed maps when possible
- ✅ Follow Go naming conventions (camelCase/PascalCase)

### **YAML:**
- ✅ Use consistent indentation (2 or 4 spaces)
- ✅ Follow snake_case for keys
- ✅ Use quotes for string values that might be ambiguous
- ✅ Validate YAML syntax regularly

---

**💡 Key Takeaway:** While all languages support similar data structures, the syntax and access patterns vary significantly. Understanding these differences helps you write idiomatic code in each language and avoid common pitfalls when switching between them in DevOps workflows. 