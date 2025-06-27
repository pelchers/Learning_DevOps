# 🐍 Python Special Characters and Symbols for DevOps

## 📖 What This File Does
This guide explains what all the special characters and symbols mean in Python scripting, with a focus on DevOps automation, infrastructure management, and system administration tasks.

## 🎯 Learning Objectives
- Understand Python-specific symbol meanings
- Learn DevOps automation patterns using Python symbols
- Master string formatting, file operations, and system interactions
- Compare Python symbols with terminal/shell equivalents

## 📋 Prerequisites
- Basic understanding of terminal symbols (see Linux navigation guide)
- Python installed and accessible
- Text editor or IDE (VS Code, Cursor, etc.)

---

## 🔤 **Python Special Characters and Symbols**

### **Variable and Assignment Symbols**

#### **= - Assignment**
```python
# = assigns values to variables
server_name = "web-server-01"
port = 8080
is_running = True
servers = ["web1", "web2", "db1"]

# DevOps examples
config_file = "/etc/nginx/nginx.conf"
backup_dir = "/var/backups"
api_key = "your-secret-api-key"
deployment_status = "success"
```

**📖 What This Does**
The assignment operator `=` stores values in variables. Unlike mathematics, this means "assign the value on the right to the variable on the left." The variable can then be used throughout your script.

**🔧 Configuration Notes**
- Variable names should use snake_case in Python (server_name, not serverName)
- Variables are created the moment you assign to them
- Python variables can change type: `x = 5` then `x = "hello"` is valid
- Use descriptive names for DevOps scripts: `backup_path` not `bp`

#### **== - Equality Comparison**
```python
# == checks if values are equal
if server_status == "running":
    print("Server is online")

if port == 80:
    protocol = "http"
elif port == 443:
    protocol = "https"

# DevOps examples
if environment == "production":
    enable_monitoring = True

if exit_code == 0:
    print("Command executed successfully")
else:
    print(f"Command failed with exit code: {exit_code}")
```

#### **!= - Not Equal**
```python
# != checks if values are NOT equal
if status != "healthy":
    send_alert("Service is down!")

if cpu_usage != 0:
    print(f"CPU usage: {cpu_usage}%")

# DevOps examples
if response_code != 200:
    print("API request failed")
    
if disk_usage != "0%":
    check_disk_space()
```

### **String Manipulation Symbols**

#### **" and ' - String Quotes**
```python
# Single quotes for simple strings
server = 'web-01'
command = 'systemctl status nginx'

# Double quotes for strings with apostrophes
message = "Server's status is healthy"
sql_query = "SELECT * FROM users WHERE name='John'"

# Triple quotes for multi-line strings
dockerfile_content = """
FROM ubuntu:20.04
RUN apt-get update && apt-get install -y nginx
COPY index.html /var/www/html/
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
"""

# DevOps script examples
backup_script = '''
#!/bin/bash
echo "Starting backup..."
tar -czf backup-$(date +%Y%m%d).tar.gz /var/www/
echo "Backup completed"
'''
```

#### **f"" - F-Strings (Formatted Strings)**
```python
# f-strings embed variables in strings
server_name = "web-01"
cpu_usage = 75.5
print(f"Server {server_name} CPU usage: {cpu_usage}%")

# DevOps examples
timestamp = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
log_message = f"[{timestamp}] Deployment completed on {server_name}"

backup_filename = f"backup-{server_name}-{datetime.now().strftime('%Y%m%d')}.tar.gz"
api_url = f"https://api.{environment}.company.com/v1/status"

# Complex f-string formatting
memory_gb = 8192 / 1024  # Convert MB to GB
print(f"Memory: {memory_gb:.2f} GB")  # Shows: Memory: 8.00 GB
```

**📖 What This Does**
F-strings (formatted string literals) are the modern Python way to embed variables and expressions directly into strings. They're faster and more readable than older string formatting methods.

**🔧 Configuration Notes**
- F-strings require Python 3.6 or newer
- Use `{variable:.2f}` for decimal places, `{variable:>10}` for alignment
- Can execute any Python expression inside `{}`
- Essential for creating dynamic log messages, API URLs, and file names in DevOps

#### **\ - Escape Character**
```python
# \ escapes special characters in strings
file_path = "C:\\Users\\Admin\\Documents"  # Windows path
json_string = "{\"name\": \"server\", \"status\": \"running\"}"
shell_command = "echo \"Hello World\""

# Multi-line string continuation
long_command = "docker run -d " \
               "--name web-server " \
               "--port 8080:80 " \
               "nginx:latest"

# DevOps examples
regex_pattern = "\\d{4}-\\d{2}-\\d{2}"  # Date pattern YYYY-MM-DD
escaped_sql = "INSERT INTO logs VALUES ('Error: Can\\'t connect')"
```

#### **+ - String Concatenation**
```python
# + joins strings together
first_name = "John"
last_name = "Doe"
full_name = first_name + " " + last_name

# DevOps examples
base_url = "https://api.example.com"
endpoint = "/v1/servers"
full_url = base_url + endpoint

log_prefix = "[ERROR]"
error_message = "Database connection failed"
full_log = log_prefix + " " + error_message
```

### **List and Data Structure Symbols**

#### **[] - Lists and Indexing**
```python
# [] creates lists
servers = ["web-01", "web-02", "db-01"]
ports = [80, 443, 22, 3306]
environments = ["dev", "staging", "production"]

# [] accesses list items by index (starts at 0)
first_server = servers[0]        # "web-01"
last_server = servers[-1]       # "db-01" (negative index from end)

# List slicing
web_servers = servers[0:2]       # ["web-01", "web-02"]
all_except_first = servers[1:]   # ["web-02", "db-01"]

# DevOps examples
production_servers = ["prod-web-01", "prod-web-02", "prod-db-01"]
backup_targets = ["/var/www", "/etc", "/home"]
monitoring_metrics = ["cpu", "memory", "disk", "network"]

# Access specific servers
primary_web = production_servers[0]
database_server = production_servers[-1]
```

**📖 What This Does**
Square brackets `[]` create lists (ordered collections) and access items by their position. Lists are fundamental for storing multiple related items like server names, configuration values, or monitoring data.

**🔧 Configuration Notes**
- Python lists start counting from 0 (first item is `[0]`)
- Negative indices count from the end: `[-1]` is last item, `[-2]` is second-to-last
- Slicing `[start:end]` includes start but excludes end
- Lists are mutable - you can modify them after creation
- Use lists for ordered data like server sequences or processing steps

#### **{} - Dictionaries**
```python
# {} creates dictionaries (key-value pairs)
server_info = {
    "hostname": "web-01",
    "ip": "192.168.1.10",
    "port": 80,
    "status": "running"
}

# Access dictionary values
hostname = server_info["hostname"]
ip_address = server_info["ip"]

# DevOps configuration examples
aws_config = {
    "region": "us-east-1",
    "instance_type": "t3.medium",
    "key_pair": "my-keypair",
    "security_groups": ["web-sg", "ssh-sg"]
}

database_config = {
    "host": "db.example.com",
    "port": 5432,
    "database": "production",
    "username": "admin",
    "password": "secret123"
}

# Nested dictionaries
infrastructure = {
    "web_servers": {
        "count": 3,
        "type": "t3.medium",
        "ports": [80, 443]
    },
    "database": {
        "engine": "postgresql",
        "version": "13.7",
        "port": 5432
    }
}
```

#### **() - Tuples and Function Calls**
```python
# () creates tuples (immutable lists)
coordinates = (40.7128, -74.0060)  # NYC coordinates
server_endpoint = ("192.168.1.10", 8080)

# () calls functions
print("Hello World")
len(servers)
max(ports)

# DevOps function examples
import subprocess
result = subprocess.run(["ls", "-la"], capture_output=True, text=True)
os.path.join("/var", "www", "html")
datetime.now().strftime("%Y-%m-%d")

# Function definitions
def restart_service(service_name):
    command = f"systemctl restart {service_name}"
    return subprocess.run(command.split(), capture_output=True)

def backup_database(db_name, backup_path):
    timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
    backup_file = f"{backup_path}/{db_name}_{timestamp}.sql"
    return backup_file
```

### **Comparison and Logic Symbols**

#### **< > <= >= - Comparison Operators**
```python
# Numerical comparisons
cpu_usage = 85
memory_usage = 67

if cpu_usage > 90:
    send_alert("High CPU usage!")
elif cpu_usage >= 70:
    print("CPU usage is elevated")

if memory_usage < 80:
    print("Memory usage is normal")

# DevOps monitoring examples
disk_usage = 92
if disk_usage >= 95:
    alert_level = "critical"
elif disk_usage >= 85:
    alert_level = "warning"
else:
    alert_level = "normal"

# String comparisons (alphabetical)
environment = "production"
if environment > "development":  # True - "p" comes after "d"
    enable_strict_monitoring = True
```

#### **and, or, not - Logical Operators**
```python
# and - both conditions must be true
if cpu_usage > 80 and memory_usage > 80:
    print("System is under heavy load")

# or - at least one condition must be true  
if port == 80 or port == 443:
    protocol = "web"

# not - negates the condition
if not is_service_running:
    start_service()

# DevOps examples
if environment == "production" and cpu_usage > 90:
    trigger_auto_scaling()

if disk_usage > 95 or memory_usage > 95:
    send_critical_alert()

if not backup_exists and is_business_hours:
    run_backup_job()

# Complex conditions
if (environment == "prod" or environment == "staging") and not maintenance_mode:
    enable_monitoring()
```

### **Arithmetic and Math Symbols**

#### **+ - * / % ** - Math Operations**
```python
# Basic math operations
total_servers = web_servers + db_servers    # Addition
memory_per_server = total_memory / server_count  # Division
cpu_cores = servers * cores_per_server      # Multiplication
storage_tb = storage_gb / 1024              # Convert GB to TB

# Modulo (remainder)
if server_id % 2 == 0:
    server_type = "even"
else:
    server_type = "odd"

# Exponentiation
storage_bytes = storage_gb * (1024 ** 3)    # Convert GB to bytes

# DevOps calculations
uptime_hours = uptime_seconds / 3600
cpu_percentage = (cpu_used / cpu_total) * 100
memory_available_gb = memory_available_mb / 1024

# Load balancing
server_index = request_count % len(servers)  # Round-robin selection
backup_interval_hours = backup_interval_minutes / 60
```

### **File and Path Symbols**

#### **/ - Path Separator (Unix/Linux)**
```python
# Unix/Linux path separator
config_path = "/etc/nginx/nginx.conf"
log_path = "/var/log/nginx/access.log"
backup_path = "/var/backups/database"

# Using os.path.join for cross-platform paths
import os
config_file = os.path.join("/etc", "nginx", "nginx.conf")
log_directory = os.path.join("/var", "log", "application")

# DevOps path examples
docker_socket = "/var/run/docker.sock"
ssl_cert_path = "/etc/ssl/certs/server.crt"
deployment_script = "/opt/scripts/deploy.sh"
```

**📖 What This Does**
The forward slash `/` separates directories in Unix/Linux file paths. In Python strings, it's used to build file paths that work on Linux servers where most DevOps tools run.

**🔧 Configuration Notes**
- Always use forward slashes for Linux/Unix paths in DevOps scripts
- Use `os.path.join()` or `pathlib.Path()` for cross-platform compatibility
- Raw strings `r"/path/to/file"` can help avoid escape character issues
- Remember that most DevOps infrastructure runs on Linux, so `/` is standard

#### **\\ - Windows Path Separator**
```python
# Windows path separator (needs escaping)
windows_config = "C:\\Program Files\\MyApp\\config.ini"
windows_logs = "C:\\Windows\\Logs\\Application.log"

# Raw strings for Windows paths (r prefix)
raw_path = r"C:\Program Files\MyApp\config.ini"
network_share = r"\\server\shared\backups"

# Cross-platform path handling
import os
app_config = os.path.join(os.path.expanduser("~"), "app", "config.json")
```

**📖 What This Does**
The backslash `\\` is the Windows directory separator. In Python strings, backslashes must be escaped (doubled) or you must use raw strings with the `r` prefix to prevent Python from interpreting them as escape characters.

**🔧 Configuration Notes**
- Use `\\\\` in regular strings or `r""` raw strings for Windows paths
- Prefer `os.path.join()` or `pathlib.Path()` for cross-platform scripts
- Network shares use `\\\\server\\share` format
- Most DevOps tools run on Linux, but you may need Windows paths for local development

### **Import and Module Symbols**

#### **. - Dot Notation**
```python
# . accesses attributes and methods
import datetime
current_time = datetime.datetime.now()

# . accesses object methods
server_name = "WEB-SERVER-01"
lowercase_name = server_name.lower()
server_parts = server_name.split("-")

# . for module imports
import os.path
file_exists = os.path.exists("/etc/hosts")

# DevOps examples
import subprocess
result = subprocess.run(["systemctl", "status", "nginx"])
output = result.stdout.decode()

import json
config_data = json.loads(config_string)
config_json = json.dumps(config_dict, indent=2)
```

#### **from ... import - Selective Imports**
```python
# Import specific functions
from datetime import datetime, timedelta
from os import environ, path
from subprocess import run, PIPE

# DevOps specific imports
from flask import Flask, request, jsonify
from boto3 import client
from kubernetes import client as k8s_client

# Usage examples
now = datetime.now()
env_var = environ.get("DATABASE_URL")
file_exists = path.exists("/etc/nginx/nginx.conf")
```

### **Loop and Control Symbols**

#### **: - Colon (Code Blocks)**
```python
# : starts code blocks
if cpu_usage > 90:
    send_alert()
    restart_service()

for server in servers:
    check_status(server)
    update_monitoring(server)

while service_status != "running":
    time.sleep(5)
    service_status = check_service()

# DevOps examples
def deploy_application(environment):
    if environment == "production":
        run_tests()
        backup_database()
        deploy_code()
    else:
        deploy_code()

class ServerManager:
    def __init__(self, servers):
        self.servers = servers
    
    def restart_all(self):
        for server in self.servers:
            self.restart_server(server)
```

#### **in - Membership Testing**
```python
# in checks if item exists in collection
if "nginx" in installed_services:
    print("Nginx is installed")

if server_name in production_servers:
    enable_monitoring()

# DevOps examples
if environment in ["production", "staging"]:
    enable_ssl = True

if error_code in [500, 502, 503, 504]:
    alert_type = "server_error"

# Check file extensions
if log_file.endswith((".log", ".txt")):
    process_log_file(log_file)

# Dictionary membership
if "database_url" in config:
    db_connection = config["database_url"]
```

### **Exception Handling Symbols**

#### **try/except/finally - Error Handling**
```python
# Basic exception handling
try:
    response = requests.get(api_url)
    data = response.json()
except requests.exceptions.RequestException as e:
    print(f"API request failed: {e}")
    data = None
finally:
    print("Request attempt completed")

# DevOps exception handling
def backup_database():
    try:
        # Attempt database backup
        run(["pg_dump", "mydb", "-f", "backup.sql"], check=True)
        print("Backup completed successfully")
    except subprocess.CalledProcessError as e:
        print(f"Backup failed: {e}")
        send_alert("Database backup failed")
    except FileNotFoundError:
        print("pg_dump command not found")
        install_postgresql_client()
    finally:
        cleanup_temp_files()

# File operations with exception handling
try:
    with open("/etc/nginx/nginx.conf", "r") as config_file:
        config_content = config_file.read()
except FileNotFoundError:
    print("Nginx config file not found")
    create_default_config()
except PermissionError:
    print("Permission denied reading config file")
```

### **Lambda and Advanced Symbols**

#### **lambda - Anonymous Functions**
```python
# lambda creates small anonymous functions
servers = ["web-01", "web-02", "db-01"]
web_servers = list(filter(lambda x: x.startswith("web"), servers))

# Sort servers by number
servers_numbered = ["server-3", "server-1", "server-10", "server-2"]
sorted_servers = sorted(servers_numbered, key=lambda x: int(x.split("-")[1]))

# DevOps examples
# Filter running containers
containers = [
    {"name": "web1", "status": "running"},
    {"name": "web2", "status": "stopped"},
    {"name": "db1", "status": "running"}
]
running_containers = list(filter(lambda c: c["status"] == "running", containers))

# Calculate memory usage percentage
memory_stats = [{"used": 4096, "total": 8192}, {"used": 2048, "total": 4096}]
memory_percentages = list(map(lambda m: (m["used"] / m["total"]) * 100, memory_stats))
```

#### **@ - Decorators**
```python
# @ applies decorators to functions
import time
from functools import wraps

def timer(func):
    @wraps(func)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(f"{func.__name__} took {end - start:.2f} seconds")
        return result
    return wrapper

@timer
def backup_database():
    # Database backup code here
    time.sleep(2)  # Simulate backup time

# DevOps decorators
def retry(max_attempts=3):
    def decorator(func):
        @wraps(func)
        def wrapper(*args, **kwargs):
            for attempt in range(max_attempts):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    if attempt == max_attempts - 1:
                        raise e
                    print(f"Attempt {attempt + 1} failed, retrying...")
                    time.sleep(2 ** attempt)  # Exponential backoff
            return wrapper
    return decorator

@retry(max_attempts=3)
def deploy_service():
    # Deployment code that might fail
    pass
```

### **Type Hinting Symbols**

#### **-> and : - Type Hints**
```python
# Type hints for better code documentation
def restart_service(service_name: str) -> bool:
    """Restart a system service"""
    try:
        subprocess.run(["systemctl", "restart", service_name], check=True)
        return True
    except subprocess.CalledProcessError:
        return False

def get_server_info(server_id: int) -> dict:
    """Get server information by ID"""
    return {
        "id": server_id,
        "hostname": f"server-{server_id:03d}",
        "status": "running"
    }

# DevOps type hints
from typing import List, Dict, Optional

def deploy_to_servers(servers: List[str], config: Dict[str, str]) -> Optional[str]:
    """Deploy application to multiple servers"""
    for server in servers:
        success = deploy_to_server(server, config)
        if not success:
            return f"Deployment failed on {server}"
    return None  # Success

# Complex type hints
from typing import Union, Tuple

def parse_server_response(response: str) -> Union[Dict[str, str], Tuple[int, str]]:
    """Parse server response, return dict on success or error tuple"""
    try:
        return json.loads(response)
    except json.JSONDecodeError as e:
        return (400, f"Invalid JSON: {e}")
```

---

## 🔧 **DevOps-Specific Python Patterns**

### **Configuration Management**
```python
# Environment-specific configuration
import os
from typing import Dict

class Config:
    def __init__(self):
        self.environment = os.environ.get("ENVIRONMENT", "development")
        self.database_url = os.environ.get("DATABASE_URL")
        self.api_key = os.environ.get("API_KEY")
        self.debug = self.environment != "production"
    
    def get_db_config(self) -> Dict[str, str]:
        return {
            "host": os.environ.get("DB_HOST", "localhost"),
            "port": os.environ.get("DB_PORT", "5432"),
            "database": os.environ.get("DB_NAME", "app_db"),
            "username": os.environ.get("DB_USER", "admin"),
            "password": os.environ.get("DB_PASSWORD", "")
        }

# Usage
config = Config()
if config.environment == "production":
    enable_monitoring()
```

### **Infrastructure as Code**
```python
# AWS infrastructure with boto3
import boto3

def create_ec2_instance(instance_type: str = "t3.micro") -> str:
    """Create EC2 instance and return instance ID"""
    ec2 = boto3.client("ec2")
    
    response = ec2.run_instances(
        ImageId="ami-0abcdef1234567890",  # Amazon Linux 2
        MinCount=1,
        MaxCount=1,
        InstanceType=instance_type,
        KeyName="my-key-pair",
        SecurityGroupIds=["sg-12345678"],
        TagSpecifications=[
            {
                "ResourceType": "instance",
                "Tags": [
                    {"Key": "Name", "Value": f"web-server-{instance_type}"},
                    {"Key": "Environment", "Value": "production"},
                    {"Key": "Project", "Value": "web-app"}
                ]
            }
        ]
    )
    
    instance_id = response["Instances"][0]["InstanceId"]
    print(f"Created instance: {instance_id}")
    return instance_id
```

### **Monitoring and Alerting**
```python
# System monitoring script
import psutil
import requests
from datetime import datetime

def collect_system_metrics() -> Dict[str, float]:
    """Collect system performance metrics"""
    return {
        "cpu_percent": psutil.cpu_percent(interval=1),
        "memory_percent": psutil.virtual_memory().percent,
        "disk_percent": psutil.disk_usage("/").percent,
        "network_sent": psutil.net_io_counters().bytes_sent,
        "network_recv": psutil.net_io_counters().bytes_recv
    }

def send_metrics_to_monitoring(metrics: Dict[str, float]):
    """Send metrics to monitoring system"""
    payload = {
        "timestamp": datetime.now().isoformat(),
        "hostname": os.uname().nodename,
        "metrics": metrics
    }
    
    try:
        response = requests.post(
            "https://monitoring.example.com/api/metrics",
            json=payload,
            timeout=30
        )
        response.raise_for_status()
    except requests.exceptions.RequestException as e:
        print(f"Failed to send metrics: {e}")

# Usage
if __name__ == "__main__":
    metrics = collect_system_metrics()
    
    # Check for alerts
    if metrics["cpu_percent"] > 90:
        send_alert("High CPU usage detected!")
    
    if metrics["disk_percent"] > 95:
        send_alert("Disk space critically low!")
    
    send_metrics_to_monitoring(metrics)
```

### **Log Processing**
```python
# Log analysis and processing
import re
from collections import defaultdict
from datetime import datetime

def parse_nginx_log(log_line: str) -> Dict[str, str]:
    """Parse nginx access log line"""
    pattern = r'(\S+) - - \[(.*?)\] "(\S+) (\S+) (\S+)" (\d+) (\d+) "(.*?)" "(.*?)"'
    match = re.match(pattern, log_line)
    
    if match:
        return {
            "ip": match.group(1),
            "timestamp": match.group(2),
            "method": match.group(3),
            "url": match.group(4),
            "protocol": match.group(5),
            "status_code": int(match.group(6)),
            "response_size": int(match.group(7)),
            "referer": match.group(8),
            "user_agent": match.group(9)
        }
    return {}

def analyze_logs(log_file: str):
    """Analyze nginx logs for patterns"""
    status_counts = defaultdict(int)
    ip_counts = defaultdict(int)
    error_count = 0
    
    with open(log_file, "r") as f:
        for line in f:
            parsed = parse_nginx_log(line.strip())
            if parsed:
                status_counts[parsed["status_code"]] += 1
                ip_counts[parsed["ip"]] += 1
                
                if parsed["status_code"] >= 400:
                    error_count += 1
    
    # Report findings
    print(f"Total errors (4xx/5xx): {error_count}")
    print("\nTop 10 IP addresses:")
    for ip, count in sorted(ip_counts.items(), key=lambda x: x[1], reverse=True)[:10]:
        print(f"  {ip}: {count} requests")
    
    print("\nStatus code distribution:")
    for status, count in sorted(status_counts.items()):
        print(f"  {status}: {count}")
```

---

## 🔧 **Variable and Assignment Symbols**

### **= - Assignment**
```python
# = assigns values to variables
server_name = "web-server-01"
port = 8080
is_running = True
servers = ["web1", "web2", "db1"]

# DevOps examples
config_file = "/etc/nginx/nginx.conf"
backup_dir = "/var/backups"
api_key = "your-secret-api-key"
deployment_status = "success"
```

**📖 What This Does**  
The assignment operator `=` stores values in variables. Unlike mathematics, this means "assign the value on the right to the variable on the left." The variable can then be used throughout your script.

**🔧 Configuration Notes**
- Variable names should use snake_case in Python (server_name, not serverName)
- Variables are created the moment you assign to them
- Python variables can change type: `x = 5` then `x = "hello"` is valid
- Use descriptive names for DevOps scripts: `backup_path` not `bp`

### **== - Equality Comparison**
```python
# == checks if values are equal
if server_status == "running":
    print("Server is online")

if port == 80:
    protocol = "http"
elif port == 443:
    protocol = "https"

# DevOps examples
if environment == "production":
    enable_monitoring = True

if exit_code == 0:
    print("Command executed successfully")
else:
    print(f"Command failed with exit code: {exit_code}")
```

**📖 What This Does**  
The equality operator `==` compares two values and returns `True` if they are the same, `False` if they are different. This is different from assignment (`=`) - it's asking "are these equal?" rather than "make this equal to that."

**🔧 Configuration Notes**
- Use `==` for comparison, `=` for assignment
- Works with strings, numbers, lists, and other data types
- Case-sensitive for strings: `"Production" == "production"` is `False`
- Essential for conditional logic in deployment scripts and monitoring

### **!= - Not Equal**
```python
# != checks if values are NOT equal
if status != "healthy":
    send_alert("Service is down!")

if cpu_usage != 0:
    print(f"CPU usage: {cpu_usage}%")

# DevOps examples
if response_code != 200:
    print("API request failed")
    
if disk_usage != "0%":
    check_disk_space()
```

**📖 What This Does**  
The not-equal operator `!=` compares two values and returns `True` if they are different, `False` if they are the same. It's the opposite of `==` - asking "are these NOT equal?"

**🔧 Configuration Notes**
- Use `!=` to check if values are different
- Very useful for error checking and validation
- Common in monitoring: `if status != "healthy"`
- Helps avoid negative logic: use `!=` instead of `not value ==`

### **+= - Add and Assign**
```python
# += adds to existing value
error_count = 0
error_count += 1  # Same as: error_count = error_count + 1

# String concatenation
log_message = "Starting deployment"
log_message += " on production server"  # "Starting deployment on production server"

# List extension
failed_servers = []
failed_servers += ["web-01", "web-02"]

# DevOps examples
total_memory = 0
for server in servers:
    total_memory += server.memory_gb

deployment_log = "Deployment started\n"
deployment_log += f"Timestamp: {datetime.now()}\n"
deployment_log += f"Environment: {environment}\n"
```

**📖 What This Does**  
The add-and-assign operator `+=` adds a value to a variable and stores the result back in that variable. It's a shortcut for `variable = variable + value`. Works with numbers (addition), strings (concatenation), and lists (extension).

**🔧 Configuration Notes**
- Shorthand for `x = x + y` becomes `x += y`
- For strings: concatenates (joins) text together
- For numbers: performs mathematical addition
- For lists: extends the list with new items
- Very common in DevOps for building log messages and accumulating data

### **-= *= /= //= %= **= - Compound Assignment**
```python
# -= subtract and assign
remaining_capacity = 100
remaining_capacity -= used_capacity

# *= multiply and assign
cpu_cores = 4
cpu_cores *= 2  # Double the cores

# /= divide and assign
total_cost = 1000
total_cost /= 12  # Monthly cost

# DevOps monitoring examples
response_time = 500  # milliseconds
response_time /= 1000  # Convert to seconds

disk_space_gb = 1024
disk_space_gb //= 1024  # Convert to TB (integer division)

retry_delay = 1
retry_delay *= 2  # Exponential backoff: 1, 2, 4, 8 seconds
```

**📖 What This Does**  
Compound assignment operators combine a mathematical operation with assignment. They modify a variable by performing an operation with another value and storing the result back in the original variable.

**🔧 Configuration Notes**
- `-=` subtracts: `x -= 5` means `x = x - 5`
- `*=` multiplies: `x *= 2` means `x = x * 2`
- `/=` divides: `x /= 3` means `x = x / 3`
- `//=` floor divides: `x //= 4` means `x = x // 4` (integer division)
- `%=` modulo: `x %= 10` means `x = x % 10` (remainder)
- `**=` exponentiation: `x **= 2` means `x = x ** 2` (power)
- Common in DevOps for calculations, conversions, and scaling operations

---

## 🔤 **String Manipulation Symbols**

### **f"" - F-Strings (Formatted Strings)**
```python
# f-strings embed variables in strings
server_name = "web-01"
cpu_usage = 75.5
print(f"Server {server_name} CPU usage: {cpu_usage}%")

# DevOps examples
timestamp = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
log_message = f"[{timestamp}] Deployment completed on {server_name}"

backup_filename = f"backup-{server_name}-{datetime.now().strftime('%Y%m%d')}.tar.gz"
api_url = f"https://api.{environment}.company.com/v1/status"

# Complex f-string formatting
memory_gb = 8192 / 1024  # Convert MB to GB
print(f"Memory: {memory_gb:.2f} GB")  # Shows: Memory: 8.00 GB

# DevOps monitoring alerts
alert_message = f"""
🚨 ALERT: High CPU Usage
Server: {server_name}
CPU: {cpu_usage:.1f}%
Threshold: {threshold}%
Time: {timestamp}
Action Required: {"Yes" if cpu_usage > 90 else "No"}
"""
```

**📖 What This Does**  
F-strings (formatted string literals) are the modern Python way to embed variables and expressions directly into strings. They're faster and more readable than older string formatting methods.

**🔧 Configuration Notes**
- F-strings require Python 3.6 or newer
- Use `{variable:.2f}` for decimal places, `{variable:>10}` for alignment
- Can execute any Python expression inside `{}`
- Essential for creating dynamic log messages, API URLs, and file names in DevOps

---

## 🔢 **Arithmetic and Comparison Symbols**

### **+ - * / // % ** - Arithmetic Operators**
```python
# Basic arithmetic
total_servers = 5 + 3  # Addition: 8
remaining_servers = 10 - 2  # Subtraction: 8
total_cores = 4 * 8  # Multiplication: 32 cores
cpu_per_server = 100 / 4  # Division: 25.0

# DevOps calculations
total_memory_gb = servers_count * memory_per_server  # 10 * 16 = 160 GB
cost_per_hour = total_cost / hours_in_month  # $500 / 730 = $0.68/hour

# Floor division (//) - integer division
full_days = total_hours // 24  # 25 hours = 1 full day
tb_storage = total_gb // 1024  # Convert GB to TB

# Modulus (%) - remainder
remaining_hours = total_hours % 24  # 25 % 24 = 1 hour remaining
server_id = request_number % total_servers  # Load balancing

# Exponentiation (**)
storage_bytes = storage_gb * (1024 ** 3)  # GB to bytes
backup_size = original_size * (2 ** compression_ratio)

# DevOps monitoring examples
cpu_threshold = 80.0
memory_threshold = 75.0
alert_score = (cpu_usage / cpu_threshold) + (memory_usage / memory_threshold)
```

**📖 What This Does**  
Arithmetic operators perform mathematical calculations on numbers. They follow standard mathematical rules and are essential for DevOps calculations like resource usage, capacity planning, and performance metrics.

**🔧 Configuration Notes**
- `+` addition: combines numbers or concatenates strings
- `-` subtraction: finds the difference between numbers
- `*` multiplication: multiplies numbers together
- `/` division: divides and returns a decimal (float) result
- `//` floor division: divides and returns only the whole number part
- `%` modulo: returns the remainder after division
- `**` exponentiation: raises a number to a power
- Very common in DevOps for metrics calculation, unit conversion, and resource allocation

### **< > <= >= - Comparison Operators**
```python
# Comparison operators return True or False
if cpu_usage > 80:
    print("High CPU usage detected")

if memory_available < 1024:  # Less than 1GB
    trigger_alert("Low memory warning")

if disk_usage >= 90:  # Greater than or equal to 90%
    cleanup_old_files()

if response_time <= 200:  # Less than or equal to 200ms
    status = "healthy"

# DevOps monitoring logic
if load_average > cpu_cores:
    scale_up_servers()

if error_rate >= 0.05:  # 5% or higher error rate
    rollback_deployment()

if uptime < 99.9:  # Below 99.9% uptime SLA
    investigate_downtime()

# Chained comparisons
if 50 <= cpu_usage <= 80:  # Between 50% and 80%
    status = "normal"
elif cpu_usage > 80:
    status = "high"
else:
    status = "low"
```

**📖 What This Does**  
Comparison operators compare two values and return `True` or `False`. They're used to make decisions in your code based on whether conditions are met, like checking if CPU usage is too high or if a server response time is acceptable.

**🔧 Configuration Notes**
- `<` less than: checks if left value is smaller
- `>` greater than: checks if left value is larger
- `<=` less than or equal: checks if left value is smaller or same
- `>=` greater than or equal: checks if left value is larger or same
- Can chain comparisons: `50 <= cpu_usage <= 80`
- Essential for monitoring thresholds, resource limits, and conditional deployments
- Works with strings (alphabetical order) and numbers

---

## 🔗 **Logical and Boolean Symbols**

### **and or not - Logical Operators**
```python
# and - both conditions must be True
if cpu_usage > 80 and memory_usage > 75:
    print("System under heavy load")
    
if server_status == "running" and response_code == 200:
    health_status = "healthy"

# or - at least one condition must be True
if server_status == "stopped" or server_status == "error":
    restart_server()

if environment == "development" or environment == "testing":
    enable_debug_logging()

# not - reverses the boolean value
if not server_is_healthy:
    send_alert("Server health check failed")

if not backup_exists:
    create_backup()

# Complex DevOps conditions
if (environment == "production" and cpu_usage > 90) or (memory_usage > 95):
    trigger_emergency_scale()

# Checking multiple server statuses
if server1_status == "healthy" and server2_status == "healthy" and server3_status == "healthy":
    cluster_status = "operational"
else:
    cluster_status = "degraded"

# Security checks
if user_authenticated and user_authorized and not security_breach:
    allow_deployment()
```

**📖 What This Does**  
Logical operators combine multiple conditions together to make complex decisions. They let you check multiple things at once, like "is the server running AND is the response time good?" or "is it development OR testing environment?"

**🔧 Configuration Notes**
- `and`: both conditions must be `True` for the result to be `True`
- `or`: at least one condition must be `True` for the result to be `True`
- `not`: reverses `True` to `False` and `False` to `True`
- Use parentheses for complex conditions: `(a and b) or (c and d)`
- Essential for multi-factor checks in deployment and monitoring logic
- More readable than nested if statements

### **is is not - Identity Operators**
```python
# is checks if variables reference the same object
server_config = None
if server_config is None:
    load_default_config()

# is not checks if variables are different objects
if deployment_status is not None:
    print(f"Deployment status: {deployment_status}")

# DevOps examples
if error_message is not None:
    log_error(error_message)

if backup_process is None:
    backup_process = start_backup()

# Boolean checks
if monitoring_enabled is True:  # Usually just: if monitoring_enabled:
    start_monitoring()

if auto_scaling is False:  # Usually just: if not auto_scaling:
    manual_scaling_required = True

# Checking for specific values
empty_list = []
if servers_list is not empty_list:  # Different from: if servers_list != []
    process_servers(servers_list)
```

**📖 What This Does**  
Identity operators check whether two variables refer to the exact same object in memory, not just whether they have the same value. Most commonly used to check if something is `None` (empty/undefined).

**🔧 Configuration Notes**
- `is`: checks if two variables are the exact same object
- `is not`: checks if two variables are different objects
- Different from `==`: `is` checks identity, `==` checks value
- Use `is None` and `is not None` for checking undefined values
- Essential for checking if configuration, connections, or processes exist
- More precise than equality when checking for `None`, `True`, or `False`

### **in not in - Membership Operators**
```python
# in checks if value exists in sequence
if "web-01" in active_servers:
    print("Web server is active")

if environment in ["production", "staging"]:
    enable_ssl = True

# not in checks if value doesn't exist
if server_name not in failed_servers:
    deploy_to_server(server_name)

# DevOps examples
critical_services = ["nginx", "mysql", "redis"]
if service_name in critical_services:
    priority = "high"

error_keywords = ["error", "fail", "exception", "timeout"]
if any(keyword in log_message.lower() for keyword in error_keywords):
    alert_level = "critical"

# Checking file extensions
allowed_extensions = [".py", ".yml", ".yaml", ".json"]
if file_extension not in allowed_extensions:
    reject_file_upload()

# Environment validation
valid_environments = {"development", "testing", "staging", "production"}
if environment not in valid_environments:
    raise ValueError(f"Invalid environment: {environment}")
```

**📖 What This Does**  
Membership operators check whether a value exists inside a collection (list, string, set, etc.). They're perfect for checking if something is in a list of valid options or if text contains certain keywords.

**🔧 Configuration Notes**
- `in`: returns `True` if value is found in the collection
- `not in`: returns `True` if value is NOT found in the collection
- Works with lists, strings, sets, dictionaries (checks keys)
- Very efficient for validation and filtering
- Essential for checking valid environments, services, file types
- Case-sensitive for strings: use `.lower()` for case-insensitive checks

---

## 🌊 **Control Flow Symbols**

### **: - Colon for Code Blocks**
```python
# if statements
if cpu_usage > 80:
    print("High CPU usage detected")
    send_alert("CPU threshold exceeded")
    
# elif and else
if environment == "production":
    log_level = "WARNING"
elif environment == "staging":
    log_level = "INFO"
else:
    log_level = "DEBUG"

# for loops
for server in server_list:
    health_status = check_health(server)
    print(f"Server {server}: {health_status}")

# while loops
retry_count = 0
while retry_count < 3:
    try:
        response = api_call()
        break
    except ConnectionError:
        retry_count += 1
        time.sleep(2 ** retry_count)  # Exponential backoff

# Function definitions
def restart_service(service_name):
    print(f"Restarting {service_name}...")
    # restart logic here
    return True

# Class definitions
class Server:
    def __init__(self, name, ip):
        self.name = name
        self.ip = ip
        self.status = "unknown"
    
    def check_health(self):
        # health check logic
        return self.status == "running"

# DevOps automation examples
def deploy_to_servers(servers, application_version):
    deployment_results = {}
    
    for server in servers:
        try:
            result = deploy_application(server, application_version)
            deployment_results[server] = "success"
        except DeploymentError as e:
            deployment_results[server] = f"failed: {e}"
            
    return deployment_results
```

**📖 What This Does**  
The colon `:` marks the start of a code block in Python. Unlike other languages that use curly braces `{}`, Python uses colons and indentation to group code together. Everything indented after the colon belongs to that block.

**🔧 Configuration Notes**
- Always put a colon after `if`, `for`, `while`, `def`, `class` statements
- The code block after the colon must be indented (usually 4 spaces)
- All lines at the same indentation level belong to the same block
- Missing colons are a common syntax error
- Essential for all control structures in Python
- Consistent indentation is required - mixing tabs and spaces causes errors

---

## 🔍 **Advanced Symbols and Operators**

### **@ - Decorator Symbol**
```python
# Decorators modify or enhance functions
import functools
import time
from flask import Flask, request, jsonify
from datetime import datetime

def retry_on_failure(max_retries=3):
    def decorator(func):
        @functools.wraps(func)
        def wrapper(*args, **kwargs):
            for attempt in range(max_retries):
                try:
                    return func(*args, **kwargs)
                except Exception as e:
                    if attempt == max_retries - 1:
                        raise e
                    time.sleep(2 ** attempt)
            return None
        return wrapper
    return decorator

def timer(func):
    """Decorator to measure function execution time"""
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(f"{func.__name__} took {end - start:.2f} seconds")
        return result
    return wrapper

def log_calls(func):
    """Decorator to log function calls"""
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        print(f"Calling {func.__name__} with args: {args}, kwargs: {kwargs}")
        result = func(*args, **kwargs)
        print(f"{func.__name__} returned: {result}")
        return result
    return wrapper

def require_auth(func):
    """Decorator for authentication checking"""
    @functools.wraps(func)
    def wrapper(*args, **kwargs):
        auth_token = request.headers.get('Authorization')
        if not auth_token or not validate_token(auth_token):
            return jsonify({'error': 'Authentication required'}), 401
        return func(*args, **kwargs)
    return wrapper

# Using decorators
@retry_on_failure(max_retries=3)
@timer
def deploy_application(server_name):
    """Deployment logic that might fail"""
    print(f"Deploying to {server_name}")
    # Simulate deployment work
    time.sleep(2)
    return True

@log_calls
@timer
def backup_database(db_name):
    """Backup database with logging"""
    print(f"Backing up {db_name}")
    return f"backup_{db_name}_{datetime.now().strftime('%Y%m%d')}.sql"

# Flask application with decorators
app = Flask(__name__)

@app.route('/health')
def health_check():
    """Basic health check endpoint"""
    return {"status": "healthy", "timestamp": time.time()}

@app.route('/servers/<server_id>')
@require_auth
def get_server_info(server_id):
    """Get server info - requires authentication"""
    return {"server": server_id, "status": "running"}

@app.route('/deploy', methods=['POST'])
@require_auth
@timer
def deploy_endpoint():
    """Deploy application endpoint"""
    data = request.get_json()
    server = data.get('server')
    result = deploy_application(server)
    return jsonify({'success': result, 'server': server})

# Error handler decorators
@app.errorhandler(404)
def not_found(error):
    """Handle 404 errors"""
    return jsonify({
        'error': 'Resource not found',
        'message': 'The requested endpoint does not exist',
        'status_code': 404
    }), 404

@app.errorhandler(500)
def internal_error(error):
    """Handle 500 errors"""
    return jsonify({
        'error': 'Internal server error',
        'message': 'Something went wrong on our end',
        'status_code': 500
    }), 500

@app.errorhandler(400)
def bad_request(error):
    """Handle 400 errors"""
    return jsonify({
        'error': 'Bad request',
        'message': 'The request was invalid',
        'status_code': 400
    }), 400

@app.before_request
def before_request():
    """Run before every request"""
    request.start_time = time.time()
    print(f"Request started: {request.method} {request.path}")

@app.after_request
def after_request(response):
    """Run after every request"""
    if hasattr(request, 'start_time'):
        duration = time.time() - request.start_time
        print(f"Request completed in {duration:.3f}s")
    return response

# Property decorators
class Server:
    def __init__(self, name, cpu_cores=4):
        self._name = name
        self._cpu_cores = cpu_cores
        self._status = "stopped"
    
    @property
    def name(self):
        """Get server name"""
        return self._name
    
    @property
    def status(self):
        """Get server status"""
        return self._status
    
    @status.setter
    def status(self, value):
        """Set server status with validation"""
        valid_statuses = ["running", "stopped", "error", "maintenance"]
        if value not in valid_statuses:
            raise ValueError(f"Invalid status: {value}")
        self._status = value
        print(f"Server {self._name} status changed to {value}")
    
    @property
    def cpu_usage(self):
        """Calculate CPU usage percentage"""
        if self._status == "running":
            return 75.5  # Simulated CPU usage
        return 0.0
    
    @classmethod
    def from_config(cls, config_dict):
        """Create server from configuration dictionary"""
        return cls(config_dict['name'], config_dict.get('cpu_cores', 4))
    
    @staticmethod
    def validate_server_name(name):
        """Validate server naming convention"""
        import re
        pattern = r'^[a-z]+-\d{2}$'  # e.g., web-01, db-03
        return re.match(pattern, name) is not None

# Using property decorators
server = Server("web-01")
server.status = "running"
print(f"CPU usage: {server.cpu_usage}%")

# Class method usage
config = {"name": "db-01", "cpu_cores": 8}
db_server = Server.from_config(config)

# Static method usage
is_valid = Server.validate_server_name("web-01")  # True
```

**📖 What This Does**  
The decorator symbol `@` applies a function that modifies or enhances another function. Decorators add extra functionality like retry logic, timing, logging, or authentication without changing the original function's code.

**🔧 Configuration Notes**
- Place `@decorator_name` on the line directly above the function
- Can stack multiple decorators: `@retry`, `@timer`, `@authenticate`
- Common in DevOps for retry logic, performance monitoring, and error handling
- Flask/Django use decorators for web routes: `@app.route('/health')`
- Makes code more modular and reusable
- Advanced topic, but very powerful for automation scripts

### **-> - Type Hints (Function Return Type)**
```python
# Type hints help with code documentation and IDE support
from typing import List, Dict, Optional, Tuple, Union, Any, Callable
import subprocess
from datetime import datetime

# Basic type hints
def get_server_list() -> List[str]:
    """Returns a list of server names"""
    return ["web-01", "web-02", "db-01"]

def get_server_info(server_name: str) -> Dict[str, str]:
    """Returns server information as a dictionary"""
    return {
        "name": server_name,
        "status": "running",
        "ip": "10.0.1.10"
    }

def check_health(server: str) -> bool:
    """Returns True if server is healthy"""
    # Health check logic
    return True

def get_system_stats() -> Tuple[float, float, float]:
    """Returns CPU, memory, and disk usage percentages"""
    return 45.2, 78.1, 62.8

def find_server(name: str) -> Optional[Dict[str, str]]:
    """Returns server info or None if not found"""
    servers = get_server_list()
    if name in servers:
        return get_server_info(name)
    return None

# Complex type hints
def process_deployment_result(result: Union[str, int, Dict[str, Any]]) -> str:
    """Process different types of deployment results"""
    if isinstance(result, str):
        return f"Message: {result}"
    elif isinstance(result, int):
        return f"Exit code: {result}"
    elif isinstance(result, dict):
        return f"Result: {result.get('status', 'unknown')}"
    return "Unknown result type"

def execute_with_callback(
    command: str, 
    callback: Callable[[str], None],
    timeout: Optional[int] = None
) -> subprocess.CompletedProcess:
    """Execute command with callback function"""
    result = subprocess.run(command.split(), capture_output=True, text=True, timeout=timeout)
    callback(result.stdout)
    return result

def batch_process_servers(
    servers: List[str],
    operation: Callable[[str], bool],
    max_concurrent: int = 5
) -> Dict[str, Union[bool, str]]:
    """Process multiple servers with operation function"""
    results = {}
    for server in servers:
        try:
            success = operation(server)
            results[server] = success
        except Exception as e:
            results[server] = str(e)
    return results

# Advanced function signatures with all parameter types
def deploy_application(
    app_name: str,                              # Required positional parameter
    version: str = "latest",                    # Optional parameter with default
    environment: str = "staging",               # Optional parameter with default
    *additional_args: str,                      # Variable positional arguments
    force: bool = False,                        # Keyword-only argument
    timeout: int = 300,                         # Keyword-only argument
    **deployment_options: Any                   # Variable keyword arguments
) -> Dict[str, Union[str, bool, int]]:
    """
    Deploy application with comprehensive parameter handling
    
    Args:
        app_name: Name of the application to deploy
        version: Version tag (default: "latest")
        environment: Target environment (default: "staging")
        *additional_args: Additional command line arguments
        force: Force deployment even if checks fail (keyword-only)
        timeout: Deployment timeout in seconds (keyword-only)
        **deployment_options: Additional deployment configuration
    
    Returns:
        Dictionary with deployment results
    """
    print(f"Deploying {app_name}:{version} to {environment}")
    print(f"Additional args: {additional_args}")
    print(f"Force: {force}, Timeout: {timeout}")
    print(f"Options: {deployment_options}")
    
    return {
        "app_name": app_name,
        "version": version,
        "environment": environment,
        "success": True,
        "deployment_time": 45
    }

# Function with complex nested types
def analyze_server_metrics(
    metrics_data: Dict[str, List[Tuple[datetime, float]]]
) -> Dict[str, Dict[str, Union[float, str]]]:
    """
    Analyze server metrics with complex nested types
    
    Args:
        metrics_data: Dictionary mapping metric names to lists of (timestamp, value) tuples
    
    Returns:
        Analysis results with statistics for each metric
    """
    analysis = {}
    for metric_name, data_points in metrics_data.items():
        values = [value for timestamp, value in data_points]
        analysis[metric_name] = {
            "avg": sum(values) / len(values),
            "max": max(values),
            "min": min(values),
            "count": len(values),
            "status": "healthy" if max(values) < 90 else "warning"
        }
    return analysis

# Generator function with type hints
def stream_log_lines(log_file: str) -> Iterator[str]:
    """Generator that yields log lines one by one"""
    with open(log_file, 'r') as f:
        for line in f:
            yield line.strip()

# Async function with type hints
async def async_health_check(url: str) -> Dict[str, Union[str, int, bool]]:
    """Async function to check service health"""
    import aiohttp
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as response:
            return {
                "url": url,
                "status_code": response.status,
                "healthy": response.status == 200,
                "response_time": 150  # Simulated
            }

# DevOps class with comprehensive type hints
class ServerManager:
    def __init__(self, servers: List[str], config: Optional[Dict[str, Any]] = None) -> None:
        self.servers = servers
        self.config = config or {}
        self._status_cache: Dict[str, str] = {}
    
    def restart_server(
        self, 
        server_name: str, 
        *, 
        force: bool = False,
        wait_time: int = 30
    ) -> bool:
        """Restart server with keyword-only parameters"""
        if server_name not in self.servers:
            return False
        
        print(f"Restarting {server_name} (force={force}, wait={wait_time}s)")
        # Restart logic here
        self._status_cache[server_name] = "restarting"
        return True
    
    def get_all_statuses(self) -> Dict[str, str]:
        """Get status of all managed servers"""
        return {server: "running" for server in self.servers}
    
    def bulk_operation(
        self,
        operation: Callable[[str], bool],
        servers: Optional[List[str]] = None
    ) -> Dict[str, bool]:
        """Apply operation to multiple servers"""
        target_servers = servers or self.servers
        return {server: operation(server) for server in target_servers}
    
    @classmethod
    def from_config_file(cls, config_path: str) -> 'ServerManager':
        """Create ServerManager from configuration file"""
        import json
        with open(config_path) as f:
            config = json.load(f)
        return cls(config['servers'], config.get('options'))

# Example usage with all parameter types
if __name__ == "__main__":
    # Using the comprehensive deploy function
    result = deploy_application(
        "web-app",                    # Required positional
        "v2.1.0",                     # Optional positional
        "production",                 # Optional positional
        "--enable-ssl",               # Variable positional args
        "--compress",
        force=True,                   # Keyword-only
        timeout=600,                  # Keyword-only
        replicas=3,                   # Variable keyword args
        health_check=True,
        rollback_on_failure=True
    )
    
    # Using callback function
    def log_output(output: str) -> None:
        print(f"Command output: {output}")
    
    cmd_result = execute_with_callback(
        "ls -la", 
        callback=log_output,
        timeout=10
    )
    
    # Using server manager
    manager = ServerManager(["web-01", "web-02"])
    manager.restart_server("web-01", force=True, wait_time=60)
```

**📖 What This Does**  
Type hints use `->` and `:` to specify what types of data functions expect and return. They help other developers understand your code and enable better IDE support with auto-completion and error detection.

**🔧 Configuration Notes**
- `parameter: type` specifies what type a parameter should be
- `-> type` specifies what type the function returns
- Common types: `str`, `int`, `bool`, `List[str]`, `Dict[str, int]`
- Optional with `Optional[str]` for values that might be `None`
- Import types from `typing` module for complex types
- Helps catch errors early and improves code documentation
- Not enforced at runtime, but very helpful for large DevOps projects

### *** ** - Unpacking Operators**
```python
# * unpacks lists/tuples
servers = ["web-01", "web-02", "db-01"]
print(*servers)  # Same as: print("web-01", "web-02", "db-01")

def restart_servers(*server_names):
    """Restart multiple servers"""
    for server in server_names:
        print(f"Restarting {server}")

restart_servers("web-01", "web-02", "db-01")
restart_servers(*servers)  # Unpacks the list

# ** unpacks dictionaries
database_config = {
    "host": "localhost",
    "port": 3306,
    "database": "production"
}

def connect_to_database(host, port, database, **kwargs):
    print(f"Connecting to {host}:{port}/{database}")
    for key, value in kwargs.items():
        print(f"  {key}: {value}")

connect_to_database(**database_config)

# DevOps examples
def deploy_application(app_name, **config):
    """Deploy with flexible configuration"""
    print(f"Deploying {app_name}")
    if "environment" in config:
        print(f"Environment: {config['environment']}")
    if "replicas" in config:
        print(f"Replicas: {config['replicas']}")

deployment_config = {
    "environment": "production",
    "replicas": 3,
    "health_check": True
}

deploy_application("web-app", **deployment_config)

# Merging dictionaries
default_config = {"debug": False, "port": 8080}
user_config = {"port": 3000, "ssl": True}
final_config = {**default_config, **user_config}  # Merge configs
```

**📖 What This Does**  
Unpacking operators extract items from collections and pass them as separate arguments to functions. `*` unpacks lists/tuples into individual items, while `**` unpacks dictionaries into keyword arguments.

**🔧 Configuration Notes**
- `*args` accepts any number of positional arguments as a tuple
- `**kwargs` accepts any number of keyword arguments as a dictionary
- `*list` unpacks a list when calling a function
- `**dict` unpacks a dictionary when calling a function
- `{**dict1, **dict2}` merges dictionaries (newer Python versions)
- Very useful for flexible function parameters in DevOps automation
- Common pattern for configuration management and dynamic function calls

---

## 🔧 **More Advanced Symbols and Use Cases**

### **; - Statement Separator (Rare)**
```python
# Multiple statements on one line (not recommended, but valid)
x = 5; y = 10; print(x + y)

# More common in one-liners or interactive Python
import os; import sys; print(os.getcwd())

# DevOps example (though separate lines are better)
status = "running"; port = 8080; print(f"Server {status} on port {port}")

# Better practice - separate lines:
status = "running"
port = 8080
print(f"Server {status} on port {port}")
```

### **# - Comments and Special Comments**
```python
# Regular comment
server_name = "web-01"  # Inline comment

# Special comments for tools
#!/usr/bin/env python3              # Shebang line
# -*- coding: utf-8 -*-             # Encoding declaration

# Type checking comments
# type: ignore                      # Tell mypy to ignore this line
variable = some_function()  # type: str  # Old-style type hint

# DevOps configuration comments
# TODO: Add error handling for deployment failures
# FIXME: This hardcoded URL should come from config
# NOTE: This assumes all servers are Linux-based
API_URL = "https://api.company.com"  # Production API endpoint

# Multi-line comments for documentation
"""
This is a multi-line comment (docstring) explaining
the deployment process:
1. Validate configuration
2. Build application
3. Deploy to staging
4. Run tests
5. Deploy to production
"""
```

### **_ - Underscore (Multiple Uses)**
```python
# Throwaway variable
for _ in range(5):
    print("Starting deployment...")

# Ignore values when unpacking
name, _, email = user_data.split(',')  # Ignore middle value
first, *_, last = servers  # Ignore everything except first and last

# Number formatting (Python 3.6+)
large_number = 1_000_000  # Same as 1000000
storage_bytes = 500_000_000_000  # 500 GB in bytes

# Private/protected attributes in classes
class Server:
    def __init__(self, name):
        self.name = name           # Public
        self._status = "running"   # Protected (convention)
        self.__secret_key = "123"  # Private (name mangling)

# Internationalization
from gettext import gettext as _
error_message = _("Deployment failed")  # Translatable string

# Previous result in Python REPL
# >>> 2 + 3
# 5
# >>> _ * 2  # Uses previous result (5)
# 10

# DevOps examples
for _ in range(max_retries):
    if deploy_success():
        break
    time.sleep(1)

# Ignore unneeded values
server_name, _, cpu_count, _ = server_info.split(',')
```

### **\ - Line Continuation**
```python
# Continue long lines
long_command = "docker run -d " \
               "--name web-server " \
               "--port 8080:80 " \
               "--env ENVIRONMENT=production " \
               "--volume /data:/app/data " \
               "nginx:latest"

# Function calls with many parameters
result = deploy_application(
    app_name="web-app",
    version="v2.1.0",
    environment="production",
    enable_ssl=True,
    health_check_timeout=30,
    rollback_on_failure=True,
    notification_channels=["slack", "email"]
)

# Long conditional statements
if (environment == "production" and 
    cpu_usage > 80 and 
    memory_usage > 75 and 
    error_rate < 0.01):
    trigger_auto_scaling()

# Dictionary spanning multiple lines (no \ needed inside brackets)
config = {
    "database": {
        "host": "db.company.com",
        "port": 5432,
        "name": "production_db"
    },
    "redis": {
        "host": "cache.company.com", 
        "port": 6379
    }
}
```

### **| & ^ ~ - Bitwise Operators (Less Common)**
```python
# Bitwise operators (rarely used in DevOps, but good to know)
permissions = 0o755  # File permissions in octal
read_perm = 0o400
write_perm = 0o200
exec_perm = 0o100

# Combine permissions
full_perm = read_perm | write_perm | exec_perm  # OR operation

# Check permissions
has_write = permissions & write_perm  # AND operation

# DevOps example: Feature flags using bitwise operations
FEATURE_SSL = 1      # 001
FEATURE_AUTH = 2     # 010  
FEATURE_CACHE = 4    # 100

# Enable multiple features
enabled_features = FEATURE_SSL | FEATURE_AUTH  # 011

# Check if feature is enabled
if enabled_features & FEATURE_SSL:
    setup_ssl_certificates()

# Set/pipe operations in modern Python (3.9+)
servers_set1 = {"web-01", "web-02", "db-01"}
servers_set2 = {"web-01", "api-01", "cache-01"}

# Union with |
all_servers = servers_set1 | servers_set2
# Intersection with &
shared_servers = servers_set1 & servers_set2
```

### **{} - Set Creation and Dictionary/Set Comprehensions**
```python
# Set creation (unordered, unique items)
unique_ports = {80, 443, 22, 3306, 5432}
empty_set = set()  # Note: {} creates empty dict, not set

# Set operations
web_ports = {80, 443, 8080}
db_ports = {3306, 5432, 27017}
all_ports = web_ports | db_ports  # Union
common_ports = web_ports & db_ports  # Intersection (empty in this case)

# Dictionary comprehension
servers = ["web-01", "web-02", "db-01"]
server_status = {server: "running" for server in servers}

# Set comprehension  
error_codes = [404, 500, 404, 200, 500, 404]
unique_errors = {code for code in error_codes if code >= 400}

# DevOps examples
# Get unique IP addresses from log file
log_entries = [
    "192.168.1.10 - GET /",
    "192.168.1.11 - POST /api", 
    "192.168.1.10 - GET /health"
]
unique_ips = {entry.split()[0] for entry in log_entries}

# Environment variable processing
import os
env_vars = {
    key: value for key, value in os.environ.items() 
    if key.startswith("APP_")
}

# Server configuration based on environment
environments = ["dev", "staging", "prod"]
server_configs = {
    env: {
        "replicas": 1 if env == "dev" else 3 if env == "staging" else 5,
        "memory": "512Mi" if env == "dev" else "1Gi" if env == "staging" else "2Gi"
    }
    for env in environments
}
```

### **[] - Advanced List Operations and Slicing**
```python
# Advanced slicing
servers = ["web-01", "web-02", "web-03", "api-01", "api-02", "db-01", "cache-01"]

# Basic slicing
web_servers = servers[0:3]        # First 3 servers
api_servers = servers[3:5]        # Servers 3-5 (not including 5)
last_two = servers[-2:]           # Last 2 servers
all_except_last = servers[:-1]    # All except last

# Step slicing
every_other = servers[::2]        # Every 2nd server: web-01, web-03, api-02, cache-01
reverse_order = servers[::-1]     # Reverse order

# DevOps examples
# Get production servers (assuming naming convention)
production_servers = [s for s in servers if "prod" in s]

# Batch processing servers
batch_size = 3
for i in range(0, len(servers), batch_size):
    batch = servers[i:i+batch_size]
    print(f"Processing batch: {batch}")
    # Process this batch of servers

# Rolling deployment (process in groups)
def rolling_deploy(servers, batch_size=2):
    for i in range(0, len(servers), batch_size):
        current_batch = servers[i:i+batch_size]
        print(f"Deploying to batch: {current_batch}")
        for server in current_batch:
            deploy_to_server(server)
        print("Waiting for health checks...")
        time.sleep(30)

# List operations
failed_servers = []
failed_servers.append("web-03")                    # Add single item
failed_servers.extend(["api-01", "db-01"])        # Add multiple items
failed_servers.insert(0, "critical-server")       # Insert at position
failed_servers.remove("api-01")                   # Remove by value
last_failed = failed_servers.pop()                # Remove and return last
failed_servers.clear()                            # Remove all items

# Advanced list methods
server_priorities = ["high", "medium", "low", "high", "medium"]
server_priorities.count("high")                   # Count occurrences: 2
server_priorities.index("low")                    # Find first index: 2
server_priorities.reverse()                       # Reverse in place
server_priorities.sort()                          # Sort in place

# List with conditional logic
healthy_servers = [
    server for server in servers 
    if check_health(server) and get_load(server) < 80
]
```

---

## 🎯 **Complete Python Symbol Quick Reference**

### **Assignment and Comparison**
| Symbol | Name | Usage | Example |
|--------|------|-------|---------|
| `=` | Assignment | Assign value to variable | `server = "web-01"` |
| `==` | Equality | Compare values | `if status == "running"` |
| `!=` | Not equal | Compare values | `if port != 80` |
| `+=` `-=` `*=` `/=` | Compound assignment | Modify and assign | `count += 1` |
| `<` `>` `<=` `>=` | Comparison | Compare values | `if cpu > 90` |

### **String and Text**
| Symbol | Name | Usage | Example |
|--------|------|-------|---------|
| `"` `'` | Quotes | Create strings | `name = "server"` |
| `f""` | F-string | Format strings | `f"Server: {name}"` |
| `"""` `'''` | Triple quotes | Multi-line strings | `"""Long text"""` |
| `\` | Escape | Escape special chars | `path = "C:\\Windows"` |
| `+` | Concatenation | Join strings | `full_name = first + last` |

### **Data Structures**
| Symbol | Name | Usage | Example |
|--------|------|-------|---------|
| `[]` | List/Index | Create lists, access items | `servers[0]` |
| `{}` | Dictionary/Set | Key-value pairs or sets | `{"host": "localhost"}` |
| `()` | Tuple/Call | Tuples, function calls | `coordinates = (x, y)` |
| `:` | Slice | Extract portions | `servers[1:3]` |

### **Logic and Control**
| Symbol | Name | Usage | Example |
|--------|------|-------|---------|
| `and` `or` `not` | Logic | Logical operations | `if a and b` |
| `is` `is not` | Identity | Object identity | `if x is None` |
| `in` `not in` | Membership | Check if item in collection | `if x in list` |
| `:` | Colon | Start code blocks | `if condition:` |

### **Math and Arithmetic**
| Symbol | Name | Usage | Example |
|--------|------|-------|---------|
| `+` `-` `*` `/` | Basic math | Arithmetic operations | `total = a + b` |
| `//` `%` `**` | Advanced math | Floor div, modulo, power | `x // 2`, `x % 3`, `x ** 2` |
| `|` `&` `^` `~` | Bitwise | Bit operations | `flags | new_flag` |

### **Functions and Objects**
| Symbol | Name | Usage | Example |
|--------|------|-------|---------|
| `.` | Dot | Access attributes/methods | `server.status()` |
| `->` `:` | Type hints | Specify types | `def func() -> str:` |
| `*` `**` | Unpacking | Unpack args/kwargs | `func(*args, **kwargs)` |
| `@` | Decorator | Modify functions | `@timer` |
| `lambda` | Anonymous | Small functions | `lambda x: x * 2` |

### **Special Characters**
| Symbol | Name | Usage | Example |
|--------|------|-------|---------|
| `#` | Comment | Add comments | `# This is a comment` |
| `_` | Underscore | Various uses | `for _ in range(5)` |
| `;` | Separator | Multiple statements | `x=1; y=2` (rare) |
| `\` | Continuation | Continue long lines | `long_var = "text" \` |
| `/` | Path | Unix path separator | `"/var/log/nginx"` |

---

📄 **File Path:** `/DevOps/01-Fundamentals_And_Environment_Setup/04-Python_For_DevOps/Python_Basics/02-Special_Characters_And_Symbols_In_Python.md`