# Error Handling and Logging in DevOps Scripts

## 📖 What This File Does
Master robust error handling and logging patterns essential for production DevOps scripts. Learn to write scripts that fail gracefully, provide meaningful error messages, and maintain comprehensive logs for troubleshooting and auditing.

## 🎯 Learning Objectives
- Implement comprehensive error handling in shell scripts
- Create structured logging systems for DevOps automation
- Build fault-tolerant scripts that handle edge cases
- Design scripts with proper exit codes and error reporting
- Implement retry mechanisms and circuit breakers
- Create monitoring-friendly logging for production systems

## 📋 Prerequisites
- Completed Bash Fundamentals module
- Understanding of shell script structure and syntax
- Basic knowledge of Linux system administration
- Familiarity with DevOps automation concepts

---

## 🚨 Error Handling Fundamentals

### Script Safety with `set` Options
```bash
#!/bin/bash

# Essential safety options for production scripts
set -euo pipefail

# Explanation:
# -e: Exit immediately if any command fails
# -u: Exit if trying to use undefined variable
# -o pipefail: Exit if any command in a pipeline fails

# Example without safety options (dangerous):
#!/bin/bash
# rm -rf $IMPORTANT_DIR  # If IMPORTANT_DIR is undefined, this becomes rm -rf /

# Example with safety options (safe):
#!/bin/bash
set -euo pipefail
# rm -rf $IMPORTANT_DIR  # Script exits with error if IMPORTANT_DIR undefined

# Conditional safety options
if [[ "${DEBUG:-false}" == "true" ]]; then
    set -x  # Enable debug mode (print commands before execution)
fi

# Disable safety temporarily when needed
set +e
command_that_might_fail
exit_code=$?
set -e

if [[ $exit_code -ne 0 ]]; then
    echo "Command failed with exit code: $exit_code"
    # Handle the failure appropriately
fi
```

### Exit Codes and Error Reporting
```bash
#!/bin/bash
set -euo pipefail

# Standard exit codes
readonly EXIT_SUCCESS=0
readonly EXIT_FAILURE=1
readonly EXIT_INVALID_ARGS=2
readonly EXIT_CONFIG_ERROR=3
readonly EXIT_NETWORK_ERROR=4
readonly EXIT_PERMISSION_ERROR=5

# Function to handle errors with context
error_exit() {
    local error_message="$1"
    local exit_code="${2:-$EXIT_FAILURE}"
    local line_number="${3:-${LINENO}}"
    
    echo "ERROR: $error_message (Line: $line_number)" >&2
    echo "Script: $0" >&2
    echo "Function: ${FUNCNAME[1]:-main}" >&2
    echo "Exit code: $exit_code" >&2
    
    # Optional: Send error to monitoring system
    send_error_to_monitoring "$error_message" "$exit_code"
    
    exit "$exit_code"
}

# Trap errors and provide context
trap 'error_exit "Unexpected error occurred" $EXIT_FAILURE $LINENO' ERR

# Function to validate arguments
validate_arguments() {
    if [[ $# -lt 2 ]]; then
        error_exit "Usage: $0 <environment> <version>" $EXIT_INVALID_ARGS
    fi
    
    local environment="$1"
    local version="$2"
    
    case "$environment" in
        dev|staging|production)
            ;;
        *)
            error_exit "Invalid environment: $environment. Use: dev, staging, or production" $EXIT_INVALID_ARGS
            ;;
    esac
    
    if [[ ! "$version" =~ ^v[0-9]+\.[0-9]+\.[0-9]+$ ]]; then
        error_exit "Invalid version format: $version. Use format: v1.2.3" $EXIT_INVALID_ARGS
    fi
}

# Example usage
main() {
    validate_arguments "$@"
    
    local environment="$1"
    local version="$2"
    
    echo "Deploying $version to $environment environment"
    
    # Simulate deployment steps with error handling
    deploy_application "$environment" "$version" || error_exit "Deployment failed" $EXIT_FAILURE
    
    echo "Deployment completed successfully"
    exit $EXIT_SUCCESS
}

# Execute main function
main "$@"
```

### Advanced Error Handling Patterns
```bash
#!/bin/bash
set -euo pipefail

# Retry mechanism with exponential backoff
retry_with_backoff() {
    local max_attempts="$1"
    local delay="$2"
    local command=("${@:3}")
    local attempt=1
    
    while [[ $attempt -le $max_attempts ]]; do
        echo "Attempt $attempt of $max_attempts: ${command[*]}"
        
        if "${command[@]}"; then
            echo "Command succeeded on attempt $attempt"
            return 0
        else
            local exit_code=$?
            echo "Command failed with exit code $exit_code"
            
            if [[ $attempt -eq $max_attempts ]]; then
                echo "All $max_attempts attempts failed"
                return $exit_code
            fi
            
            echo "Waiting $delay seconds before retry..."
            sleep "$delay"
            
            # Exponential backoff
            delay=$((delay * 2))
            ((attempt++))
        fi
    done
}

# Circuit breaker pattern
circuit_breaker() {
    local service_name="$1"
    local command=("${@:2}")
    local failure_threshold=5
    local recovery_timeout=60
    local failure_file="/tmp/circuit_breaker_${service_name}_failures"
    local last_failure_file="/tmp/circuit_breaker_${service_name}_last_failure"
    
    # Check if circuit is open
    if [[ -f "$failure_file" ]]; then
        local failures=$(cat "$failure_file")
        local last_failure=$(cat "$last_failure_file" 2>/dev/null || echo 0)
        local current_time=$(date +%s)
        
        if [[ $failures -ge $failure_threshold ]]; then
            if [[ $((current_time - last_failure)) -lt $recovery_timeout ]]; then
                echo "Circuit breaker OPEN for $service_name. Failing fast."
                return 1
            else
                echo "Circuit breaker HALF-OPEN for $service_name. Attempting recovery."
                # Reset failure count for recovery attempt
                echo 0 > "$failure_file"
            fi
        fi
    fi
    
    # Execute command
    if "${command[@]}"; then
        # Success - reset failure count
        echo 0 > "$failure_file"
        return 0
    else
        # Failure - increment counter
        local failures=$(cat "$failure_file" 2>/dev/null || echo 0)
        echo $((failures + 1)) > "$failure_file"
        echo $(date +%s) > "$last_failure_file"
        return 1
    fi
}

# Timeout wrapper
timeout_command() {
    local timeout_duration="$1"
    local command=("${@:2}")
    
    timeout "$timeout_duration" "${command[@]}" || {
        local exit_code=$?
        if [[ $exit_code -eq 124 ]]; then
            echo "Command timed out after $timeout_duration seconds"
        else
            echo "Command failed with exit code $exit_code"
        fi
        return $exit_code
    }
}

# Example usage of advanced patterns
deploy_with_resilience() {
    local service_url="$1"
    
    echo "Testing service connectivity with circuit breaker..."
    if circuit_breaker "api_service" curl -f "$service_url/health"; then
        echo "Service is healthy"
    else
        echo "Service is unhealthy, skipping deployment"
        return 1
    fi
    
    echo "Deploying with retry mechanism..."
    retry_with_backoff 3 5 kubectl apply -f deployment.yaml
    
    echo "Waiting for deployment with timeout..."
    timeout_command 300 kubectl rollout status deployment/myapp
}
```

---

## 📝 Comprehensive Logging System

### Structured Logging Framework
```bash
#!/bin/bash
set -euo pipefail

# Logging configuration
readonly LOG_DIR="/var/log/deployment"
readonly LOG_FILE="$LOG_DIR/deployment-$(date +%Y%m%d).log"
readonly ERROR_LOG_FILE="$LOG_DIR/deployment-error-$(date +%Y%m%d).log"
readonly AUDIT_LOG_FILE="$LOG_DIR/deployment-audit-$(date +%Y%m%d).log"

# Log levels
readonly LOG_LEVEL_DEBUG=0
readonly LOG_LEVEL_INFO=1
readonly LOG_LEVEL_WARN=2
readonly LOG_LEVEL_ERROR=3
readonly LOG_LEVEL_FATAL=4

# Current log level (can be set via environment variable)
readonly CURRENT_LOG_LEVEL="${LOG_LEVEL:-$LOG_LEVEL_INFO}"

# Color codes for console output
readonly COLOR_RED='\033[0;31m'
readonly COLOR_YELLOW='\033[1;33m'
readonly COLOR_GREEN='\033[0;32m'
readonly COLOR_BLUE='\033[0;34m'
readonly COLOR_PURPLE='\033[0;35m'
readonly COLOR_RESET='\033[0m'

# Initialize logging
init_logging() {
    # Create log directory if it doesn't exist
    mkdir -p "$LOG_DIR"
    
    # Set up log rotation (keep last 30 days)
    find "$LOG_DIR" -name "*.log" -mtime +30 -delete 2>/dev/null || true
    
    # Log script start
    log_info "Script started: $0 $*"
    log_info "PID: $$, User: $(whoami), Host: $(hostname)"
    log_info "Working directory: $(pwd)"
}

# Core logging function
write_log() {
    local level="$1"
    local message="$2"
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    local caller="${BASH_SOURCE[2]##*/}:${BASH_LINENO[1]}:${FUNCNAME[2]:-main}"
    
    # Structured log format: JSON-like for easy parsing
    local log_entry="[$timestamp] [$level] [$caller] $message"
    
    # Write to main log file
    echo "$log_entry" >> "$LOG_FILE"
    
    # Write errors to separate error log
    if [[ "$level" == "ERROR" || "$level" == "FATAL" ]]; then
        echo "$log_entry" >> "$ERROR_LOG_FILE"
    fi
}

# Logging functions with level checking
log_debug() {
    [[ $CURRENT_LOG_LEVEL -le $LOG_LEVEL_DEBUG ]] || return 0
    write_log "DEBUG" "$1"
    echo -e "${COLOR_PURPLE}[DEBUG]${COLOR_RESET} $1" >&2
}

log_info() {
    [[ $CURRENT_LOG_LEVEL -le $LOG_LEVEL_INFO ]] || return 0
    write_log "INFO" "$1"
    echo -e "${COLOR_GREEN}[INFO]${COLOR_RESET} $1"
}

log_warn() {
    [[ $CURRENT_LOG_LEVEL -le $LOG_LEVEL_WARN ]] || return 0
    write_log "WARN" "$1"
    echo -e "${COLOR_YELLOW}[WARN]${COLOR_RESET} $1" >&2
}

log_error() {
    [[ $CURRENT_LOG_LEVEL -le $LOG_LEVEL_ERROR ]] || return 0
    write_log "ERROR" "$1"
    echo -e "${COLOR_RED}[ERROR]${COLOR_RESET} $1" >&2
}

log_fatal() {
    write_log "FATAL" "$1"
    echo -e "${COLOR_RED}[FATAL]${COLOR_RESET} $1" >&2
    exit 1
}

# Audit logging for security-sensitive operations
log_audit() {
    local action="$1"
    local resource="$2"
    local result="$3"
    local timestamp=$(date '+%Y-%m-%d %H:%M:%S')
    local user=$(whoami)
    local source_ip="${SSH_CLIENT%% *}"
    
    local audit_entry="[$timestamp] USER=$user SOURCE_IP=${source_ip:-local} ACTION=$action RESOURCE=$resource RESULT=$result"
    echo "$audit_entry" >> "$AUDIT_LOG_FILE"
    log_info "AUDIT: $audit_entry"
}

# Performance logging
log_performance() {
    local operation="$1"
    local duration="$2"
    local status="$3"
    
    log_info "PERFORMANCE: operation=$operation duration=${duration}s status=$status"
}

# Progress logging for long operations
log_progress() {
    local current="$1"
    local total="$2"
    local operation="$3"
    local percentage=$((current * 100 / total))
    
    log_info "PROGRESS: $operation [$current/$total] ($percentage%)"
}
```

### Monitoring Integration
```bash
#!/bin/bash

# Integration with monitoring systems
send_metric() {
    local metric_name="$1"
    local metric_value="$2"
    local metric_type="${3:-gauge}"
    local tags="${4:-}"
    
    # StatsD integration
    if command -v nc >/dev/null 2>&1; then
        echo "${metric_name}:${metric_value}|${metric_type}${tags:+|#$tags}" | nc -u -w1 localhost 8125 2>/dev/null || true
    fi
    
    # Prometheus pushgateway integration
    if [[ -n "${PROMETHEUS_GATEWAY:-}" ]]; then
        curl -X POST "$PROMETHEUS_GATEWAY/metrics/job/deployment/instance/$(hostname)" \
             --data-binary "# TYPE $metric_name $metric_type
$metric_name{$tags} $metric_value" 2>/dev/null || true
    fi
    
    log_debug "Sent metric: $metric_name=$metric_value ($metric_type)"
}

# Alert integration
send_alert() {
    local severity="$1"
    local message="$2"
    local component="${3:-deployment}"
    
    # Slack integration
    if [[ -n "${SLACK_WEBHOOK:-}" ]]; then
        local color="good"
        case "$severity" in
            "ERROR"|"FATAL") color="danger" ;;
            "WARN") color="warning" ;;
        esac
        
        curl -X POST "$SLACK_WEBHOOK" \
             -H 'Content-type: application/json' \
             --data "{\"attachments\":[{\"color\":\"$color\",\"text\":\"[$severity] $component: $message\"}]}" \
             2>/dev/null || true
    fi
    
    # PagerDuty integration for critical alerts
    if [[ "$severity" == "FATAL" && -n "${PAGERDUTY_KEY:-}" ]]; then
        curl -X POST "https://events.pagerduty.com/v2/enqueue" \
             -H "Content-Type: application/json" \
             --data "{
                \"routing_key\": \"$PAGERDUTY_KEY\",
                \"event_action\": \"trigger\",
                \"payload\": {
                    \"summary\": \"$component: $message\",
                    \"severity\": \"critical\",
                    \"source\": \"$(hostname)\"
                }
             }" 2>/dev/null || true
    fi
    
    log_info "Sent $severity alert: $message"
}

# Health check logging
log_health_check() {
    local service="$1"
    local status="$2"
    local response_time="$3"
    local details="${4:-}"
    
    log_info "HEALTH_CHECK: service=$service status=$status response_time=${response_time}ms details=$details"
    
    # Send metrics
    send_metric "health_check_status" "$([[ "$status" == "healthy" ]] && echo 1 || echo 0)" "gauge" "service=$service"
    send_metric "health_check_response_time" "$response_time" "gauge" "service=$service"
    
    # Send alert if unhealthy
    if [[ "$status" != "healthy" ]]; then
        send_alert "ERROR" "Health check failed for $service: $details" "$service"
    fi
}
```

---

## 🎯 Production-Ready Script Template

### Complete DevOps Script with Error Handling and Logging
```bash
#!/bin/bash

# Production DevOps Script Template
# Author: DevOps Team
# Description: Deployment automation with comprehensive error handling and logging

set -euo pipefail

# Script metadata
readonly SCRIPT_NAME="$(basename "$0")"
readonly SCRIPT_VERSION="1.0.0"
readonly SCRIPT_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"

# Configuration
readonly CONFIG_FILE="${SCRIPT_DIR}/config.conf"
readonly LOG_DIR="/var/log/deployment"
readonly LOCK_FILE="/var/run/${SCRIPT_NAME}.lock"
readonly PID_FILE="/var/run/${SCRIPT_NAME}.pid"

# Load configuration if exists
[[ -f "$CONFIG_FILE" ]] && source "$CONFIG_FILE"

# Default configuration
readonly DEFAULT_TIMEOUT="${TIMEOUT:-300}"
readonly DEFAULT_RETRIES="${RETRIES:-3}"
readonly DEFAULT_ENVIRONMENT="${ENVIRONMENT:-development}"

# Exit codes
readonly EXIT_SUCCESS=0
readonly EXIT_FAILURE=1
readonly EXIT_INVALID_ARGS=2
readonly EXIT_CONFIG_ERROR=3
readonly EXIT_LOCK_ERROR=4
readonly EXIT_TIMEOUT=5

# Source logging functions (assuming they're in a separate file)
source "${SCRIPT_DIR}/logging.sh" 2>/dev/null || {
    echo "ERROR: Cannot load logging functions" >&2
    exit $EXIT_CONFIG_ERROR
}

# Cleanup function
cleanup() {
    local exit_code=$?
    
    log_info "Cleaning up..."
    
    # Remove lock file
    [[ -f "$LOCK_FILE" ]] && rm -f "$LOCK_FILE"
    
    # Remove PID file
    [[ -f "$PID_FILE" ]] && rm -f "$PID_FILE"
    
    # Kill background jobs
    jobs -p | xargs -r kill 2>/dev/null || true
    
    # Log completion
    if [[ $exit_code -eq $EXIT_SUCCESS ]]; then
        log_info "Script completed successfully"
        send_metric "deployment_status" 1 "gauge" "script=$SCRIPT_NAME"
    else
        log_error "Script failed with exit code: $exit_code"
        send_metric "deployment_status" 0 "gauge" "script=$SCRIPT_NAME"
        send_alert "ERROR" "Script $SCRIPT_NAME failed with exit code $exit_code"
    fi
    
    log_audit "SCRIPT_END" "$SCRIPT_NAME" "exit_code=$exit_code"
}

# Set up signal handlers
trap cleanup EXIT
trap 'log_fatal "Script interrupted by user"' INT TERM

# Lock mechanism to prevent concurrent execution
acquire_lock() {
    if [[ -f "$LOCK_FILE" ]]; then
        local lock_pid=$(cat "$LOCK_FILE" 2>/dev/null || echo "")
        
        if [[ -n "$lock_pid" ]] && kill -0 "$lock_pid" 2>/dev/null; then
            log_fatal "Script is already running with PID: $lock_pid"
        else
            log_warn "Stale lock file found, removing..."
            rm -f "$LOCK_FILE"
        fi
    fi
    
    echo $$ > "$LOCK_FILE"
    echo $$ > "$PID_FILE"
    log_info "Acquired lock with PID: $$"
}

# Validate environment and dependencies
validate_environment() {
    log_info "Validating environment..."
    
    # Check required commands
    local required_commands=("curl" "jq" "kubectl" "docker")
    for cmd in "${required_commands[@]}"; do
        if ! command -v "$cmd" >/dev/null 2>&1; then
            log_fatal "Required command not found: $cmd"
        fi
    done
    
    # Check required environment variables
    local required_vars=("KUBECONFIG" "DOCKER_REGISTRY")
    for var in "${required_vars[@]}"; do
        if [[ -z "${!var:-}" ]]; then
            log_fatal "Required environment variable not set: $var"
        fi
    done
    
    # Check disk space
    local available_space=$(df /tmp | awk 'NR==2 {print $4}')
    if [[ $available_space -lt 1000000 ]]; then  # Less than 1GB
        log_fatal "Insufficient disk space: ${available_space}KB available"
    fi
    
    log_info "Environment validation completed"
}

# Main deployment function with comprehensive error handling
deploy_application() {
    local environment="$1"
    local version="$2"
    local start_time=$(date +%s)
    
    log_info "Starting deployment: $version to $environment"
    log_audit "DEPLOYMENT_START" "$environment/$version" "SUCCESS"
    
    # Step 1: Pre-deployment validation
    log_info "Step 1: Pre-deployment validation"
    validate_deployment_config "$environment" "$version" || {
        log_audit "DEPLOYMENT_VALIDATION" "$environment/$version" "FAILED"
        log_fatal "Pre-deployment validation failed"
    }
    
    # Step 2: Build and push Docker image
    log_info "Step 2: Building Docker image"
    build_docker_image "$version" || {
        log_audit "IMAGE_BUILD" "$version" "FAILED"
        log_fatal "Docker image build failed"
    }
    
    # Step 3: Deploy to Kubernetes
    log_info "Step 3: Deploying to Kubernetes"
    deploy_to_kubernetes "$environment" "$version" || {
        log_audit "K8S_DEPLOYMENT" "$environment/$version" "FAILED"
        log_error "Kubernetes deployment failed, initiating rollback"
        rollback_deployment "$environment" || log_error "Rollback also failed!"
        return $EXIT_FAILURE
    }
    
    # Step 4: Health checks
    log_info "Step 4: Running health checks"
    run_health_checks "$environment" || {
        log_audit "HEALTH_CHECK" "$environment" "FAILED"
        log_error "Health checks failed, initiating rollback"
        rollback_deployment "$environment" || log_error "Rollback also failed!"
        return $EXIT_FAILURE
    }
    
    # Step 5: Post-deployment tasks
    log_info "Step 5: Post-deployment tasks"
    run_post_deployment_tasks "$environment" "$version"
    
    local end_time=$(date +%s)
    local duration=$((end_time - start_time))
    
    log_performance "deployment" "$duration" "SUCCESS"
    log_audit "DEPLOYMENT_COMPLETE" "$environment/$version" "SUCCESS"
    log_info "Deployment completed successfully in ${duration}s"
    
    # Send success metrics and notifications
    send_metric "deployment_duration" "$duration" "histogram" "environment=$environment,version=$version"
    send_alert "INFO" "Deployment of $version to $environment completed successfully"
}

# Individual deployment functions with error handling
validate_deployment_config() {
    local environment="$1"
    local version="$2"
    
    log_debug "Validating deployment configuration for $environment"
    
    # Check if deployment manifests exist
    local manifest_file="k8s/${environment}/deployment.yaml"
    [[ -f "$manifest_file" ]] || {
        log_error "Deployment manifest not found: $manifest_file"
        return 1
    }
    
    # Validate Kubernetes manifests
    kubectl apply --dry-run=client -f "$manifest_file" >/dev/null || {
        log_error "Invalid Kubernetes manifest: $manifest_file"
        return 1
    }
    
    log_debug "Deployment configuration validation completed"
    return 0
}

build_docker_image() {
    local version="$1"
    local image_tag="${DOCKER_REGISTRY}/myapp:${version}"
    
    log_debug "Building Docker image: $image_tag"
    
    # Build with timeout and retry
    retry_with_backoff 2 10 timeout_command 600 docker build -t "$image_tag" . || {
        log_error "Docker build failed for version: $version"
        return 1
    }
    
    # Push with retry
    retry_with_backoff 3 5 timeout_command 300 docker push "$image_tag" || {
        log_error "Docker push failed for image: $image_tag"
        return 1
    }
    
    log_info "Docker image built and pushed successfully: $image_tag"
    return 0
}

deploy_to_kubernetes() {
    local environment="$1"
    local version="$2"
    
    log_debug "Deploying to Kubernetes environment: $environment"
    
    # Update deployment with new image
    kubectl set image deployment/myapp myapp="${DOCKER_REGISTRY}/myapp:${version}" -n "$environment" || {
        log_error "Failed to update Kubernetes deployment"
        return 1
    }
    
    # Wait for rollout with timeout
    timeout_command 600 kubectl rollout status deployment/myapp -n "$environment" || {
        log_error "Kubernetes rollout failed or timed out"
        return 1
    }
    
    log_info "Kubernetes deployment completed successfully"
    return 0
}

run_health_checks() {
    local environment="$1"
    local service_url="https://api-${environment}.company.com"
    local max_attempts=10
    local attempt=1
    
    log_debug "Running health checks for $environment"
    
    while [[ $attempt -le $max_attempts ]]; do
        log_progress "$attempt" "$max_attempts" "health_check"
        
        local start_time=$(date +%s%3N)
        if curl -f -s "$service_url/health" >/dev/null; then
            local end_time=$(date +%s%3N)
            local response_time=$((end_time - start_time))
            
            log_health_check "myapp" "healthy" "$response_time"
            log_info "Health check passed on attempt $attempt"
            return 0
        else
            log_warn "Health check failed on attempt $attempt"
            [[ $attempt -lt $max_attempts ]] && sleep 10
        fi
        
        ((attempt++))
    done
    
    log_health_check "myapp" "unhealthy" "0" "Failed after $max_attempts attempts"
    log_error "All health check attempts failed"
    return 1
}

rollback_deployment() {
    local environment="$1"
    
    log_warn "Initiating rollback for environment: $environment"
    log_audit "ROLLBACK_START" "$environment" "INITIATED"
    
    # Rollback Kubernetes deployment
    if kubectl rollout undo deployment/myapp -n "$environment"; then
        # Wait for rollback to complete
        if timeout_command 300 kubectl rollout status deployment/myapp -n "$environment"; then
            log_info "Rollback completed successfully"
            log_audit "ROLLBACK_COMPLETE" "$environment" "SUCCESS"
            send_alert "WARN" "Rollback completed for $environment"
            return 0
        else
            log_error "Rollback rollout failed or timed out"
            log_audit "ROLLBACK_COMPLETE" "$environment" "FAILED"
            return 1
        fi
    else
        log_error "Rollback command failed"
        log_audit "ROLLBACK_COMPLETE" "$environment" "FAILED"
        return 1
    fi
}

run_post_deployment_tasks() {
    local environment="$1"
    local version="$2"
    
    log_debug "Running post-deployment tasks"
    
    # Update deployment tracking
    echo "$version" > "/var/lib/deployment/${environment}_current_version"
    
    # Clear application cache if needed
    if [[ "$environment" == "production" ]]; then
        log_info "Clearing production cache"
        # Add cache clearing logic here
    fi
    
    # Update monitoring dashboards
    # Add dashboard update logic here
    
    log_info "Post-deployment tasks completed"
}

# Usage and help
show_usage() {
    cat << EOF
Usage: $SCRIPT_NAME [OPTIONS] <environment> <version>

Deploy application to specified environment.

Arguments:
    environment     Target environment (dev, staging, production)
    version         Application version to deploy (e.g., v1.2.3)

Options:
    -h, --help      Show this help message
    -v, --version   Show script version
    -d, --debug     Enable debug logging
    --dry-run       Validate configuration without deploying

Examples:
    $SCRIPT_NAME staging v1.2.3
    $SCRIPT_NAME --debug production v1.2.3
    $SCRIPT_NAME --dry-run production v1.2.3

EOF
}

# Main function
main() {
    # Initialize logging
    init_logging
    
    log_info "Starting $SCRIPT_NAME v$SCRIPT_VERSION"
    log_audit "SCRIPT_START" "$SCRIPT_NAME" "SUCCESS"
    
    # Parse command line arguments
    local dry_run=false
    local debug=false
    
    while [[ $# -gt 0 ]]; do
        case $1 in
            -h|--help)
                show_usage
                exit $EXIT_SUCCESS
                ;;
            -v|--version)
                echo "$SCRIPT_NAME version $SCRIPT_VERSION"
                exit $EXIT_SUCCESS
                ;;
            -d|--debug)
                debug=true
                export LOG_LEVEL=$LOG_LEVEL_DEBUG
                shift
                ;;
            --dry-run)
                dry_run=true
                shift
                ;;
            -*)
                log_fatal "Unknown option: $1"
                ;;
            *)
                break
                ;;
        esac
    done
    
    # Validate arguments
    if [[ $# -ne 2 ]]; then
        log_error "Invalid number of arguments"
        show_usage
        exit $EXIT_INVALID_ARGS
    fi
    
    local environment="$1"
    local version="$2"
    
    # Validate environment
    case "$environment" in
        dev|staging|production)
            ;;
        *)
            log_fatal "Invalid environment: $environment"
            ;;
    esac
    
    # Validate version format
    if [[ ! "$version" =~ ^v[0-9]+\.[0-9]+\.[0-9]+$ ]]; then
        log_fatal "Invalid version format: $version. Use format: v1.2.3"
    fi
    
    # Acquire lock
    acquire_lock
    
    # Validate environment
    validate_environment
    
    # Perform deployment
    if [[ "$dry_run" == "true" ]]; then
        log_info "Dry run mode - validating configuration only"
        validate_deployment_config "$environment" "$version"
        log_info "Dry run completed successfully"
    else
        deploy_application "$environment" "$version"
    fi
}

# Execute main function with all arguments
main "$@"
```

---

## 📊 Progress Checkpoint

Test your error handling and logging knowledge:

```bash
# Create a test script to verify your understanding
cat > test_error_handling.sh << 'EOF'
#!/bin/bash
set -euo pipefail

# Test error handling patterns
source ./logging.sh  # Assume logging functions exist

test_basic_error_handling() {
    echo "Testing basic error handling..."
    
    # Test command that should fail
    if ! ls /nonexistent/directory 2>/dev/null; then
        log_warn "Expected failure: directory not found"
        return 0
    else
        log_error "Unexpected success"
        return 1
    fi
}

test_retry_mechanism() {
    echo "Testing retry mechanism..."
    
    # Simulate flaky command
    local attempt=1
    local max_attempts=3
    
    while [[ $attempt -le $max_attempts ]]; do
        if [[ $attempt -eq 3 ]]; then
            echo "Success on attempt $attempt"
            return 0
        else
            echo "Failure on attempt $attempt"
            ((attempt++))
            sleep 1
        fi
    done
    
    return 1
}

main() {
    init_logging
    
    log_info "Starting error handling tests"
    
    if test_basic_error_handling; then
        log_info "✓ Basic error handling test passed"
    else
        log_error "✗ Basic error handling test failed"
    fi
    
    if test_retry_mechanism; then
        log_info "✓ Retry mechanism test passed"
    else
        log_error "✗ Retry mechanism test failed"
    fi
    
    log_info "Error handling tests completed"
}

main "$@"
EOF

chmod +x test_error_handling.sh
echo "Created error handling test script"
```

---

## ➡️ Next Steps

You're now ready for:
1. **03-Automation_Scripts** - Real-world DevOps automation examples
2. **04-CI_CD_Integration** - Pipeline scripting and integration
3. Advanced monitoring and alerting integration

---

📄 **File Path:** `/DevOps/02-Linux_System_Administration/04-Shell_Scripting_Automation/02-DevOps_Script_Patterns/01-Error_Handling_And_Logging.md` 