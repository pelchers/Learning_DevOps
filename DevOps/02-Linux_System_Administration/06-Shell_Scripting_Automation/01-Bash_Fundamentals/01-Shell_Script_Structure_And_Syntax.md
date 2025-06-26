# Shell Script Structure and Syntax

## 📖 What This File Does
Master the fundamental structure and syntax of Bash shell scripts - the essential automation language for DevOps engineers. You'll learn to write well-structured, maintainable scripts that form the backbone of DevOps automation workflows.

## 🎯 Learning Objectives
- Understand shell script structure and execution
- Master Bash syntax including variables, operators, and control structures
- Learn best practices for script organization and documentation
- Write robust scripts with proper error handling
- Build reusable functions and modular script components
- Create production-ready automation scripts for DevOps tasks

## 📋 Prerequisites
- Completed Linux Fundamentals module
- Comfortable with Linux command line operations
- Basic understanding of file permissions and execution
- Access to a Linux environment for practice

---

## 🏗️ Shell Script Fundamentals

### What is a Shell Script?
A shell script is a text file containing a sequence of commands that the shell can execute. It's the bridge between manual command-line operations and sophisticated automation systems.

```bash
# Simple example - save as hello.sh
#!/bin/bash
echo "Hello, DevOps World!"
echo "Today is $(date)"
echo "You are logged in as: $(whoami)"
```

### The Shebang Line
```bash
#!/bin/bash                    # Standard bash interpreter
#!/usr/bin/env bash           # Portable bash (finds bash in PATH)
#!/bin/sh                     # POSIX shell (more portable)
#!/usr/bin/env python3        # Python script
#!/usr/bin/env node           # Node.js script

# Why the shebang matters:
# 1. Tells system which interpreter to use
# 2. Allows script to be executed directly
# 3. Makes scripts portable across systems
```

### Making Scripts Executable
```bash
# Create a script file
cat > first-script.sh << 'EOF'
#!/bin/bash
echo "My first DevOps script!"
echo "Current directory: $(pwd)"
echo "Available disk space:"
df -h /
EOF

# Make it executable
chmod +x first-script.sh

# Execute the script
./first-script.sh

# Alternative execution methods
bash first-script.sh          # Execute with bash explicitly
sh first-script.sh            # Execute with sh
source first-script.sh        # Execute in current shell
. first-script.sh             # Same as source (dot notation)
```

---

## 📝 Variables and Data Types

### Variable Declaration and Usage
```bash
#!/bin/bash

# Variable assignment (no spaces around =)
name="DevOps Engineer"
age=25
is_admin=true
current_date=$(date)

# Using variables
echo "Name: $name"
echo "Age: $age"
echo "Admin status: $is_admin"
echo "Current date: $current_date"

# Alternative syntax with braces (recommended for clarity)
echo "Name: ${name}"
echo "Age: ${age}"
echo "Today is ${current_date}"

# Read-only variables
readonly PI=3.14159
readonly SCRIPT_NAME="deployment-script"

# Environment variables
export DATABASE_URL="postgresql://localhost:5432/mydb"
export LOG_LEVEL="INFO"
```

### Special Variables
```bash
#!/bin/bash

# Script name and arguments
echo "Script name: $0"
echo "First argument: $1"
echo "Second argument: $2"
echo "All arguments: $@"
echo "Number of arguments: $#"

# Process information
echo "Process ID: $$"
echo "Parent process ID: $PPID"
echo "Exit status of last command: $?"

# Current user and system info
echo "Current user: $USER"
echo "Home directory: $HOME"
echo "Current path: $PATH"
echo "Working directory: $PWD"

# Example usage:
# ./script.sh arg1 arg2 arg3
```

### Variable Scope and Local Variables
```bash
#!/bin/bash

# Global variables
global_var="I'm global"

function demo_scope() {
    # Local variables (only exist within function)
    local local_var="I'm local"
    local function_name="demo_scope"
    
    # Modify global variable
    global_var="Modified by function"
    
    echo "Inside function:"
    echo "  Local: $local_var"
    echo "  Global: $global_var"
}

echo "Before function call: $global_var"
demo_scope
echo "After function call: $global_var"
# echo "Local var: $local_var"  # This would cause an error
```

### Arrays
```bash
#!/bin/bash

# Indexed arrays
servers=("web1" "web2" "db1" "cache1")
ports=(80 443 3306 6379)

# Associative arrays (requires bash 4+)
declare -A server_info
server_info[web1]="192.168.1.10"
server_info[web2]="192.168.1.11"
server_info[db1]="192.168.1.20"

# Array operations
echo "All servers: ${servers[@]}"
echo "First server: ${servers[0]}"
echo "Number of servers: ${#servers[@]}"

# Loop through array
for server in "${servers[@]}"; do
    echo "Processing server: $server"
done

# Loop through associative array
for server in "${!server_info[@]}"; do
    echo "Server $server has IP: ${server_info[$server]}"
done

# Add elements to array
servers+=("monitoring1")
echo "Updated servers: ${servers[@]}"
```

---

## 🔧 String Operations

### String Manipulation
```bash
#!/bin/bash

# String variables
full_name="John DevOps Engineer"
email="john.doe@company.com"
server_name="web-server-prod-01"

# String length
echo "Name length: ${#full_name}"

# Substring extraction
echo "First name: ${full_name:0:4}"        # Characters 0-3
echo "Last name: ${full_name:5}"           # From character 5 to end
echo "Domain: ${email#*.}"                 # Remove shortest match from beginning
echo "Username: ${email%@*}"               # Remove shortest match from end

# String replacement
echo "Replace spaces: ${full_name// /-}"   # Replace all spaces with hyphens
echo "Replace first: ${full_name/ /-}"     # Replace first space with hyphen

# Case conversion (bash 4+)
echo "Uppercase: ${full_name^^}"
echo "Lowercase: ${full_name,,}"
echo "Title case: ${full_name^}"

# Pattern matching
if [[ $server_name == *"prod"* ]]; then
    echo "This is a production server"
fi

# String concatenation
prefix="backup-"
suffix="-$(date +%Y%m%d)"
backup_name="${prefix}${server_name}${suffix}"
echo "Backup name: $backup_name"
```

### String Validation and Testing
```bash
#!/bin/bash

# Test if string is empty
check_string() {
    local str="$1"
    
    if [[ -z "$str" ]]; then
        echo "String is empty"
    elif [[ -n "$str" ]]; then
        echo "String is not empty: '$str'"
    fi
}

# Test string patterns
validate_email() {
    local email="$1"
    
    if [[ $email =~ ^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$ ]]; then
        echo "Valid email: $email"
        return 0
    else
        echo "Invalid email: $email"
        return 1
    fi
}

# Test IP address format
validate_ip() {
    local ip="$1"
    
    if [[ $ip =~ ^[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}$ ]]; then
        echo "Valid IP format: $ip"
        return 0
    else
        echo "Invalid IP format: $ip"
        return 1
    fi
}

# Examples
check_string ""
check_string "DevOps"
validate_email "admin@company.com"
validate_email "invalid-email"
validate_ip "192.168.1.1"
validate_ip "999.999.999.999"
```

---

## 🔀 Control Structures

### Conditional Statements
```bash
#!/bin/bash

# Basic if statement
check_system_health() {
    local cpu_usage=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d'%' -f1)
    local memory_usage=$(free | grep Mem | awk '{printf "%.0f", $3/$2 * 100.0}')
    
    if [[ ${cpu_usage%.*} -gt 80 ]]; then
        echo "WARNING: High CPU usage: ${cpu_usage}%"
    elif [[ ${cpu_usage%.*} -gt 60 ]]; then
        echo "CAUTION: Moderate CPU usage: ${cpu_usage}%"
    else
        echo "OK: CPU usage is normal: ${cpu_usage}%"
    fi
    
    # Multiple conditions
    if [[ ${memory_usage} -gt 90 && ${cpu_usage%.*} -gt 70 ]]; then
        echo "CRITICAL: Both CPU and memory are high!"
        return 1
    fi
    
    return 0
}

# File and directory tests
check_file_system() {
    local file_path="$1"
    
    if [[ -f "$file_path" ]]; then
        echo "$file_path is a regular file"
    elif [[ -d "$file_path" ]]; then
        echo "$file_path is a directory"
    elif [[ -L "$file_path" ]]; then
        echo "$file_path is a symbolic link"
    elif [[ -e "$file_path" ]]; then
        echo "$file_path exists but is not a regular file or directory"
    else
        echo "$file_path does not exist"
    fi
    
    # Permission tests
    if [[ -r "$file_path" ]]; then
        echo "$file_path is readable"
    fi
    
    if [[ -w "$file_path" ]]; then
        echo "$file_path is writable"
    fi
    
    if [[ -x "$file_path" ]]; then
        echo "$file_path is executable"
    fi
}

# Numeric comparisons
compare_numbers() {
    local num1=$1
    local num2=$2
    
    if [[ $num1 -eq $num2 ]]; then
        echo "$num1 equals $num2"
    elif [[ $num1 -gt $num2 ]]; then
        echo "$num1 is greater than $num2"
    else
        echo "$num1 is less than $num2"
    fi
}
```

### Case Statements
```bash
#!/bin/bash

# Process command-line arguments
process_action() {
    local action="$1"
    
    case $action in
        start)
            echo "Starting services..."
            systemctl start nginx
            systemctl start mysql
            ;;
        stop)
            echo "Stopping services..."
            systemctl stop nginx
            systemctl stop mysql
            ;;
        restart)
            echo "Restarting services..."
            systemctl restart nginx
            systemctl restart mysql
            ;;
        status)
            echo "Checking service status..."
            systemctl status nginx
            systemctl status mysql
            ;;
        backup)
            echo "Creating backup..."
            backup_database
            backup_config_files
            ;;
        deploy)
            echo "Deploying application..."
            deploy_application "$2"
            ;;
        *)
            echo "Unknown action: $action"
            echo "Usage: $0 {start|stop|restart|status|backup|deploy}"
            exit 1
            ;;
    esac
}

# Environment-specific configuration
configure_environment() {
    local env="$1"
    
    case $env in
        development|dev)
            export DATABASE_URL="postgresql://localhost:5432/myapp_dev"
            export LOG_LEVEL="DEBUG"
            export CACHE_TTL=60
            ;;
        staging|stage)
            export DATABASE_URL="postgresql://staging-db:5432/myapp_staging"
            export LOG_LEVEL="INFO"
            export CACHE_TTL=300
            ;;
        production|prod)
            export DATABASE_URL="postgresql://prod-db:5432/myapp_prod"
            export LOG_LEVEL="WARN"
            export CACHE_TTL=3600
            ;;
        *)
            echo "Unknown environment: $env"
            echo "Supported environments: development, staging, production"
            exit 1
            ;;
    esac
    
    echo "Configured for $env environment"
}
```

### Loops
```bash
#!/bin/bash

# For loop with list
deploy_to_servers() {
    local servers=("web1.example.com" "web2.example.com" "web3.example.com")
    
    for server in "${servers[@]}"; do
        echo "Deploying to $server..."
        ssh "$server" "sudo systemctl restart nginx"
        
        if [[ $? -eq 0 ]]; then
            echo "✓ Successfully deployed to $server"
        else
            echo "✗ Failed to deploy to $server"
        fi
    done
}

# For loop with range
backup_daily_logs() {
    local log_dir="/var/log/myapp"
    local backup_dir="/backup/logs"
    
    # Backup logs for the last 7 days
    for day in {1..7}; do
        local date_str=$(date -d "$day days ago" +%Y-%m-%d)
        local log_file="$log_dir/app-$date_str.log"
        
        if [[ -f "$log_file" ]]; then
            echo "Backing up log for $date_str..."
            cp "$log_file" "$backup_dir/"
        else
            echo "No log file found for $date_str"
        fi
    done
}

# While loop for monitoring
monitor_service() {
    local service_name="$1"
    local max_attempts=10
    local attempt=1
    
    while [[ $attempt -le $max_attempts ]]; do
        echo "Attempt $attempt: Checking $service_name status..."
        
        if systemctl is-active --quiet "$service_name"; then
            echo "✓ $service_name is running"
            return 0
        else
            echo "✗ $service_name is not running"
            
            if [[ $attempt -eq $max_attempts ]]; then
                echo "Failed to start $service_name after $max_attempts attempts"
                return 1
            fi
            
            echo "Attempting to start $service_name..."
            systemctl start "$service_name"
            sleep 5
        fi
        
        ((attempt++))
    done
}

# Until loop (runs until condition is true)
wait_for_port() {
    local host="$1"
    local port="$2"
    local timeout=30
    local elapsed=0
    
    echo "Waiting for $host:$port to be available..."
    
    until nc -z "$host" "$port" 2>/dev/null; do
        if [[ $elapsed -ge $timeout ]]; then
            echo "Timeout: $host:$port not available after $timeout seconds"
            return 1
        fi
        
        echo "Waiting... ($elapsed/$timeout seconds)"
        sleep 2
        ((elapsed += 2))
    done
    
    echo "✓ $host:$port is now available"
    return 0
}
```

---

## 🔧 Functions

### Function Definition and Usage
```bash
#!/bin/bash

# Basic function definition
log_message() {
    local level="$1"
    local message="$2"
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    
    echo "[$timestamp] [$level] $message"
}

# Function with return value
check_disk_space() {
    local path="$1"
    local threshold="${2:-80}"  # Default threshold is 80%
    
    local usage=$(df "$path" | awk 'NR==2 {print $5}' | sed 's/%//')
    
    if [[ $usage -gt $threshold ]]; then
        log_message "WARNING" "Disk usage on $path is ${usage}% (threshold: ${threshold}%)"
        return 1
    else
        log_message "INFO" "Disk usage on $path is ${usage}% (OK)"
        return 0
    fi
}

# Function with multiple return values (using global variables)
get_system_info() {
    HOSTNAME=$(hostname)
    UPTIME=$(uptime -p)
    LOAD_AVERAGE=$(uptime | awk -F'load average:' '{print $2}')
    MEMORY_USAGE=$(free -m | awk 'NR==2{printf "%.2f%%", $3*100/$2}')
    DISK_USAGE=$(df -h / | awk 'NR==2{print $5}')
}

# Function with error handling
backup_database() {
    local db_name="$1"
    local backup_dir="$2"
    local backup_file="$backup_dir/${db_name}_$(date +%Y%m%d_%H%M%S).sql"
    
    # Validate inputs
    if [[ -z "$db_name" || -z "$backup_dir" ]]; then
        log_message "ERROR" "Database name and backup directory are required"
        return 1
    fi
    
    # Create backup directory if it doesn't exist
    if [[ ! -d "$backup_dir" ]]; then
        mkdir -p "$backup_dir" || {
            log_message "ERROR" "Failed to create backup directory: $backup_dir"
            return 1
        }
    fi
    
    # Perform backup
    log_message "INFO" "Starting backup of database: $db_name"
    
    if mysqldump "$db_name" > "$backup_file"; then
        log_message "INFO" "Backup completed successfully: $backup_file"
        
        # Compress backup
        if gzip "$backup_file"; then
            log_message "INFO" "Backup compressed: ${backup_file}.gz"
        fi
        
        return 0
    else
        log_message "ERROR" "Database backup failed"
        return 1
    fi
}

# Usage examples
log_message "INFO" "Script started"
check_disk_space "/" 85
get_system_info
echo "System: $HOSTNAME, Uptime: $UPTIME"
backup_database "myapp_prod" "/backup/databases"
```

### Advanced Function Techniques
```bash
#!/bin/bash

# Function with variable arguments
deploy_to_multiple_servers() {
    local app_version="$1"
    shift  # Remove first argument, rest are server names
    
    if [[ $# -eq 0 ]]; then
        echo "No servers specified"
        return 1
    fi
    
    echo "Deploying version $app_version to $# servers..."
    
    for server in "$@"; do
        echo "Deploying to $server..."
        ssh "$server" "docker pull myapp:$app_version && docker restart myapp"
    done
}

# Function that modifies arrays
add_server_to_pool() {
    local -n pool_ref=$1  # Name reference (bash 4.3+)
    local server="$2"
    
    pool_ref+=("$server")
    echo "Added $server to pool. Total servers: ${#pool_ref[@]}"
}

# Recursive function
factorial() {
    local n=$1
    
    if [[ $n -le 1 ]]; then
        echo 1
    else
        local prev=$(factorial $((n - 1)))
        echo $((n * prev))
    fi
}

# Function with cleanup trap
deploy_with_cleanup() {
    local temp_dir=$(mktemp -d)
    
    # Setup cleanup trap
    trap "rm -rf $temp_dir; echo 'Cleanup completed'" EXIT
    
    echo "Using temporary directory: $temp_dir"
    
    # Simulate deployment work
    cd "$temp_dir"
    echo "Downloading application..."
    sleep 2
    
    echo "Extracting files..."
    sleep 1
    
    echo "Deployment completed"
    # Cleanup happens automatically via trap
}

# Usage examples
deploy_to_multiple_servers "v1.2.3" "web1" "web2" "web3"

server_pool=("web1" "web2")
add_server_to_pool server_pool "web3"
echo "Current pool: ${server_pool[@]}"

result=$(factorial 5)
echo "5! = $result"

deploy_with_cleanup
```

---

## 🎯 Practical Exercises

### Exercise 1: System Information Script
```bash
#!/bin/bash
# Task: Create a comprehensive system information gathering script

# Create this script and test it
cat > system_info.sh << 'EOF'
#!/bin/bash

# System Information Gathering Script
# Author: DevOps Student
# Date: $(date)

set -euo pipefail

# Color codes for output
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
BLUE='\033[0;34m'
NC='\033[0m' # No Color

# Function to print colored output
print_section() {
    echo -e "\n${BLUE}=== $1 ===${NC}"
}

print_info() {
    echo -e "${GREEN}$1${NC}"
}

print_warning() {
    echo -e "${YELLOW}$1${NC}"
}

print_error() {
    echo -e "${RED}$1${NC}"
}

# Main information gathering
main() {
    print_section "SYSTEM INFORMATION"
    print_info "Hostname: $(hostname)"
    print_info "Operating System: $(cat /etc/os-release | grep PRETTY_NAME | cut -d'"' -f2)"
    print_info "Kernel Version: $(uname -r)"
    print_info "Architecture: $(uname -m)"
    print_info "Uptime: $(uptime -p)"
    
    print_section "CPU INFORMATION"
    print_info "CPU Model: $(grep 'model name' /proc/cpuinfo | head -1 | cut -d':' -f2 | xargs)"
    print_info "CPU Cores: $(nproc)"
    print_info "Load Average: $(uptime | awk -F'load average:' '{print $2}')"
    
    print_section "MEMORY INFORMATION"
    print_info "Total Memory: $(free -h | awk 'NR==2{print $2}')"
    print_info "Used Memory: $(free -h | awk 'NR==2{print $3}')"
    print_info "Free Memory: $(free -h | awk 'NR==2{print $4}')"
    print_info "Memory Usage: $(free | awk 'NR==2{printf "%.2f%%", $3*100/$2}')"
    
    print_section "DISK INFORMATION"
    df -h | grep -E '^/dev/' | while read line; do
        usage=$(echo $line | awk '{print $5}' | sed 's/%//')
        if [[ $usage -gt 80 ]]; then
            print_warning "$line"
        else
            print_info "$line"
        fi
    done
    
    print_section "NETWORK INFORMATION"
    print_info "Network Interfaces:"
    ip addr show | grep -E '^[0-9]+:' | awk '{print $2}' | sed 's/://' | while read iface; do
        ip=$(ip addr show $iface | grep 'inet ' | awk '{print $2}' | cut -d'/' -f1)
        if [[ -n "$ip" ]]; then
            print_info "  $iface: $ip"
        fi
    done
    
    print_section "RUNNING SERVICES"
    print_info "Active systemd services:"
    systemctl list-units --type=service --state=active | head -10
}

# Execute main function
main
EOF

chmod +x system_info.sh
./system_info.sh
```

### Exercise 2: Log Analysis Script
```bash
#!/bin/bash
# Task: Create a log analysis script

cat > log_analyzer.sh << 'EOF'
#!/bin/bash

# Log Analysis Script
set -euo pipefail

analyze_log() {
    local log_file="$1"
    local output_file="${2:-analysis_$(date +%Y%m%d_%H%M%S).txt}"
    
    if [[ ! -f "$log_file" ]]; then
        echo "Error: Log file $log_file not found"
        return 1
    fi
    
    echo "Analyzing log file: $log_file"
    echo "Output will be saved to: $output_file"
    
    {
        echo "LOG ANALYSIS REPORT"
        echo "==================="
        echo "File: $log_file"
        echo "Generated: $(date)"
        echo "Total lines: $(wc -l < "$log_file")"
        echo ""
        
        echo "TOP 10 ERROR MESSAGES:"
        grep -i error "$log_file" | sort | uniq -c | sort -nr | head -10
        echo ""
        
        echo "TOP 10 IP ADDRESSES:"
        grep -oE '\b([0-9]{1,3}\.){3}[0-9]{1,3}\b' "$log_file" | sort | uniq -c | sort -nr | head -10
        echo ""
        
        echo "HOURLY ACTIVITY:"
        awk '{print $4}' "$log_file" | cut -d: -f2 | sort | uniq -c
        echo ""
        
        echo "STATUS CODE DISTRIBUTION:"
        awk '{print $9}' "$log_file" | sort | uniq -c | sort -nr
        
    } > "$output_file"
    
    echo "Analysis complete. Results saved to $output_file"
}

# Usage
if [[ $# -eq 0 ]]; then
    echo "Usage: $0 <log_file> [output_file]"
    exit 1
fi

analyze_log "$1" "$2"
EOF

chmod +x log_analyzer.sh
# Test with: ./log_analyzer.sh /var/log/nginx/access.log
```

### Exercise 3: Deployment Script
```bash
#!/bin/bash
# Task: Create a deployment automation script

cat > deploy_app.sh << 'EOF'
#!/bin/bash

# Application Deployment Script
set -euo pipefail

# Configuration
APP_NAME="myapp"
DEPLOY_USER="deploy"
BACKUP_DIR="/backup/deployments"
LOG_FILE="/var/log/deployment.log"

# Logging function
log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $*" | tee -a "$LOG_FILE"
}

# Error handling
error_exit() {
    log "ERROR: $1"
    exit 1
}

# Rollback function
rollback() {
    local backup_file="$1"
    
    log "Starting rollback process..."
    
    if [[ -f "$backup_file" ]]; then
        log "Restoring from backup: $backup_file"
        tar -xzf "$backup_file" -C /opt/
        systemctl restart "$APP_NAME"
        log "Rollback completed successfully"
    else
        error_exit "Backup file not found: $backup_file"
    fi
}

# Main deployment function
deploy() {
    local version="$1"
    local environment="$2"
    
    log "Starting deployment of $APP_NAME version $version to $environment"
    
    # Pre-deployment checks
    log "Running pre-deployment checks..."
    
    # Check if user exists
    if ! id "$DEPLOY_USER" &>/dev/null; then
        error_exit "Deploy user $DEPLOY_USER does not exist"
    fi
    
    # Check disk space
    local available_space=$(df /opt | awk 'NR==2 {print $4}')
    if [[ $available_space -lt 1000000 ]]; then  # Less than 1GB
        error_exit "Insufficient disk space for deployment"
    fi
    
    # Create backup
    local backup_file="$BACKUP_DIR/${APP_NAME}_backup_$(date +%Y%m%d_%H%M%S).tar.gz"
    log "Creating backup: $backup_file"
    
    mkdir -p "$BACKUP_DIR"
    tar -czf "$backup_file" -C /opt "$APP_NAME" || error_exit "Backup creation failed"
    
    # Download new version
    log "Downloading version $version..."
    cd /tmp
    wget "https://releases.company.com/$APP_NAME/$version.tar.gz" || error_exit "Download failed"
    
    # Stop application
    log "Stopping application..."
    systemctl stop "$APP_NAME" || error_exit "Failed to stop application"
    
    # Deploy new version
    log "Deploying new version..."
    tar -xzf "/tmp/$version.tar.gz" -C /opt/ || {
        log "Deployment failed, starting rollback..."
        rollback "$backup_file"
        error_exit "Deployment failed and rollback completed"
    }
    
    # Update permissions
    chown -R "$DEPLOY_USER:$DEPLOY_USER" "/opt/$APP_NAME"
    
    # Start application
    log "Starting application..."
    systemctl start "$APP_NAME" || {
        log "Failed to start application, starting rollback..."
        rollback "$backup_file"
        error_exit "Application start failed and rollback completed"
    }
    
    # Health check
    log "Performing health check..."
    sleep 10
    
    if curl -f http://localhost:8080/health &>/dev/null; then
        log "Health check passed"
        log "Deployment of $APP_NAME version $version completed successfully"
    else
        log "Health check failed, starting rollback..."
        rollback "$backup_file"
        error_exit "Health check failed and rollback completed"
    fi
    
    # Cleanup
    rm -f "/tmp/$version.tar.gz"
    
    # Keep only last 5 backups
    ls -t "$BACKUP_DIR"/${APP_NAME}_backup_*.tar.gz | tail -n +6 | xargs -r rm
    
    log "Deployment process completed successfully"
}

# Main script logic
main() {
    if [[ $# -lt 2 ]]; then
        echo "Usage: $0 <version> <environment>"
        echo "Example: $0 v1.2.3 production"
        exit 1
    fi
    
    local version="$1"
    local environment="$2"
    
    # Validate environment
    case $environment in
        development|staging|production)
            deploy "$version" "$environment"
            ;;
        *)
            error_exit "Invalid environment: $environment. Use: development, staging, or production"
            ;;
    esac
}

# Execute main function
main "$@"
EOF

chmod +x deploy_app.sh
# Test with: ./deploy_app.sh v1.0.0 development
```

---

## 📊 Progress Checkpoint

Test your shell scripting knowledge:

```bash
# 1. Can you create a script that accepts command-line arguments?
# 2. Can you implement proper error handling with exit codes?
# 3. Can you write functions with local variables?
# 4. Can you use arrays and loops effectively?
# 5. Can you implement string manipulation and validation?

# Create a simple test script:
cat > test_knowledge.sh << 'EOF'
#!/bin/bash
set -euo pipefail

# Test 1: Command-line arguments
if [[ $# -eq 0 ]]; then
    echo "Usage: $0 <name> [age]"
    exit 1
fi

name="$1"
age="${2:-unknown}"

# Test 2: String manipulation
echo "Hello, ${name^}!"  # Capitalize first letter
echo "Age: $age"

# Test 3: Function with error handling
validate_age() {
    local age="$1"
    
    if [[ "$age" =~ ^[0-9]+$ ]] && [[ $age -gt 0 ]] && [[ $age -lt 150 ]]; then
        return 0
    else
        return 1
    fi
}

# Test 4: Conditional logic
if [[ "$age" != "unknown" ]]; then
    if validate_age "$age"; then
        echo "Valid age provided"
    else
        echo "Invalid age: $age"
        exit 1
    fi
fi

echo "Script completed successfully"
EOF

chmod +x test_knowledge.sh
./test_knowledge.sh "John" 25
```

If you can understand and modify this script confidently, you're ready for the next module!

---

## ➡️ Next Steps

You're now ready for:
1. **02-DevOps_Script_Patterns** - Error handling and logging
2. **03-Automation_Scripts** - Real-world automation examples
3. **04-CI_CD_Integration** - Pipeline scripting

---

📄 **File Path:** `/DevOps/02-Linux_System_Administration/04-Shell_Scripting_Automation/01-Bash_Fundamentals/01-Shell_Script_Structure_And_Syntax.md` 