# 🐍 Complete Python Code Concepts Reference for DevOps

## 📖 What This File Does
Comprehensive reference covering every Python code concept you'll encounter in DevOps automation. Each section includes code examples, detailed explanations, and practical DevOps applications.

## 🎯 Learning Objectives
- Master every Python syntax element and concept
- Understand practical applications in DevOps scenarios
- Learn best practices for automation scripts
- Bridge theory with real-world implementation

---

## 🔧 **Import Statements and Module Management**

### **Basic Import Concepts**
```python
# Basic imports
import os
import sys
import subprocess
import json
import datetime
from pathlib import Path
from typing import List, Dict, Optional
```

**📖 What This Does**  
Import statements bring external functionality into your script. They load modules (collections of functions and classes) that extend Python's capabilities for file operations, system commands, data processing, and more.

**🔧 DevOps Applications**
- `import os` - File system operations, environment variables
- `import subprocess` - Running shell commands from Python
- `import json` - Configuration file parsing, API responses
- `from pathlib import Path` - Modern file path handling
- `from typing import List, Dict` - Type hints for better code documentation

**💡 Example Explanation**
```python
import subprocess
from pathlib import Path
import json

# Why we import these:
# subprocess - Run shell commands like 'docker build' or 'kubectl apply'
# pathlib - Handle file paths in a cross-platform way
# json - Parse configuration files and API responses

def deploy_application(config_file: str):
    """Deploy application using configuration file"""
    # Use pathlib for safe file handling
    config_path = Path(config_file)
    
    # Use json to parse configuration
    with open(config_path) as f:
        config = json.load(f)
    
    # Use subprocess to run deployment commands
    result = subprocess.run(f"docker build -t {config['app_name']} .", 
                          shell=True, capture_output=True, text=True)
    
    return result.returncode == 0
```

### **Advanced Import Patterns**
```python
# Conditional imports
try:
    import psutil  # Third-party library
except ImportError:
    psutil = None
    print("psutil not available - system monitoring disabled")

# Aliased imports
import subprocess as sp
import pandas as pd
from datetime import datetime as dt

# Selective imports
from os import environ, path, makedirs
from subprocess import run, PIPE, CalledProcessError
```

**📖 What This Does**  
Advanced import patterns handle optional dependencies, create shorter aliases for frequently used modules, and import only specific functions to reduce namespace pollution.

**🔧 DevOps Applications**
```python
# Real DevOps example with conditional imports
try:
    import docker  # Docker SDK
    DOCKER_AVAILABLE = True
except ImportError:
    DOCKER_AVAILABLE = False

try:
    import kubernetes  # Kubernetes client
    K8S_AVAILABLE = True
except ImportError:
    K8S_AVAILABLE = False

def deploy_container(image_name: str, method: str = "auto"):
    """Deploy container using available tools"""
    if method == "docker" and DOCKER_AVAILABLE:
        client = docker.from_env()
        return client.containers.run(image_name, detach=True)
    elif method == "k8s" and K8S_AVAILABLE:
        # Kubernetes deployment logic
        pass
    else:
        # Fall back to subprocess
        import subprocess
        return subprocess.run(f"docker run -d {image_name}", 
                            shell=True, capture_output=True)
```

---

## 🔄 **Function Definitions and Structure**

### **Basic Function Definition**
```python
def function_name(parameter1, parameter2="default_value"):
    """Function docstring explaining what it does"""
    # Function body
    result = parameter1 + parameter2
    return result
```

**📖 What This Does**  
Functions encapsulate reusable code logic. The `def` keyword starts a function definition, followed by the function name, parameters in parentheses, and a colon. The indented block contains the function's code.

**🔧 DevOps Applications**
```python
def restart_service(service_name: str, timeout: int = 30) -> bool:
    """Restart a system service with timeout"""
    import subprocess
    
    try:
        # Stop the service
        subprocess.run(f"systemctl stop {service_name}", 
                      shell=True, check=True, timeout=timeout)
        
        # Start the service
        subprocess.run(f"systemctl start {service_name}", 
                      shell=True, check=True, timeout=timeout)
        
        # Verify it's running
        result = subprocess.run(f"systemctl is-active {service_name}", 
                              shell=True, capture_output=True, text=True)
        
        return result.stdout.strip() == "active"
        
    except subprocess.CalledProcessError:
        return False
    except subprocess.TimeoutExpired:
        print(f"Timeout restarting {service_name}")
        return False

# Usage
if restart_service("nginx", timeout=60):
    print("✅ Nginx restarted successfully")
else:
    print("❌ Failed to restart nginx")
```

### **Advanced Function Features**
```python
def advanced_function(*args, **kwargs):
    """Function with variable arguments"""
    print(f"Positional args: {args}")
    print(f"Keyword args: {kwargs}")

def typed_function(servers: List[str], config: Dict[str, str]) -> Optional[str]:
    """Function with type hints"""
    for server in servers:
        if server not in config:
            return f"Missing config for {server}"
    return None

# Lambda functions (anonymous functions)
filter_running = lambda containers: [c for c in containers if c.status == "running"]
```

**📖 What This Does**  
Advanced function features include variable arguments (`*args`, `**kwargs`), type hints for better documentation, and lambda functions for simple operations.

**🔧 DevOps Applications**
```python
def deploy_to_servers(*server_names: str, **deploy_config) -> Dict[str, bool]:
    """Deploy to multiple servers with flexible configuration"""
    results = {}
    
    # Default configuration
    default_config = {
        "timeout": 300,
        "rollback_on_failure": True,
        "health_check": True
    }
    
    # Merge with provided config
    config = {**default_config, **deploy_config}
    
    for server in server_names:
        print(f"Deploying to {server} with config: {config}")
        
        try:
            # Deployment logic here
            success = deploy_single_server(server, config)
            results[server] = success
            
            if not success and config["rollback_on_failure"]:
                rollback_server(server)
                
        except Exception as e:
            print(f"Deployment failed on {server}: {e}")
            results[server] = False
    
    return results

# Usage examples
deploy_to_servers("web-01", "web-02", timeout=600, health_check=False)
deploy_to_servers("db-01", rollback_on_failure=False)
```

---

## 🔀 **Control Flow: If Statements and Conditionals**

### **Basic If Statements**
```python
if condition:
    # Execute if condition is True
    action()
elif another_condition:
    # Execute if first is False but this is True
    another_action()
else:
    # Execute if all conditions are False
    default_action()
```

**📖 What This Does**  
If statements control program flow based on conditions. Python evaluates conditions and executes different code blocks based on whether conditions are True or False.

**🔧 DevOps Applications**
```python
def check_system_health():
    """Check system health and take appropriate actions"""
    import psutil
    
    # Check CPU usage
    cpu_percent = psutil.cpu_percent(interval=1)
    
    if cpu_percent > 90:
        print("🚨 CRITICAL: CPU usage is very high!")
        send_alert("High CPU usage", cpu_percent)
        scale_up_resources()
    elif cpu_percent > 70:
        print("⚠️  WARNING: CPU usage is elevated")
        log_warning(f"CPU usage: {cpu_percent}%")
    else:
        print("✅ CPU usage is normal")
    
    # Check memory
    memory = psutil.virtual_memory()
    
    if memory.percent > 85:
        print("🚨 CRITICAL: Memory usage is high!")
        cleanup_temp_files()
        restart_memory_intensive_services()
    elif memory.percent > 70:
        print("⚠️  Memory usage is getting high")
    else:
        print("✅ Memory usage is normal")
    
    # Check disk space
    disk = psutil.disk_usage('/')
    disk_percent = (disk.used / disk.total) * 100
    
    if disk_percent > 95:
        print("🚨 CRITICAL: Disk space almost full!")
        emergency_cleanup()
    elif disk_percent > 80:
        print("⚠️  Disk space getting low")
        scheduled_cleanup()
    else:
        print("✅ Disk space is adequate")

def send_alert(message: str, value: float):
    """Send alert to monitoring system"""
    # Implementation depends on your monitoring system
    pass
```

### **Complex Conditional Logic**
```python
# Multiple conditions with logical operators
if condition1 and condition2:
    # Both must be True
    pass

if condition1 or condition2:
    # At least one must be True
    pass

if not condition:
    # Condition must be False
    pass

# Membership testing
if item in collection:
    pass

if value not in forbidden_values:
    pass

# Existence checking
if variable is None:
    pass

if variable is not None:
    pass
```

**📖 What This Does**  
Complex conditionals combine multiple conditions using logical operators (`and`, `or`, `not`) and test for membership (`in`) or existence (`is None`).

**🔧 DevOps Applications**
```python
def should_deploy(environment: str, tests_passed: bool, approval_received: bool, 
                 maintenance_window: bool) -> bool:
    """Determine if deployment should proceed"""
    
    # Production deployments need all conditions
    if environment == "production":
        if tests_passed and approval_received and maintenance_window:
            return True
        else:
            print("❌ Production deployment blocked:")
            if not tests_passed:
                print("  - Tests have not passed")
            if not approval_received:
                print("  - Deployment approval missing")
            if not maintenance_window:
                print("  - Not in maintenance window")
            return False
    
    # Development and staging are more flexible
    elif environment in ["development", "staging"]:
        if tests_passed:
            return True
        else:
            print(f"⚠️  {environment} deployment without tests")
            return True  # Allow but warn
    
    else:
        print(f"❌ Unknown environment: {environment}")
        return False

# Usage
deploy_ready = should_deploy(
    environment="production",
    tests_passed=True,
    approval_received=True,
    maintenance_window=False
)

if deploy_ready:
    print("🚀 Starting deployment...")
    start_deployment()
else:
    print("⏸️  Deployment postponed")
```

---

## 🔄 **Loops: For and While**

### **For Loops**
```python
# Iterate over a list
for item in list_of_items:
    process(item)

# Iterate with index
for index, item in enumerate(list_of_items):
    print(f"Item {index}: {item}")

# Iterate over dictionary
for key, value in dictionary.items():
    print(f"{key}: {value}")

# Range-based loops
for i in range(10):  # 0 to 9
    print(i)

for i in range(1, 11):  # 1 to 10
    print(i)
```

**📖 What This Does**  
For loops iterate over sequences (lists, strings, ranges) or collections (dictionaries, sets). They execute the same code block for each item in the collection.

**🔧 DevOps Applications**
```python
def deploy_to_multiple_servers(servers: List[str], application_version: str):
    """Deploy application to multiple servers"""
    deployment_results = {}
    
    for index, server in enumerate(servers, 1):
        print(f"📦 Deploying to server {index}/{len(servers)}: {server}")
        
        try:
            # Deploy to single server
            result = deploy_to_server(server, application_version)
            deployment_results[server] = {
                "success": True,
                "version": application_version,
                "timestamp": datetime.now().isoformat()
            }
            print(f"✅ {server} deployment successful")
            
        except Exception as e:
            deployment_results[server] = {
                "success": False,
                "error": str(e),
                "timestamp": datetime.now().isoformat()
            }
            print(f"❌ {server} deployment failed: {e}")
    
    # Summary report
    successful = sum(1 for result in deployment_results.values() if result["success"])
    total = len(servers)
    
    print(f"\n📊 Deployment Summary: {successful}/{total} servers successful")
    
    # List failed deployments
    failed_servers = [server for server, result in deployment_results.items() 
                     if not result["success"]]
    
    if failed_servers:
        print("❌ Failed servers:")
        for server in failed_servers:
            error = deployment_results[server]["error"]
            print(f"  - {server}: {error}")
    
    return deployment_results

# Process configuration files
def update_config_files(config_dir: str, updates: Dict[str, str]):
    """Update multiple configuration files"""
    config_path = Path(config_dir)
    
    for config_file in config_path.glob("*.yml"):
        print(f"🔧 Updating {config_file.name}")
        
        with open(config_file, 'r') as f:
            content = f.read()
        
        # Apply updates
        for old_value, new_value in updates.items():
            content = content.replace(old_value, new_value)
        
        with open(config_file, 'w') as f:
            f.write(content)
        
        print(f"✅ {config_file.name} updated")
```

### **While Loops**
```python
while condition:
    # Execute while condition is True
    do_something()
    # Make sure to modify condition or use break
```

**📖 What This Does**  
While loops continue executing as long as a condition remains True. They're useful for waiting, retrying operations, or processing until a specific state is reached.

**🔧 DevOps Applications**
```python
def wait_for_service_ready(service_url: str, max_attempts: int = 30, 
                          delay: int = 10) -> bool:
    """Wait for service to become ready"""
    import requests
    import time
    
    attempts = 0
    
    while attempts < max_attempts:
        try:
            print(f"🔍 Checking service health (attempt {attempts + 1}/{max_attempts})")
            
            response = requests.get(f"{service_url}/health", timeout=5)
            
            if response.status_code == 200:
                print("✅ Service is ready!")
                return True
            else:
                print(f"⚠️  Service returned status {response.status_code}")
                
        except requests.exceptions.RequestException as e:
            print(f"⚠️  Service not ready: {e}")
        
        attempts += 1
        
        if attempts < max_attempts:
            print(f"⏳ Waiting {delay} seconds before next check...")
            time.sleep(delay)
    
    print(f"❌ Service failed to become ready after {max_attempts} attempts")
    return False

def monitor_deployment_progress(deployment_id: str):
    """Monitor deployment until completion"""
    import time
    
    status = "running"
    
    while status == "running":
        # Check deployment status
        status = get_deployment_status(deployment_id)
        
        if status == "running":
            print("🔄 Deployment in progress...")
            time.sleep(30)  # Check every 30 seconds
        elif status == "completed":
            print("✅ Deployment completed successfully!")
            break
        elif status == "failed":
            print("❌ Deployment failed!")
            break
        else:
            print(f"⚠️  Unknown deployment status: {status}")
            break
    
    return status
```

---

## 📊 **Data Structures and Variables**

### **Variable Assignment and Types**
```python
# Basic variable assignment
name = "web-server"
port = 8080
is_running = True
servers = ["web-01", "web-02", "db-01"]
config = {"host": "localhost", "port": 5432}

# Multiple assignment
x, y, z = 1, 2, 3
host, port = "localhost", 8080

# Augmented assignment
counter += 1
path += "/subdirectory"
servers.append("new-server")
```

**📖 What This Does**  
Variables store data that can be used throughout your program. Python automatically determines the type based on the value assigned. Variables can be reassigned to different values and types.

**🔧 DevOps Applications**
```python
def setup_deployment_environment():
    """Setup deployment environment variables and configuration"""
    
    # Environment configuration
    environment = "production"
    region = "us-east-1"
    cluster_name = f"{environment}-cluster-{region}"
    
    # Server configuration
    web_servers = ["web-01", "web-02", "web-03"]
    database_servers = ["db-primary", "db-replica-01", "db-replica-02"]
    cache_servers = ["redis-01", "redis-02"]
    
    # Combine all servers
    all_servers = web_servers + database_servers + cache_servers
    
    # Configuration dictionary
    deployment_config = {
        "environment": environment,
        "region": region,
        "cluster": cluster_name,
        "servers": {
            "web": web_servers,
            "database": database_servers,
            "cache": cache_servers
        },
        "scaling": {
            "min_instances": 2,
            "max_instances": 10,
            "target_cpu": 70
        }
    }
    
    # Resource limits
    memory_limit_mb = 2048
    cpu_limit_cores = 2.0
    disk_limit_gb = 50
    
    # Update limits based on environment
    if environment == "production":
        memory_limit_mb *= 2  # 4GB for production
        cpu_limit_cores *= 2  # 4 cores for production
        disk_limit_gb *= 4   # 200GB for production
    
    return deployment_config, memory_limit_mb, cpu_limit_cores, disk_limit_gb
```

### **Lists and List Operations**
```python
# Creating lists
servers = ["web-01", "web-02", "db-01"]

# Adding items
servers.append("cache-01")  # Add to end
servers.insert(0, "load-balancer")  # Insert at position
servers.extend(["api-01", "api-02"])  # Add multiple

# Removing items
servers.remove("cache-01")  # Remove by value
popped = servers.pop()  # Remove and return last
del servers[0]  # Remove by index

# List comprehensions
running_servers = [s for s in servers if is_running(s)]
server_ips = [get_ip(server) for server in servers]
```

**📖 What This Does**  
Lists store ordered collections of items. They're mutable (can be changed) and support various operations for adding, removing, and processing items.

**🔧 DevOps Applications**
```python
def manage_server_inventory():
    """Manage dynamic server inventory"""
    
    # Initial server list
    servers = ["web-01", "web-02", "db-01"]
    
    # Add new servers based on load
    if current_load() > 80:
        new_servers = [f"web-{i:02d}" for i in range(3, 6)]  # web-03, web-04, web-05
        servers.extend(new_servers)
        print(f"🚀 Scaled up: Added {new_servers}")
    
    # Remove failed servers
    failed_servers = [server for server in servers if not health_check(server)]
    
    for failed_server in failed_servers:
        servers.remove(failed_server)
        print(f"❌ Removed failed server: {failed_server}")
    
    # Group servers by type
    web_servers = [s for s in servers if s.startswith("web")]
    db_servers = [s for s in servers if s.startswith("db")]
    
    # Get server details
    server_details = []
    for server in servers:
        details = {
            "name": server,
            "ip": get_server_ip(server),
            "status": get_server_status(server),
            "cpu_usage": get_cpu_usage(server),
            "memory_usage": get_memory_usage(server)
        }
        server_details.append(details)
    
    # Sort by CPU usage (highest first)
    server_details.sort(key=lambda x: x["cpu_usage"], reverse=True)
    
    return {
        "all_servers": servers,
        "web_servers": web_servers,
        "db_servers": db_servers,
        "server_details": server_details,
        "total_count": len(servers)
    }
```

### **Dictionaries and Dictionary Operations**
```python
# Creating dictionaries
config = {"host": "localhost", "port": 5432}

# Accessing values
host = config["host"]  # Direct access
port = config.get("port", 8080)  # With default

# Adding/updating
config["database"] = "myapp"
config.update({"user": "admin", "ssl": True})

# Dictionary methods
keys = config.keys()
values = config.values()
items = config.items()

# Dictionary comprehensions
env_vars = {k: v for k, v in os.environ.items() if k.startswith("APP_")}
```

**📖 What This Does**  
Dictionaries store key-value pairs, like configuration settings or mappings. They provide fast lookup by key and are essential for structured data in DevOps.

**🔧 DevOps Applications**
```python
def manage_application_config():
    """Manage application configuration across environments"""
    
    # Base configuration
    base_config = {
        "app_name": "myapp",
        "version": "1.0.0",
        "debug": False,
        "database": {
            "engine": "postgresql",
            "port": 5432,
            "ssl": True
        },
        "cache": {
            "engine": "redis",
            "port": 6379,
            "ttl": 3600
        }
    }
    
    # Environment-specific overrides
    environment_configs = {
        "development": {
            "debug": True,
            "database": {
                "host": "localhost",
                "name": "myapp_dev"
            },
            "cache": {
                "host": "localhost"
            }
        },
        "staging": {
            "database": {
                "host": "staging-db.company.com",
                "name": "myapp_staging"
            },
            "cache": {
                "host": "staging-cache.company.com"
            }
        },
        "production": {
            "database": {
                "host": "prod-db.company.com",
                "name": "myapp_prod",
                "pool_size": 20
            },
            "cache": {
                "host": "prod-cache.company.com",
                "cluster": True
            },
            "monitoring": {
                "enabled": True,
                "metrics_endpoint": "/metrics"
            }
        }
    }
    
    def get_config_for_environment(env: str) -> Dict:
        """Get merged configuration for specific environment"""
        if env not in environment_configs:
            raise ValueError(f"Unknown environment: {env}")
        
        # Start with base config
        config = base_config.copy()
        
        # Deep merge environment-specific config
        env_config = environment_configs[env]
        
        for key, value in env_config.items():
            if key in config and isinstance(config[key], dict) and isinstance(value, dict):
                # Merge nested dictionaries
                config[key] = {**config[key], **value}
            else:
                # Override or add new key
                config[key] = value
        
        return config
    
    # Generate configs for all environments
    configs = {}
    for env in environment_configs.keys():
        configs[env] = get_config_for_environment(env)
    
    return configs

# Usage
configs = manage_application_config()
prod_config = configs["production"]
print(f"Production database host: {prod_config['database']['host']}")
```

---

## ⚡ **Subprocess and System Commands**

### **Basic Subprocess Usage**
```python
import subprocess

# Simple command execution
result = subprocess.run("ls -la", shell=True)

# Capture output
result = subprocess.run("ls -la", shell=True, capture_output=True, text=True)

# Check for errors
result = subprocess.run("command", shell=True, check=True)
```

**📖 What This Does**  
The subprocess module allows Python to run shell commands and system programs. It provides control over input/output, error handling, and process management.

**🔧 DevOps Applications**
```python
def execute_deployment_commands():
    """Execute a series of deployment commands with proper error handling"""
    
    commands = [
        ("git pull origin main", "Update code from repository"),
        ("docker build -t myapp:latest .", "Build Docker image"),
        ("docker tag myapp:latest myapp:v1.0.0", "Tag image with version"),
        ("docker push myapp:v1.0.0", "Push image to registry"),
        ("kubectl set image deployment/myapp myapp=myapp:v1.0.0", "Update Kubernetes deployment")
    ]
    
    for command, description in commands:
        print(f"🔄 {description}")
        print(f"   Command: {command}")
        
        try:
            result = subprocess.run(
                command,
                shell=True,
                capture_output=True,
                text=True,
                check=True,  # Raises exception on non-zero exit
                timeout=300  # 5 minute timeout
            )
            
            print(f"✅ Success: {description}")
            if result.stdout:
                print(f"   Output: {result.stdout.strip()}")
                
        except subprocess.CalledProcessError as e:
            print(f"❌ Failed: {description}")
            print(f"   Exit code: {e.returncode}")
            print(f"   Error: {e.stderr}")
            return False
            
        except subprocess.TimeoutExpired:
            print(f"⏰ Timeout: {description}")
            return False
    
    print("🎉 All deployment commands completed successfully!")
    return True
```

### **Advanced Subprocess Patterns**
```python
# Real-time output
process = subprocess.Popen(
    command,
    shell=True,
    stdout=subprocess.PIPE,
    stderr=subprocess.STDOUT,
    universal_newlines=True
)

for line in process.stdout:
    print(line.strip())

# Custom environment
env = os.environ.copy()
env["CUSTOM_VAR"] = "value"
result = subprocess.run(command, env=env)

# Input to process
result = subprocess.run(
    "command",
    input="data to send",
    text=True,
    capture_output=True
)
```

**📖 What This Does**  
Advanced subprocess patterns provide real-time output monitoring, environment variable control, and input/output redirection for complex automation scenarios.

**🔧 DevOps Applications**
```python
def monitor_long_running_deployment():
    """Monitor a long-running deployment with real-time output"""
    
    def run_with_live_output(command: str, description: str) -> bool:
        """Run command and show output in real-time"""
        print(f"🚀 Starting: {description}")
        
        process = subprocess.Popen(
            command,
            shell=True,
            stdout=subprocess.PIPE,
            stderr=subprocess.STDOUT,
            universal_newlines=True,
            bufsize=1  # Line buffered
        )
        
        output_lines = []
        
        try:
            for line in process.stdout:
                line = line.strip()
                if line:
                    print(f"   {line}")
                    output_lines.append(line)
            
            process.wait()
            
            if process.returncode == 0:
                print(f"✅ Completed: {description}")
                return True
            else:
                print(f"❌ Failed: {description} (exit code: {process.returncode})")
                return False
                
        except KeyboardInterrupt:
            print(f"⏹️  Interrupted: {description}")
            process.terminate()
            return False
    
    # Execute deployment steps with live monitoring
    steps = [
        ("terraform apply -auto-approve", "Provisioning infrastructure"),
        ("ansible-playbook -i inventory deploy.yml", "Configuring servers"),
        ("docker-compose up -d", "Starting services")
    ]
    
    for command, description in steps:
        if not run_with_live_output(command, description):
            print("💥 Deployment failed, stopping execution")
            return False
    
    print("🎉 Deployment completed successfully!")
    return True

def run_with_custom_environment(script_path: str, environment_vars: Dict[str, str]):
    """Run script with custom environment variables"""
    
    # Start with current environment
    env = os.environ.copy()
    
    # Add custom variables
    env.update(environment_vars)
    
    # Add script directory to PATH
    script_dir = Path(script_path).parent
    env["PATH"] = f"{script_dir}:{env.get('PATH', '')}"
    
    print(f"🔧 Running script with custom environment:")
    for key, value in environment_vars.items():
        print(f"   {key}={value}")
    
    result = subprocess.run(
        f"python {script_path}",
        shell=True,
        env=env,
        capture_output=True,
        text=True
    )
    
    if result.returncode == 0:
        print("✅ Script completed successfully")
        return result.stdout
    else:
        print(f"❌ Script failed: {result.stderr}")
        return None

# Usage
custom_env = {
    "ENVIRONMENT": "production",
    "API_KEY": "secret-key",
    "DATABASE_URL": "postgresql://user:pass@host:5432/db"
}

output = run_with_custom_environment("deploy.py", custom_env)
```

---

## 🖨️ **Print Statements and Output**

### **Basic Print Usage**
```python
print("Hello, World!")
print("Value:", variable)
print(f"Formatted: {variable}")
print("Multiple", "values", "separated", "by", "commas")
print("Custom separator", "values", sep=" | ")
print("No newline", end="")
```

**📖 What This Does**  
Print statements output text and variables to the console. They're essential for debugging, logging, and providing user feedback in scripts.

**🔧 DevOps Applications**
```python
def deployment_status_reporter():
    """Report deployment status with formatted output"""
    
    # Deployment info
    app_name = "myapp"
    version = "v2.1.0"
    environment = "production"
    timestamp = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    
    # Print deployment header
    print("=" * 60)
    print(f"🚀 DEPLOYMENT REPORT")
    print("=" * 60)
    print(f"Application: {app_name}")
    print(f"Version: {version}")
    print(f"Environment: {environment}")
    print(f"Timestamp: {timestamp}")
    print("-" * 60)
    
    # Server status with custom formatting
    servers = [
        {"name": "web-01", "status": "✅ Running", "cpu": 45.2, "memory": 67.8},
        {"name": "web-02", "status": "✅ Running", "cpu": 52.1, "memory": 71.3},
        {"name": "db-01", "status": "⚠️  High Load", "cpu": 89.7, "memory": 82.1}
    ]
    
    print("SERVER STATUS:")
    print(f"{'Name':<10} {'Status':<15} {'CPU %':<8} {'Memory %':<10}")
    print("-" * 50)
    
    for server in servers:
        print(f"{server['name']:<10} {server['status']:<15} {server['cpu']:<8.1f} {server['memory']:<10.1f}")
    
    # Summary with color-coded output
    total_servers = len(servers)
    healthy_servers = sum(1 for s in servers if "Running" in s["status"])
    
    print("-" * 60)
    print(f"SUMMARY: {healthy_servers}/{total_servers} servers healthy")
    
    if healthy_servers == total_servers:
        print("🎉 All systems operational!")
    else:
        print("⚠️  Some servers need attention")
    
    print("=" * 60)

def log_deployment_progress(step: str, status: str, details: str = ""):
    """Log deployment progress with consistent formatting"""
    timestamp = datetime.now().strftime("%H:%M:%S")
    
    status_icons = {
        "start": "🔄",
        "success": "✅",
        "error": "❌",
        "warning": "⚠️",
        "info": "ℹ️"
    }
    
    icon = status_icons.get(status, "•")
    
    print(f"[{timestamp}] {icon} {step}", end="")
    
    if details:
        print(f" - {details}")
    else:
        print()

# Usage examples
log_deployment_progress("Starting deployment", "start")
log_deployment_progress("Building Docker image", "success", "Image built in 45s")
log_deployment_progress("Database migration", "warning", "Some warnings encountered")
log_deployment_progress("Deployment complete", "success")
```

### **Advanced Print Formatting**
```python
# String formatting methods
name = "server-01"
cpu = 75.5
memory = 82.3

# F-strings (preferred)
print(f"Server {name}: CPU {cpu:.1f}%, Memory {memory:.1f}%")

# .format() method
print("Server {}: CPU {:.1f}%, Memory {:.1f}%".format(name, cpu, memory))

# % formatting (old style)
print("Server %s: CPU %.1f%%, Memory %.1f%%" % (name, cpu, memory))

# Advanced formatting
print(f"{'Server':<15} {'CPU %':<8} {'Memory %':<10}")
print(f"{name:<15} {cpu:<8.1f} {memory:<10.1f}")
```

**📖 What This Does**  
Advanced formatting creates aligned, professional-looking output with precise control over decimal places, padding, and alignment.

**🔧 DevOps Applications**
```python
def generate_resource_usage_report(servers_data: List[Dict]):
    """Generate formatted resource usage report"""
    
    print("\n" + "=" * 80)
    print(f"{'RESOURCE USAGE REPORT':.^80}")
    print("=" * 80)
    
    # Table header
    print(f"{'Server Name':<20} {'CPU %':<8} {'Memory %':<10} {'Disk %':<8} {'Network I/O':<15} {'Status':<10}")
    print("-" * 80)
    
    # Server data rows
    for server in servers_data:
        name = server['name']
        cpu = server['cpu_percent']
        memory = server['memory_percent']
        disk = server['disk_percent']
        network = f"{server['network_mb']:.1f} MB/s"
        
        # Determine status based on thresholds
        if cpu > 90 or memory > 90 or disk > 95:
            status = "🚨 CRITICAL"
        elif cpu > 70 or memory > 80 or disk > 85:
            status = "⚠️  WARNING"
        else:
            status = "✅ HEALTHY"
        
        print(f"{name:<20} {cpu:<8.1f} {memory:<10.1f} {disk:<8.1f} {network:<15} {status:<10}")
    
    # Summary statistics
    avg_cpu = sum(s['cpu_percent'] for s in servers_data) / len(servers_data)
    avg_memory = sum(s['memory_percent'] for s in servers_data) / len(servers_data)
    avg_disk = sum(s['disk_percent'] for s in servers_data) / len(servers_data)
    
    print("-" * 80)
    print(f"{'AVERAGES:':<20} {avg_cpu:<8.1f} {avg_memory:<10.1f} {avg_disk:<8.1f}")
    print("=" * 80)
    
    # Recommendations
    print("\n📋 RECOMMENDATIONS:")
    
    high_cpu_servers = [s for s in servers_data if s['cpu_percent'] > 80]
    if high_cpu_servers:
        print(f"• Scale up CPU for: {', '.join(s['name'] for s in high_cpu_servers)}")
    
    high_memory_servers = [s for s in servers_data if s['memory_percent'] > 85]
    if high_memory_servers:
        print(f"• Add memory to: {', '.join(s['name'] for s in high_memory_servers)}")
    
    high_disk_servers = [s for s in servers_data if s['disk_percent'] > 90]
    if high_disk_servers:
        print(f"• Clean up disk space on: {', '.join(s['name'] for s in high_disk_servers)}")
    
    if not (high_cpu_servers or high_memory_servers or high_disk_servers):
        print("• All systems operating within normal parameters ✅")

# Example usage
sample_data = [
    {"name": "web-01", "cpu_percent": 45.2, "memory_percent": 67.8, "disk_percent": 34.5, "network_mb": 12.3},
    {"name": "web-02", "cpu_percent": 52.1, "memory_percent": 71.3, "disk_percent": 41.2, "network_mb": 15.7},
    {"name": "db-01", "cpu_percent": 89.7, "memory_percent": 82.1, "disk_percent": 78.9, "network_mb": 45.2}
]

generate_resource_usage_report(sample_data)
```

---

## 🎯 **Next Steps and Advanced Topics**

This reference covers the fundamental Python concepts you'll use in DevOps automation. For advanced topics, explore:

- **Async/Await**: For concurrent operations
- **Context Managers**: For resource management
- **Decorators**: For function enhancement
- **Classes and OOP**: For complex automation frameworks
- **Error Handling**: For robust production scripts
- **Testing**: For reliable automation code

---

📄 **File Path:** `/DevOps/01-Fundamentals_And_Environment_Setup/04-Python_For_DevOps/Python_Basics/04-Complete_Python_Code_Concepts_Reference.md` 