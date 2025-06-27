# 🐧🐍 Complete Shell & Python Integration Reference for DevOps

## 📖 What This File Does
Comprehensive reference covering shell commands, Python integration, subprocess concepts, and DevOps automation patterns. Each section includes detailed explanations, examples, and practical applications.

## 🎯 Learning Objectives
- Master shell command execution from Python
- Understand subprocess parameters and implications
- Learn shell scripting integrated with Python logic
- Master DevOps automation patterns combining both approaches

---

## 🔧 **Shell Command Fundamentals**

### **Basic Shell Commands**
```bash
# File operations
ls -la                    # List files with details
cd /path/to/directory     # Change directory
pwd                       # Print working directory
mkdir -p /path/to/dir     # Create directories recursively
cp source destination     # Copy files
mv old new               # Move/rename files
rm -rf directory         # Remove files/directories
chmod +x script.sh       # Make executable

# System information
ps aux                   # Show processes
top                      # Monitor system resources
df -h                    # Disk usage
free -h                  # Memory usage
uptime                   # System uptime
whoami                   # Current user
```

**📖 What This Does**  
Shell commands provide direct system interaction for file management, process control, and system monitoring. They form the foundation of system administration and automation.

**🔧 DevOps Applications**
- File operations for deployment artifacts
- System monitoring and health checks
- Process management for services
- Permission management for security

**💡 Example Explanation**
```bash
# This command breakdown:
ls -la /var/log/
#  |  |     |
#  |  |     └── Path to list
#  |  └────── Show all files (including hidden)
#  └───────── Long format with details

# Returns: permissions, owner, size, date, filename
# -rw-r--r-- 1 root root 1234 Dec 1 10:30 app.log
```

### **Shell Command Execution from Python**
```python
import subprocess

# Basic command execution
result = subprocess.run("ls -la", shell=True)

# Capture output
result = subprocess.run("ls -la", shell=True, capture_output=True, text=True)
print(f"Output: {result.stdout}")
print(f"Errors: {result.stderr}")
print(f"Return code: {result.returncode}")

# Command without shell
result = subprocess.run(["ls", "-la"], capture_output=True, text=True)
```

**📖 What This Does**  
Python's subprocess module executes shell commands and captures their output, errors, and exit codes. This bridges Python logic with system operations.

**🔧 DevOps Applications**
```python
def system_health_check():
    """Comprehensive system health check using shell commands"""
    
    checks = [
        ("df -h", "Disk usage check"),
        ("free -h", "Memory usage check"),
        ("systemctl status nginx", "Nginx service status"),
        ("docker ps", "Running containers"),
        ("kubectl get pods", "Kubernetes pods status")
    ]
    
    results = {}
    
    for command, description in checks:
        print(f"🔍 {description}")
        
        try:
            result = subprocess.run(
                command, 
                shell=True, 
                capture_output=True, 
                text=True, 
                timeout=30
            )
            
            results[description] = {
                "command": command,
                "success": result.returncode == 0,
                "output": result.stdout,
                "error": result.stderr,
                "return_code": result.returncode
            }
            
            if result.returncode == 0:
                print(f"✅ {description} - OK")
            else:
                print(f"❌ {description} - FAILED")
                print(f"   Error: {result.stderr}")
                
        except subprocess.TimeoutExpired:
            print(f"⏰ {description} - TIMEOUT")
            results[description] = {"error": "timeout"}
        except Exception as e:
            print(f"💥 {description} - EXCEPTION: {e}")
            results[description] = {"error": str(e)}
    
    return results
```

---

## 🔄 **Subprocess Module Deep Dive**

### **subprocess.run() Parameters**
```python
import subprocess

# Complete parameter breakdown
result = subprocess.run(
    args="command with arguments",           # Command to run
    shell=True,                             # Use shell interpretation
    capture_output=True,                    # Capture stdout and stderr
    text=True,                             # Return strings not bytes
    input="data to send to stdin",         # Input to the process
    timeout=30,                            # Timeout in seconds
    check=True,                            # Raise exception on failure
    cwd="/path/to/working/directory",      # Working directory
    env={"VAR": "value"},                  # Environment variables
    encoding="utf-8"                       # Text encoding
)
```

**📖 What This Does**  
Each parameter controls how the subprocess behaves, what it captures, and how it handles input/output. Understanding these parameters is crucial for reliable automation.

**🔧 Parameter Breakdown**
- **`shell=True`**: Interprets command through shell (allows pipes, redirects)
- **`capture_output=True`**: Captures stdout and stderr for processing
- **`text=True`**: Returns strings instead of bytes
- **`check=True`**: Raises CalledProcessError if command fails
- **`timeout=30`**: Kills process after 30 seconds
- **`cwd="/path"`**: Sets working directory for command
- **`env={}`**: Sets environment variables

**💡 Example Explanation**
```python
def deploy_with_environment():
    """Deploy application with custom environment"""
    
    # Custom environment for deployment
    deploy_env = {
        "ENVIRONMENT": "production",
        "API_KEY": "secret-key-here",
        "DATABASE_URL": "postgresql://user:pass@host:5432/db",
        "PATH": "/usr/local/bin:/usr/bin:/bin"
    }
    
    # Deploy in specific directory with custom environment
    result = subprocess.run(
        "./deploy.sh",                    # Script to run
        shell=True,                       # Allow shell features
        capture_output=True,              # Capture all output
        text=True,                        # Get strings not bytes
        timeout=600,                      # 10 minute timeout
        check=False,                      # Don't raise exception on failure
        cwd="/opt/myapp",                # Run in app directory
        env=deploy_env                    # Use custom environment
    )
    
    # Process results
    if result.returncode == 0:
        print("🎉 Deployment successful!")
        print(f"Output: {result.stdout}")
    else:
        print(f"💥 Deployment failed (code: {result.returncode})")
        print(f"Error: {result.stderr}")
    
    return result.returncode == 0
```

### **subprocess.Popen() for Advanced Control**
```python
import subprocess
import threading

# Real-time output processing
process = subprocess.Popen(
    "long-running-command",
    shell=True,
    stdout=subprocess.PIPE,
    stderr=subprocess.PIPE,
    universal_newlines=True,
    bufsize=1                           # Line buffered
)

# Read output in real-time
for line in process.stdout:
    print(f"OUTPUT: {line.strip()}")

# Wait for completion
process.wait()
```

**📖 What This Does**  
Popen provides more control over process execution, allowing real-time output processing, input/output redirection, and process management.

**🔧 DevOps Applications**
```python
def monitor_deployment_logs():
    """Monitor deployment logs in real-time"""
    
    def read_output(pipe, prefix):
        """Read output from pipe and print with prefix"""
        for line in pipe:
            line = line.strip()
            if line:
                timestamp = datetime.now().strftime("%H:%M:%S")
                print(f"[{timestamp}] {prefix}: {line}")
    
    # Start deployment process
    process = subprocess.Popen(
        "kubectl logs -f deployment/myapp",
        shell=True,
        stdout=subprocess.PIPE,
        stderr=subprocess.PIPE,
        universal_newlines=True,
        bufsize=1
    )
    
    # Create threads to read stdout and stderr
    stdout_thread = threading.Thread(
        target=read_output, 
        args=(process.stdout, "LOG")
    )
    stderr_thread = threading.Thread(
        target=read_output, 
        args=(process.stderr, "ERR")
    )
    
    # Start threads
    stdout_thread.start()
    stderr_thread.start()
    
    try:
        # Wait for process to complete
        return_code = process.wait(timeout=300)  # 5 minute timeout
        
        # Wait for threads to finish
        stdout_thread.join(timeout=5)
        stderr_thread.join(timeout=5)
        
        return return_code == 0
        
    except subprocess.TimeoutExpired:
        print("⏰ Deployment monitoring timed out")
        process.terminate()
        return False
    except KeyboardInterrupt:
        print("⏹️  Monitoring interrupted by user")
        process.terminate()
        return False
```

---

## 🔀 **Shell vs Python Decision Matrix**

### **When to Use Shell Commands**
```bash
# Simple file operations
cp config.txt config.txt.backup
mkdir -p /var/log/myapp
chmod +x deploy.sh

# System service management
systemctl start nginx
systemctl enable docker
systemctl status postgresql

# Quick text processing
grep "ERROR" /var/log/app.log | tail -10
cat access.log | awk '{print $1}' | sort | uniq -c

# Environment setup
export PATH=$PATH:/opt/bin
source ~/.bashrc
```

**📖 What This Does**  
Shell commands excel at simple, direct system operations. They're concise for file management, service control, and basic text processing.

**🔧 Use Shell When:**
- Simple file operations (copy, move, delete)
- System service management
- Quick text processing with pipes
- Environment variable manipulation
- Existing shell scripts work well

### **When to Use Python**
```python
# Complex logic and error handling
def deploy_with_rollback():
    """Deploy with automatic rollback on failure"""
    
    # Save current state
    current_version = get_current_version()
    
    try:
        # Deploy new version
        deploy_new_version()
        
        # Run health checks
        if not health_check_passes():
            raise Exception("Health checks failed")
            
        # Update load balancer
        update_load_balancer()
        
    except Exception as e:
        print(f"Deployment failed: {e}")
        print("Rolling back...")
        rollback_to_version(current_version)
        return False
    
    return True

# Data processing and analysis
def analyze_server_logs():
    """Analyze server logs for patterns"""
    
    log_data = []
    
    # Process multiple log files
    for log_file in Path("/var/log").glob("*.log"):
        with open(log_file) as f:
            for line in f:
                if "ERROR" in line:
                    log_data.append(parse_log_line(line))
    
    # Analyze patterns
    error_counts = Counter(entry['error_type'] for entry in log_data)
    hourly_distribution = group_by_hour(log_data)
    
    # Generate report
    generate_error_report(error_counts, hourly_distribution)
```

**📖 What This Does**  
Python excels at complex logic, error handling, data processing, and integration with APIs and databases.

**🔧 Use Python When:**
- Complex conditional logic
- Error handling and recovery
- Data processing and analysis
- API interactions
- Cross-platform compatibility needed
- Object-oriented design required

### **Hybrid Approaches**
```python
def hybrid_deployment():
    """Combine Python logic with shell commands"""
    
    # Python logic for validation
    if not validate_deployment_config():
        raise ValueError("Invalid deployment configuration")
    
    # Shell commands for system operations
    commands = [
        "docker build -t myapp:latest .",
        "docker tag myapp:latest myapp:$(git rev-parse --short HEAD)",
        "kubectl apply -f k8s/",
        "kubectl rollout status deployment/myapp"
    ]
    
    for command in commands:
        print(f"🔄 Executing: {command}")
        
        result = subprocess.run(
            command,
            shell=True,
            capture_output=True,
            text=True
        )
        
        if result.returncode != 0:
            # Python error handling
            print(f"❌ Command failed: {result.stderr}")
            send_alert(f"Deployment failed at: {command}")
            return False
        
        print(f"✅ Success: {command}")
    
    # Python logic for post-deployment
    if not verify_deployment():
        print("⚠️  Deployment verification failed")
        return False
    
    update_monitoring_dashboard()
    send_success_notification()
    return True
```

---

## 🌐 **Environment Variables and Configuration**

### **Shell Environment Variables**
```bash
# Setting environment variables
export DATABASE_URL="postgresql://user:pass@host:5432/db"
export API_KEY="secret-key-here"
export ENVIRONMENT="production"

# Using environment variables
echo $DATABASE_URL
echo "Current environment: $ENVIRONMENT"

# Conditional logic based on environment
if [ "$ENVIRONMENT" = "production" ]; then
    echo "Running in production mode"
    systemctl start monitoring
else
    echo "Running in development mode"
fi
```

**📖 What This Does**  
Environment variables store configuration that can be accessed by scripts and applications. They provide a way to configure behavior without changing code.

**🔧 DevOps Applications**
```bash
#!/bin/bash
# deployment.sh - Environment-aware deployment script

# Check required environment variables
if [ -z "$ENVIRONMENT" ]; then
    echo "❌ ENVIRONMENT variable not set"
    exit 1
fi

if [ -z "$API_KEY" ]; then
    echo "❌ API_KEY variable not set"
    exit 1
fi

# Environment-specific configuration
case $ENVIRONMENT in
    "production")
        REPLICAS=5
        MEMORY_LIMIT="2Gi"
        CPU_LIMIT="1000m"
        ;;
    "staging")
        REPLICAS=2
        MEMORY_LIMIT="1Gi"
        CPU_LIMIT="500m"
        ;;
    "development")
        REPLICAS=1
        MEMORY_LIMIT="512Mi"
        CPU_LIMIT="250m"
        ;;
    *)
        echo "❌ Unknown environment: $ENVIRONMENT"
        exit 1
        ;;
esac

echo "🚀 Deploying to $ENVIRONMENT environment"
echo "   Replicas: $REPLICAS"
echo "   Memory: $MEMORY_LIMIT"
echo "   CPU: $CPU_LIMIT"

# Deploy with environment-specific settings
kubectl set env deployment/myapp \
    ENVIRONMENT=$ENVIRONMENT \
    API_KEY=$API_KEY

kubectl scale deployment/myapp --replicas=$REPLICAS

kubectl patch deployment myapp -p "{
    \"spec\": {
        \"template\": {
            \"spec\": {
                \"containers\": [{
                    \"name\": \"myapp\",
                    \"resources\": {
                        \"limits\": {
                            \"memory\": \"$MEMORY_LIMIT\",
                            \"cpu\": \"$CPU_LIMIT\"
                        }
                    }
                }]
            }
        }
    }
}"
```

### **Python Environment Variable Management**
```python
import os
from typing import Dict, Optional

def get_environment_config() -> Dict[str, str]:
    """Get environment configuration with validation"""
    
    required_vars = [
        "ENVIRONMENT",
        "DATABASE_URL", 
        "API_KEY"
    ]
    
    optional_vars = {
        "DEBUG": "false",
        "LOG_LEVEL": "INFO",
        "TIMEOUT": "30",
        "RETRY_COUNT": "3"
    }
    
    config = {}
    
    # Check required variables
    for var in required_vars:
        value = os.environ.get(var)
        if not value:
            raise ValueError(f"Required environment variable {var} is not set")
        config[var] = value
    
    # Set optional variables with defaults
    for var, default in optional_vars.items():
        config[var] = os.environ.get(var, default)
    
    return config

def validate_environment_config(config: Dict[str, str]) -> bool:
    """Validate environment configuration"""
    
    # Validate environment
    valid_environments = ["development", "staging", "production"]
    if config["ENVIRONMENT"] not in valid_environments:
        print(f"❌ Invalid environment: {config['ENVIRONMENT']}")
        return False
    
    # Validate database URL format
    if not config["DATABASE_URL"].startswith(("postgresql://", "mysql://")):
        print("❌ Invalid database URL format")
        return False
    
    # Validate numeric values
    try:
        timeout = int(config["TIMEOUT"])
        retry_count = int(config["RETRY_COUNT"])
        
        if timeout < 1 or timeout > 300:
            print("❌ Timeout must be between 1 and 300 seconds")
            return False
            
        if retry_count < 0 or retry_count > 10:
            print("❌ Retry count must be between 0 and 10")
            return False
            
    except ValueError:
        print("❌ Invalid numeric configuration values")
        return False
    
    return True

def setup_deployment_environment():
    """Setup deployment with environment configuration"""
    
    try:
        config = get_environment_config()
        
        if not validate_environment_config(config):
            return False
        
        print(f"🔧 Configuration loaded for {config['ENVIRONMENT']} environment")
        
        # Environment-specific setup
        if config["ENVIRONMENT"] == "production":
            setup_production_monitoring()
            enable_security_features()
        elif config["ENVIRONMENT"] == "staging":
            setup_staging_testing()
        else:  # development
            enable_debug_features()
        
        return True
        
    except ValueError as e:
        print(f"❌ Configuration error: {e}")
        return False
```

---

## 🔧 **Process Management and Monitoring**

### **Shell Process Management**
```bash
# Process information
ps aux                          # All processes
ps aux | grep nginx            # Find nginx processes
pgrep nginx                    # Get nginx process IDs
pkill nginx                    # Kill nginx processes

# Process monitoring
top                            # Interactive process monitor
htop                           # Enhanced process monitor
nohup command &                # Run command in background
disown                         # Detach from shell

# Service management
systemctl start service        # Start service
systemctl stop service         # Stop service
systemctl restart service      # Restart service
systemctl status service       # Check service status
systemctl enable service       # Enable at boot
```

**📖 What This Does**  
Process management commands control running programs, monitor system resources, and manage system services. Essential for maintaining system health and service availability.

**🔧 DevOps Applications**
```bash
#!/bin/bash
# service_monitor.sh - Monitor critical services

SERVICES=("nginx" "postgresql" "redis" "docker")
FAILED_SERVICES=()

echo "🔍 Checking critical services..."

for service in "${SERVICES[@]}"; do
    if systemctl is-active --quiet "$service"; then
        echo "✅ $service is running"
    else
        echo "❌ $service is not running"
        FAILED_SERVICES+=("$service")
        
        # Attempt to restart
        echo "🔄 Attempting to restart $service..."
        if systemctl restart "$service"; then
            echo "✅ $service restarted successfully"
        else
            echo "💥 Failed to restart $service"
        fi
    fi
done

# Report summary
if [ ${#FAILED_SERVICES[@]} -eq 0 ]; then
    echo "🎉 All services are healthy"
    exit 0
else
    echo "⚠️  ${#FAILED_SERVICES[@]} services had issues: ${FAILED_SERVICES[*]}"
    exit 1
fi
```

### **Python Process Management**
```python
import psutil
import time
from typing import List, Dict

def get_process_info(process_name: str) -> List[Dict]:
    """Get detailed information about processes by name"""
    
    processes = []
    
    for proc in psutil.process_iter(['pid', 'name', 'cpu_percent', 'memory_percent', 'status']):
        try:
            if process_name.lower() in proc.info['name'].lower():
                # Get additional details
                process_details = {
                    'pid': proc.info['pid'],
                    'name': proc.info['name'],
                    'status': proc.info['status'],
                    'cpu_percent': proc.info['cpu_percent'],
                    'memory_percent': proc.info['memory_percent'],
                    'memory_mb': proc.memory_info().rss / 1024 / 1024,
                    'create_time': proc.create_time(),
                    'cmdline': ' '.join(proc.cmdline())
                }
                processes.append(process_details)
                
        except (psutil.NoSuchProcess, psutil.AccessDenied, psutil.ZombieProcess):
            continue
    
    return processes

def monitor_process_health(process_name: str, cpu_threshold: float = 80.0, 
                          memory_threshold: float = 80.0) -> Dict:
    """Monitor process health and detect issues"""
    
    processes = get_process_info(process_name)
    
    if not processes:
        return {
            "status": "not_found",
            "message": f"No processes found matching '{process_name}'"
        }
    
    issues = []
    total_cpu = sum(p['cpu_percent'] for p in processes)
    total_memory = sum(p['memory_percent'] for p in processes)
    
    # Check thresholds
    if total_cpu > cpu_threshold:
        issues.append(f"High CPU usage: {total_cpu:.1f}%")
    
    if total_memory > memory_threshold:
        issues.append(f"High memory usage: {total_memory:.1f}%")
    
    # Check for zombie processes
    zombie_count = sum(1 for p in processes if p['status'] == 'zombie')
    if zombie_count > 0:
        issues.append(f"{zombie_count} zombie processes")
    
    return {
        "status": "healthy" if not issues else "warning",
        "process_count": len(processes),
        "total_cpu": total_cpu,
        "total_memory": total_memory,
        "issues": issues,
        "processes": processes
    }

def automated_process_management():
    """Automated process monitoring and management"""
    
    critical_processes = ["nginx", "postgresql", "redis"]
    
    for process_name in critical_processes:
        print(f"🔍 Monitoring {process_name}...")
        
        health = monitor_process_health(process_name)
        
        if health["status"] == "not_found":
            print(f"❌ {process_name} not running - attempting restart")
            
            # Attempt to restart using systemctl
            result = subprocess.run(
                f"systemctl restart {process_name}",
                shell=True,
                capture_output=True,
                text=True
            )
            
            if result.returncode == 0:
                print(f"✅ {process_name} restarted successfully")
            else:
                print(f"💥 Failed to restart {process_name}: {result.stderr}")
                send_alert(f"{process_name} service is down and restart failed")
        
        elif health["status"] == "warning":
            print(f"⚠️  {process_name} has issues:")
            for issue in health["issues"]:
                print(f"   - {issue}")
            
            # Log detailed process information
            for proc in health["processes"]:
                if proc['cpu_percent'] > 50 or proc['memory_percent'] > 50:
                    print(f"   High resource usage: PID {proc['pid']} - "
                          f"CPU: {proc['cpu_percent']:.1f}% "
                          f"Memory: {proc['memory_percent']:.1f}%")
        
        else:
            print(f"✅ {process_name} is healthy "
                  f"(CPU: {health['total_cpu']:.1f}%, "
                  f"Memory: {health['total_memory']:.1f}%)")

def send_alert(message: str):
    """Send alert to monitoring system"""
    # Implementation depends on your alerting system
    print(f"🚨 ALERT: {message}")
```

---

## 🎯 **Complete DevOps Automation Examples**

### **Comprehensive Deployment Script**
```python
#!/usr/bin/env python3
"""
Complete deployment automation combining shell commands and Python logic
"""

import subprocess
import json
import time
from pathlib import Path
from typing import Dict, List, Optional

class DeploymentManager:
    def __init__(self, config_file: str):
        self.config = self.load_config(config_file)
        self.deployment_log = []
    
    def load_config(self, config_file: str) -> Dict:
        """Load deployment configuration"""
        config_path = Path(config_file)
        if not config_path.exists():
            raise FileNotFoundError(f"Config file not found: {config_file}")
        
        with open(config_path) as f:
            return json.load(f)
    
    def log_step(self, step: str, status: str, details: str = ""):
        """Log deployment step"""
        timestamp = time.strftime("%Y-%m-%d %H:%M:%S")
        log_entry = {
            "timestamp": timestamp,
            "step": step,
            "status": status,
            "details": details
        }
        self.deployment_log.append(log_entry)
        
        status_icon = {"start": "🔄", "success": "✅", "error": "❌", "warning": "⚠️"}.get(status, "•")
        print(f"[{timestamp}] {status_icon} {step}")
        if details:
            print(f"    {details}")
    
    def run_command(self, command: str, description: str, critical: bool = True) -> bool:
        """Run shell command with logging"""
        self.log_step(description, "start")
        
        try:
            result = subprocess.run(
                command,
                shell=True,
                capture_output=True,
                text=True,
                timeout=300
            )
            
            if result.returncode == 0:
                self.log_step(description, "success", f"Command: {command}")
                return True
            else:
                self.log_step(description, "error", f"Error: {result.stderr}")
                if critical:
                    raise Exception(f"Critical command failed: {command}")
                return False
                
        except subprocess.TimeoutExpired:
            self.log_step(description, "error", "Command timed out")
            if critical:
                raise Exception(f"Command timed out: {command}")
            return False
    
    def pre_deployment_checks(self) -> bool:
        """Run pre-deployment validation"""
        self.log_step("Pre-deployment checks", "start")
        
        checks = [
            ("git status --porcelain", "Check for uncommitted changes", False),
            ("docker --version", "Verify Docker is available", True),
            ("kubectl cluster-info", "Verify Kubernetes connection", True),
            (f"docker pull {self.config['base_image']}", "Pull base image", True)
        ]
        
        for command, description, critical in checks:
            if not self.run_command(command, description, critical):
                if critical:
                    return False
        
        self.log_step("Pre-deployment checks", "success")
        return True
    
    def build_application(self) -> bool:
        """Build application artifacts"""
        app_name = self.config['app_name']
        version = self.config['version']
        
        build_commands = [
            (f"docker build -t {app_name}:latest .", "Build Docker image"),
            (f"docker tag {app_name}:latest {app_name}:{version}", "Tag with version"),
            (f"docker push {app_name}:{version}", "Push to registry")
        ]
        
        for command, description in build_commands:
            if not self.run_command(command, description):
                return False
        
        return True
    
    def deploy_to_kubernetes(self) -> bool:
        """Deploy to Kubernetes cluster"""
        app_name = self.config['app_name']
        namespace = self.config['namespace']
        version = self.config['version']
        
        # Update deployment image
        update_command = (
            f"kubectl set image deployment/{app_name} "
            f"{app_name}={app_name}:{version} -n {namespace}"
        )
        
        if not self.run_command(update_command, "Update deployment image"):
            return False
        
        # Wait for rollout
        rollout_command = f"kubectl rollout status deployment/{app_name} -n {namespace}"
        if not self.run_command(rollout_command, "Wait for rollout completion"):
            return False
        
        return True
    
    def run_health_checks(self) -> bool:
        """Run post-deployment health checks"""
        self.log_step("Health checks", "start")
        
        app_url = self.config.get('health_check_url')
        if not app_url:
            self.log_step("Health checks", "warning", "No health check URL configured")
            return True
        
        # Wait for application to be ready
        for attempt in range(10):
            try:
                import requests
                response = requests.get(app_url, timeout=10)
                
                if response.status_code == 200:
                    self.log_step("Health checks", "success", f"Application healthy at {app_url}")
                    return True
                else:
                    self.log_step("Health checks", "warning", 
                                f"Attempt {attempt + 1}: Status {response.status_code}")
                    
            except Exception as e:
                self.log_step("Health checks", "warning", 
                            f"Attempt {attempt + 1}: {str(e)}")
            
            if attempt < 9:
                time.sleep(30)
        
        self.log_step("Health checks", "error", "Application failed health checks")
        return False
    
    def rollback_deployment(self):
        """Rollback deployment on failure"""
        self.log_step("Rollback deployment", "start")
        
        app_name = self.config['app_name']
        namespace = self.config['namespace']
        
        rollback_command = f"kubectl rollout undo deployment/{app_name} -n {namespace}"
        self.run_command(rollback_command, "Execute rollback", critical=False)
    
    def deploy(self) -> bool:
        """Execute complete deployment"""
        try:
            self.log_step("Deployment started", "start", 
                         f"Deploying {self.config['app_name']} version {self.config['version']}")
            
            # Pre-deployment checks
            if not self.pre_deployment_checks():
                raise Exception("Pre-deployment checks failed")
            
            # Build application
            if not self.build_application():
                raise Exception("Application build failed")
            
            # Deploy to Kubernetes
            if not self.deploy_to_kubernetes():
                raise Exception("Kubernetes deployment failed")
            
            # Health checks
            if not self.run_health_checks():
                self.log_step("Deployment", "warning", "Health checks failed - considering rollback")
                if self.config.get('rollback_on_health_check_failure', True):
                    self.rollback_deployment()
                    raise Exception("Deployment rolled back due to health check failures")
            
            self.log_step("Deployment completed", "success", 
                         f"Successfully deployed {self.config['app_name']} version {self.config['version']}")
            return True
            
        except Exception as e:
            self.log_step("Deployment failed", "error", str(e))
            return False
        
        finally:
            self.save_deployment_log()
    
    def save_deployment_log(self):
        """Save deployment log to file"""
        log_file = f"deployment-{self.config['app_name']}-{int(time.time())}.json"
        with open(log_file, 'w') as f:
            json.dump(self.deployment_log, f, indent=2)
        print(f"📝 Deployment log saved to {log_file}")

# Usage
if __name__ == "__main__":
    import sys
    
    if len(sys.argv) != 2:
        print("Usage: python deploy.py <config.json>")
        sys.exit(1)
    
    try:
        deployer = DeploymentManager(sys.argv[1])
        success = deployer.deploy()
        sys.exit(0 if success else 1)
    except Exception as e:
        print(f"💥 Deployment failed: {e}")
        sys.exit(1)
```

---

## 🎯 **Best Practices Summary**

### **Shell Command Best Practices**
- Always quote variables: `"$VARIABLE"`
- Check exit codes: `if [ $? -eq 0 ]; then`
- Use `set -e` to exit on errors
- Validate inputs before using them
- Use absolute paths for critical scripts

### **Python Subprocess Best Practices**
- Use `shell=False` when possible for security
- Always set timeouts for long-running commands
- Capture and log both stdout and stderr
- Handle exceptions gracefully
- Use type hints for better code documentation

### **Integration Best Practices**
- Validate environment before execution
- Log all operations for debugging
- Implement proper error handling and rollback
- Use configuration files for flexibility
- Test scripts in non-production environments first

---

📄 **File Path:** `/DevOps/02-Linux_System_Administration/02-Linux_Basics_For_Beginners/01-Essential_Commands/03-Complete_Shell_Python_Integration_Reference.md` 