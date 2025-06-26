# Shell Script Structure and Syntax: Mastering Bash Automation

## 📖 What This File Does
Learn the essential structure and syntax of Bash scripts - the foundation for all DevOps automation. Master variables, loops, conditions, and functions to create powerful automation tools that every DevOps engineer needs.

## 🎯 Learning Objectives
- Understand proper Bash script structure and organization
- Master variables, arrays, and parameter handling
- Implement control structures (if/else, loops, case statements)
- Create reusable functions and modular scripts
- Handle input/output and command substitution effectively
- Write production-ready scripts with proper error handling

## 📋 Prerequisites
- Completed "Understanding Scripts and Automation" module
- Comfortable with Linux command line operations
- Basic familiarity with text editors (nano, vim)
- Understanding of file permissions and execution

---

## 🏗️ Bash Script Structure

### **Essential Script Template**
```bash
#!/bin/bash
# Script: script-name.sh
# Description: Brief description of what this script does
# Author: Your Name
# Date: $(date)
# Version: 1.0

# Exit on any error, undefined variables, or pipe failures
set -euo pipefail

# Script configuration
readonly SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
readonly SCRIPT_NAME="$(basename "$0")"
readonly LOG_FILE="/var/log/${SCRIPT_NAME%.*}.log"

# Global variables
DEBUG=${DEBUG:-0}
VERBOSE=${VERBOSE:-0}

# Functions
usage() {
    cat << EOF
Usage: $SCRIPT_NAME [OPTIONS] [ARGUMENTS]

Description of what this script does.

OPTIONS:
    -h, --help      Show this help message
    -v, --verbose   Enable verbose output
    -d, --debug     Enable debug mode

EXAMPLES:
    $SCRIPT_NAME --verbose
    $SCRIPT_NAME --debug file.txt

EOF
}

log() {
    local level="$1"
    shift
    echo "[$(date +'%Y-%m-%d %H:%M:%S')] [$level] $*" | tee -a "$LOG_FILE"
}

error() {
    log "ERROR" "$*" >&2
}

info() {
    [[ $VERBOSE -eq 1 ]] && log "INFO" "$*"
}

debug() {
    [[ $DEBUG -eq 1 ]] && log "DEBUG" "$*"
}

# Main function
main() {
    log "INFO" "Script started: $SCRIPT_NAME"
    
    # Your main logic here
    
    log "INFO" "Script completed successfully"
}

# Parse command line arguments
while [[ $# -gt 0 ]]; do
    case $1 in
        -h|--help)
            usage
            exit 0
            ;;
        -v|--verbose)
            VERBOSE=1
            shift
            ;;
        -d|--debug)
            DEBUG=1
            shift
            ;;
        *)
            echo "Unknown option: $1"
            usage
            exit 1
            ;;
    esac
done

# Execute main function
main "$@"
```

---

## 📝 Variables and Data Types

### **Variable Declaration and Usage**
```bash
#!/bin/bash

# String variables
NAME="DevOps Engineer"
PROJECT_NAME="my-app"
ENVIRONMENT="production"

# Numeric variables
PORT=8080
TIMEOUT=30
MAX_RETRIES=3

# Boolean-like variables (0=true, 1=false in bash)
DEBUG_MODE=0
FORCE_UPDATE=1

# Read-only variables (constants)
readonly VERSION="1.2.3"
readonly CONFIG_FILE="/etc/myapp/config.yml"

# Environment variables with defaults
HOME_DIR=${HOME:-"/home/user"}
LOG_LEVEL=${LOG_LEVEL:-"INFO"}
DATABASE_URL=${DATABASE_URL:-"localhost:5432"}

# Using variables
echo "Hello, $NAME!"
echo "Project: ${PROJECT_NAME} v${VERSION}"
echo "Running on port: $PORT"
echo "Config file: $CONFIG_FILE"

# Variable expansion examples
echo "Uppercase name: ${NAME^^}"          # Convert to uppercase
echo "Lowercase name: ${NAME,,}"          # Convert to lowercase
echo "Name length: ${#NAME}"              # String length
echo "Default value: ${UNDEFINED_VAR:-'default'}"  # Use default if undefined
```

### **Arrays and Lists**
```bash
#!/bin/bash

# Indexed arrays
SERVERS=("web01" "web02" "web03")
PORTS=(8080 8081 8082)
ENVIRONMENTS=("dev" "staging" "production")

# Associative arrays (requires Bash 4+)
declare -A CONFIG
CONFIG["database_host"]="db.example.com"
CONFIG["database_port"]="5432"
CONFIG["redis_host"]="redis.example.com"
CONFIG["redis_port"]="6379"

# Working with arrays
echo "First server: ${SERVERS[0]}"
echo "All servers: ${SERVERS[@]}"
echo "Number of servers: ${#SERVERS[@]}"

# Iterating through arrays
echo "=== Server List ==="
for i in "${!SERVERS[@]}"; do
    echo "Server $((i+1)): ${SERVERS[i]} on port ${PORTS[i]}"
done

echo "=== Configuration ==="
for key in "${!CONFIG[@]}"; do
    echo "$key: ${CONFIG[$key]}"
done

# Adding to arrays
SERVERS+=("web04")
CONFIG["api_key"]="secret123"

# Array operations
ALL_PORTS="${PORTS[*]}"              # Join with space
COMMA_SEPARATED="${SERVERS[*]}"      # Join with first char of IFS
IFS=','
COMMA_SERVERS="${SERVERS[*]}"        # Join with comma
IFS=' '                              # Reset IFS
```

---

## 🔄 Control Structures

### **Conditional Statements**
```bash
#!/bin/bash

# Basic if-else
check_service_status() {
    local service_name="$1"
    
    if systemctl is-active --quiet "$service_name"; then
        echo "✅ $service_name is running"
        return 0
    else
        echo "❌ $service_name is not running"
        return 1
    fi
}

# Multiple conditions
check_system_health() {
    local cpu_usage=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d'%' -f1)
    local memory_usage=$(free | grep Mem | awk '{printf "%.0f", $3/$2 * 100.0}')
    local disk_usage=$(df / | awk 'NR==2{print $5}' | cut -d'%' -f1)
    
    if [[ $cpu_usage -gt 80 ]]; then
        echo "⚠️  High CPU usage: ${cpu_usage}%"
    elif [[ $memory_usage -gt 85 ]]; then
        echo "⚠️  High memory usage: ${memory_usage}%"
    elif [[ $disk_usage -gt 90 ]]; then
        echo "⚠️  High disk usage: ${disk_usage}%"
    else
        echo "✅ System health is good"
    fi
}

# File and directory tests
validate_environment() {
    local config_file="$1"
    local data_dir="$2"
    
    # File existence and permissions
    if [[ ! -f "$config_file" ]]; then
        echo "❌ Config file not found: $config_file"
        return 1
    elif [[ ! -r "$config_file" ]]; then
        echo "❌ Config file not readable: $config_file"
        return 1
    fi
    
    # Directory tests
    if [[ ! -d "$data_dir" ]]; then
        echo "📁 Creating data directory: $data_dir"
        mkdir -p "$data_dir"
    elif [[ ! -w "$data_dir" ]]; then
        echo "❌ Data directory not writable: $data_dir"
        return 1
    fi
    
    echo "✅ Environment validation passed"
    return 0
}

# String comparisons
validate_input() {
    local environment="$1"
    local action="$2"
    
    # String equality
    if [[ "$environment" == "production" ]]; then
        echo "🔒 Production environment detected"
        
        # Multiple string conditions
        if [[ "$action" == "deploy" || "$action" == "restart" ]]; then
            echo "⚠️  Dangerous action in production: $action"
            read -p "Are you sure? (yes/no): " confirmation
            [[ "$confirmation" == "yes" ]] || return 1
        fi
    fi
    
    # Pattern matching
    if [[ "$environment" =~ ^(dev|staging|prod)$ ]]; then
        echo "✅ Valid environment: $environment"
    else
        echo "❌ Invalid environment: $environment"
        return 1
    fi
}

# Case statements
handle_action() {
    local action="$1"
    
    case "$action" in
        start|run)
            echo "🚀 Starting application..."
            ;;
        stop|halt)
            echo "🛑 Stopping application..."
            ;;
        restart)
            echo "🔄 Restarting application..."
            ;;
        status|info)
            echo "📊 Checking application status..."
            ;;
        deploy)
            echo "🚢 Deploying application..."
            ;;
        backup)
            echo "💾 Creating backup..."
            ;;
        *)
            echo "❌ Unknown action: $action"
            echo "Valid actions: start, stop, restart, status, deploy, backup"
            return 1
            ;;
    esac
}
```

### **Loops and Iteration**
```bash
#!/bin/bash

# For loops with lists
deploy_to_servers() {
    local servers=("web01.example.com" "web02.example.com" "web03.example.com")
    local app_version="$1"
    
    echo "🚀 Deploying version $app_version to ${#servers[@]} servers..."
    
    for server in "${servers[@]}"; do
        echo "📡 Deploying to $server..."
        
        # Simulate deployment (replace with actual commands)
        if ssh "$server" "docker pull myapp:$app_version"; then
            echo "✅ $server: Pull successful"
        else
            echo "❌ $server: Pull failed"
            continue
        fi
        
        if ssh "$server" "docker-compose up -d"; then
            echo "✅ $server: Deployment successful"
        else
            echo "❌ $server: Deployment failed"
        fi
    done
}

# For loops with ranges
backup_logs() {
    local log_dir="/var/log/myapp"
    local backup_dir="/backup/logs"
    
    # Backup last 7 days of logs
    for day in {1..7}; do
        local date=$(date -d "$day days ago" +%Y-%m-%d)
        local log_file="$log_dir/app-$date.log"
        
        if [[ -f "$log_file" ]]; then
            echo "📦 Backing up log: $log_file"
            cp "$log_file" "$backup_dir/"
        else
            echo "⚠️  Log file not found: $log_file"
        fi
    done
}

# While loops with conditions
monitor_deployment() {
    local max_attempts=30
    local attempt=1
    local service_url="http://localhost:8080/health"
    
    echo "🔍 Monitoring deployment health..."
    
    while [[ $attempt -le $max_attempts ]]; do
        echo "Attempt $attempt/$max_attempts: Checking $service_url"
        
        if curl -s -f "$service_url" >/dev/null; then
            echo "✅ Service is healthy!"
            return 0
        else
            echo "⏳ Service not ready yet, waiting..."
            sleep 10
            ((attempt++))
        fi
    done
    
    echo "❌ Service failed to become healthy after $max_attempts attempts"
    return 1
}

# Until loops (opposite of while)
wait_for_file() {
    local file_path="$1"
    local timeout=300  # 5 minutes
    local elapsed=0
    
    echo "⏳ Waiting for file: $file_path"
    
    until [[ -f "$file_path" ]] || [[ $elapsed -ge $timeout ]]; do
        sleep 5
        ((elapsed += 5))
        echo "Waiting... (${elapsed}s/${timeout}s)"
    done
    
    if [[ -f "$file_path" ]]; then
        echo "✅ File found: $file_path"
        return 0
    else
        echo "❌ Timeout waiting for file: $file_path"
        return 1
    fi
}

# Reading files line by line
process_server_list() {
    local server_file="$1"
    local line_number=1
    
    while IFS= read -r server || [[ -n "$server" ]]; do
        # Skip empty lines and comments
        [[ -z "$server" || "$server" =~ ^[[:space:]]*# ]] && continue
        
        echo "Processing server $line_number: $server"
        
        # Extract hostname and port if specified
        if [[ "$server" =~ ^([^:]+):([0-9]+)$ ]]; then
            local hostname="${BASH_REMATCH[1]}"
            local port="${BASH_REMATCH[2]}"
            echo "  Hostname: $hostname, Port: $port"
        else
            echo "  Hostname: $server, Port: 22 (default)"
        fi
        
        ((line_number++))
    done < "$server_file"
}
```

---

## 🔧 Functions and Modularity

### **Function Definition and Usage**
```bash
#!/bin/bash

# Basic function definition
greet_user() {
    local username="$1"
    local role="${2:-'user'}"  # Default value
    
    echo "Hello, $username! You are logged in as: $role"
}

# Function with return values
check_port() {
    local host="$1"
    local port="$2"
    
    if nc -z "$host" "$port" 2>/dev/null; then
        return 0  # Success
    else
        return 1  # Failure
    fi
}

# Function with multiple return types
get_system_info() {
    local info_type="$1"
    
    case "$info_type" in
        "cpu")
            top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d'%' -f1
            ;;
        "memory")
            free | grep Mem | awk '{printf "%.1f", $3/$2 * 100.0}'
            ;;
        "disk")
            df / | awk 'NR==2{print $5}' | cut -d'%' -f1
            ;;
        "uptime")
            uptime -p
            ;;
        *)
            echo "Unknown info type: $info_type" >&2
            return 1
            ;;
    esac
}

# Complex function with error handling
deploy_application() {
    local app_name="$1"
    local version="$2"
    local environment="$3"
    
    # Validation
    [[ -z "$app_name" ]] && { echo "Error: App name required" >&2; return 1; }
    [[ -z "$version" ]] && { echo "Error: Version required" >&2; return 1; }
    [[ -z "$environment" ]] && { echo "Error: Environment required" >&2; return 1; }
    
    echo "🚀 Deploying $app_name:$version to $environment"
    
    # Pre-deployment checks
    if ! check_port "localhost" "8080"; then
        echo "⚠️  Port 8080 is not available"
        return 1
    fi
    
    # Backup current version
    if ! backup_current_version "$app_name" "$environment"; then
        echo "❌ Backup failed"
        return 1
    fi
    
    # Deploy new version
    if ! perform_deployment "$app_name" "$version" "$environment"; then
        echo "❌ Deployment failed, rolling back..."
        rollback_deployment "$app_name" "$environment"
        return 1
    fi
    
    # Verify deployment
    if ! verify_deployment "$app_name" "$environment"; then
        echo "❌ Deployment verification failed"
        return 1
    fi
    
    echo "✅ Deployment completed successfully"
    return 0
}

# Helper functions
backup_current_version() {
    local app_name="$1"
    local environment="$2"
    local backup_dir="/backup/$app_name/$environment"
    local timestamp=$(date +%Y%m%d-%H%M%S)
    
    echo "📦 Creating backup..."
    mkdir -p "$backup_dir"
    
    if [[ -d "/opt/$app_name" ]]; then
        tar -czf "$backup_dir/backup-$timestamp.tar.gz" -C "/opt" "$app_name"
        echo "✅ Backup created: $backup_dir/backup-$timestamp.tar.gz"
    else
        echo "⚠️  No existing installation to backup"
    fi
    
    return 0
}

perform_deployment() {
    local app_name="$1"
    local version="$2"
    local environment="$3"
    
    echo "🔄 Performing deployment..."
    
    # Download new version
    if ! wget -q "https://releases.example.com/$app_name-$version.tar.gz" -O "/tmp/$app_name-$version.tar.gz"; then
        echo "❌ Failed to download $app_name-$version.tar.gz"
        return 1
    fi
    
    # Extract and install
    if ! tar -xzf "/tmp/$app_name-$version.tar.gz" -C "/opt/"; then
        echo "❌ Failed to extract application"
        return 1
    fi
    
    # Update configuration
    if ! update_configuration "$app_name" "$environment"; then
        echo "❌ Failed to update configuration"
        return 1
    fi
    
    # Restart services
    if ! systemctl restart "$app_name"; then
        echo "❌ Failed to restart service"
        return 1
    fi
    
    return 0
}

verify_deployment() {
    local app_name="$1"
    local environment="$2"
    local health_url="http://localhost:8080/health"
    local max_attempts=10
    local attempt=1
    
    echo "🔍 Verifying deployment..."
    
    while [[ $attempt -le $max_attempts ]]; do
        if curl -s -f "$health_url" >/dev/null; then
            echo "✅ Health check passed"
            return 0
        fi
        
        echo "⏳ Attempt $attempt/$max_attempts failed, retrying..."
        sleep 10
        ((attempt++))
    done
    
    echo "❌ Health check failed after $max_attempts attempts"
    return 1
}
```

---

## 📥 Input/Output and Command Substitution

### **Command Substitution and Process Substitution**
```bash
#!/bin/bash

# Command substitution - old style
CURRENT_DATE=`date +%Y-%m-%d`
echo "Old style date: $CURRENT_DATE"

# Command substitution - modern style (preferred)
CURRENT_TIME=$(date +%H:%M:%S)
HOSTNAME=$(hostname)
KERNEL_VERSION=$(uname -r)

echo "Current time: $CURRENT_TIME"
echo "Hostname: $HOSTNAME"
echo "Kernel: $KERNEL_VERSION"

# Complex command substitution
CPU_COUNT=$(nproc)
MEMORY_GB=$(free -g | awk 'NR==2{print $2}')
DISK_USAGE=$(df -h / | awk 'NR==2{print $5}')

echo "System specs: ${CPU_COUNT} CPUs, ${MEMORY_GB}GB RAM, ${DISK_USAGE} disk usage"

# Command substitution with error handling
get_docker_status() {
    local docker_version
    local docker_status
    
    if docker_version=$(docker --version 2>/dev/null); then
        echo "Docker installed: $docker_version"
    else
        echo "Docker not installed or not accessible"
        return 1
    fi
    
    if docker_status=$(docker info --format '{{.ServerVersion}}' 2>/dev/null); then
        echo "Docker daemon running: $docker_status"
    else
        echo "Docker daemon not running"
        return 1
    fi
}

# Process substitution for comparing outputs
compare_configurations() {
    local config1="$1"
    local config2="$2"
    
    echo "Comparing configurations..."
    
    # Compare sorted configuration files
    if diff <(sort "$config1") <(sort "$config2") >/dev/null; then
        echo "✅ Configurations are identical"
    else
        echo "❌ Configurations differ:"
        diff <(sort "$config1") <(sort "$config2")
    fi
}

# Process substitution for reading command output
monitor_processes() {
    local threshold=80
    
    # Read process list and check CPU usage
    while IFS= read -r line; do
        local cpu_usage=$(echo "$line" | awk '{print $3}' | cut -d'%' -f1)
        local process_name=$(echo "$line" | awk '{print $11}')
        
        if [[ $cpu_usage -gt $threshold ]]; then
            echo "⚠️  High CPU process: $process_name ($cpu_usage%)"
        fi
    done < <(ps aux --sort=-%cpu | tail -n +2)
}
```

### **Advanced Input/Output Handling**
```bash
#!/bin/bash

# Reading user input with validation
get_user_confirmation() {
    local prompt="$1"
    local default="${2:-n}"
    local response
    
    while true; do
        read -p "$prompt [y/N]: " response
        response=${response:-$default}
        
        case "${response,,}" in  # Convert to lowercase
            y|yes)
                return 0
                ;;
            n|no)
                return 1
                ;;
            *)
                echo "Please answer yes or no."
                ;;
        esac
    done
}

# Reading passwords securely
get_database_credentials() {
    local username
    local password
    
    read -p "Database username: " username
    read -s -p "Database password: " password
    echo  # New line after hidden input
    
    # Validate credentials
    if [[ -z "$username" || -z "$password" ]]; then
        echo "❌ Username and password are required"
        return 1
    fi
    
    # Export for use by other functions
    export DB_USERNAME="$username"
    export DB_PASSWORD="$password"
    
    echo "✅ Credentials set"
}

# File input/output with error handling
process_configuration_file() {
    local config_file="$1"
    local output_file="$2"
    
    # Validate input file
    if [[ ! -f "$config_file" ]]; then
        echo "❌ Configuration file not found: $config_file" >&2
        return 1
    fi
    
    if [[ ! -r "$config_file" ]]; then
        echo "❌ Configuration file not readable: $config_file" >&2
        return 1
    fi
    
    # Process file line by line
    {
        echo "# Processed configuration - $(date)"
        echo "# Source: $config_file"
        echo ""
        
        while IFS= read -r line || [[ -n "$line" ]]; do
            # Skip comments and empty lines
            [[ "$line" =~ ^[[:space:]]*# ]] && continue
            [[ -z "${line// }" ]] && continue
            
            # Process configuration lines
            if [[ "$line" =~ ^([^=]+)=(.*)$ ]]; then
                local key="${BASH_REMATCH[1]// }"  # Remove spaces
                local value="${BASH_REMATCH[2]}"
                
                # Environment-specific processing
                case "$key" in
                    "DATABASE_URL")
                        echo "export $key=\"$value\""
                        ;;
                    "API_KEY")
                        echo "export $key=\"***REDACTED***\""
                        ;;
                    *)
                        echo "export $key=\"$value\""
                        ;;
                esac
            else
                echo "# Invalid line: $line"
            fi
        done < "$config_file"
    } > "$output_file"
    
    echo "✅ Configuration processed: $output_file"
}

# Logging with different levels
setup_logging() {
    local log_file="${1:-/var/log/script.log}"
    local log_level="${2:-INFO}"
    
    # Ensure log directory exists
    mkdir -p "$(dirname "$log_file")"
    
    # Export logging functions
    export LOG_FILE="$log_file"
    export LOG_LEVEL="$log_level"
}

log_message() {
    local level="$1"
    shift
    local message="$*"
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    
    # Check log level
    case "$LOG_LEVEL" in
        "DEBUG")
            ;;  # Log everything
        "INFO")
            [[ "$level" == "DEBUG" ]] && return
            ;;
        "WARN")
            [[ "$level" =~ ^(DEBUG|INFO)$ ]] && return
            ;;
        "ERROR")
            [[ "$level" =~ ^(DEBUG|INFO|WARN)$ ]] && return
            ;;
    esac
    
    # Write to log file and optionally to stdout
    echo "[$timestamp] [$level] $message" >> "$LOG_FILE"
    
    # Also output to stderr for ERROR and WARN
    if [[ "$level" =~ ^(ERROR|WARN)$ ]]; then
        echo "[$timestamp] [$level] $message" >&2
    fi
}

# Convenience logging functions
debug() { log_message "DEBUG" "$@"; }
info() { log_message "INFO" "$@"; }
warn() { log_message "WARN" "$@"; }
error() { log_message "ERROR" "$@"; }
```

---

## 📊 Progress Checkpoints

### **Script Structure Mastery** ✅
- [ ] Can create well-organized script templates
- [ ] Understands proper use of shebang and set options
- [ ] Can implement proper command-line argument parsing
- [ ] Uses consistent logging and error handling patterns

### **Variable Management** ✅
- [ ] Masters variable declaration and scoping
- [ ] Can work with arrays and associative arrays
- [ ] Understands variable expansion and substitution
- [ ] Uses environment variables and defaults effectively

### **Control Flow** ✅
- [ ] Implements complex conditional logic
- [ ] Uses appropriate loop constructs for different scenarios
- [ ] Masters case statements for menu-driven scripts
- [ ] Combines multiple control structures effectively

### **Function Development** ✅
- [ ] Creates reusable, modular functions
- [ ] Implements proper parameter handling and validation
- [ ] Uses return codes and error handling effectively
- [ ] Organizes code into logical, maintainable modules

### **Input/Output Mastery** ✅
- [ ] Handles user input with validation
- [ ] Processes files and command output effectively
- [ ] Implements comprehensive logging systems
- [ ] Uses command and process substitution appropriately

---

## ➡️ Next Steps

**You're ready for advanced Bash techniques when you can:**
- [ ] Create complex, multi-function scripts
- [ ] Handle errors gracefully with proper logging
- [ ] Process various input types and formats
- [ ] Organize code into reusable, maintainable modules
- [ ] Debug and optimize script performance

**Continue to:**
1. **03-Advanced_Bash_Techniques** - Error handling, debugging, and optimization
2. **04-Python_For_DevOps** - Advanced automation with Python
3. **05-DevOps_Script_Patterns** - Production-ready automation patterns

---

📄 **File Path:** `/DevOps/02-Linux_System_Administration/06-Shell_Scripting_Automation/02-Bash_Scripting_Fundamentals/01-Shell_Script_Structure_And_Syntax.md` 