# Python Fundamentals for DevOps

## 📖 What This File Covers
Essential Python programming concepts specifically tailored for DevOps workflows. Learn Python through the lens of automation, infrastructure management, and cloud operations rather than general programming.

## 🎯 Learning Objectives
- Master Python syntax for DevOps automation tasks
- Understand file system operations and process management
- Work with APIs and cloud services using Python
- Implement error handling for resilient automation
- Use Python for configuration management and deployment scripts

---

## 🐍 **Python Basics for DevOps Context**

### **Variables and Data Types in DevOps**
```python
# DevOps-specific variable examples
server_name = "web-server-01"
server_port = 8080
is_production = True
server_config = {
    "memory": "4GB",
    "cpu_cores": 2,
    "disk_space": "50GB"
}

# Environment-specific configurations
environments = ["dev", "staging", "prod"]
deployment_regions = ["us-east-1", "us-west-2", "eu-west-1"]

# Service endpoints and credentials
api_endpoints = {
    "monitoring": "https://monitoring.company.com/api",
    "deployment": "https://deploy.company.com/api",
    "logging": "https://logs.company.com/api"
}

print(f"Deploying {server_name} to {environments[0]} environment")
print(f"Server configuration: {server_config}")
```

### **Lists and Dictionaries for Infrastructure Data**
```python
# Managing server inventories
servers = [
    {"name": "web-01", "ip": "10.0.1.10", "role": "frontend"},
    {"name": "web-02", "ip": "10.0.1.11", "role": "frontend"},
    {"name": "db-01", "ip": "10.0.1.20", "role": "database"},
    {"name": "cache-01", "ip": "10.0.1.30", "role": "redis"}
]

# Filter servers by role
web_servers = [server for server in servers if server["role"] == "frontend"]
print(f"Web servers: {web_servers}")

# Configuration management
service_configs = {
    "nginx": {
        "port": 80,
        "workers": 4,
        "timeout": 30
    },
    "mysql": {
        "port": 3306,
        "max_connections": 100,
        "buffer_size": "256M"
    }
}

# Update configuration
service_configs["nginx"]["workers"] = 8
print(f"Updated Nginx config: {service_configs['nginx']}")
```

---

## 📁 **File System Operations for DevOps**

### **Reading Configuration Files**
```python
import json
import yaml
import os
from pathlib import Path

def load_deployment_config(environment):
    """Load environment-specific deployment configuration"""
    config_file = f"configs/{environment}.yml"
    
    try:
        with open(config_file, 'r') as file:
            config = yaml.safe_load(file)
        
        print(f"✅ Loaded configuration for {environment}")
        return config
        
    except FileNotFoundError:
        print(f"❌ Configuration file not found: {config_file}")
        return None
    except yaml.YAMLError as e:
        print(f"❌ Error parsing YAML: {e}")
        return None

# Load different environment configs
dev_config = load_deployment_config("dev")
prod_config = load_deployment_config("prod")
```

---

**💡 Next Steps**: Practice these Python fundamentals with real DevOps scenarios! 