# 📊 System Monitoring and Logging: Observability for DevOps

## 📖 What This Module Does
Master Linux system monitoring and log management - essential skills for maintaining healthy production systems. Learn to proactively monitor system performance, analyze logs for troubleshooting, and set up automated monitoring solutions.

## 🎯 Learning Objectives
By completing this module, you will:
- ✅ Monitor system performance (CPU, memory, disk, network)
- ✅ Manage and analyze system logs effectively
- ✅ Set up automated monitoring and alerting
- ✅ Troubleshoot system issues using monitoring data
- ✅ Implement log rotation and retention policies
- ✅ Create custom monitoring dashboards and scripts

## 📋 Prerequisites
- Completed Linux Basics for Beginners module
- Comfortable with Linux command line operations
- Basic understanding of system processes and services
- Familiarity with text editors and file operations

---

## 📚 Module Structure

### **01-System_Performance_Monitoring/**
**Time Investment:** 2-3 days
- CPU, memory, and disk monitoring
- Network performance analysis
- Process monitoring and analysis
- Performance bottleneck identification

### **02-Log_Management_And_Analysis/**
**Time Investment:** 2-3 days
- Understanding system logs
- Log analysis tools and techniques
- Log rotation and retention
- Centralized logging setup

### **03-Automated_Monitoring_Setup/**
**Time Investment:** 2-3 days
- Setting up monitoring tools (htop, iotop, netstat)
- Creating monitoring scripts
- Alerting and notification systems
- Dashboard creation and customization

### **04-Troubleshooting_Techniques/**
**Time Investment:** 1-2 days
- Systematic troubleshooting approaches
- Using monitoring data for diagnosis
- Performance optimization strategies
- Common issue resolution patterns

---

## 🎯 Learning Path

### **Day 1-3: Performance Monitoring Mastery**
**Goal:** Understand and monitor all system resources

**Daily Practice:**
```bash
# Monitor system resources
top                    # Real-time process monitoring
htop                   # Enhanced process viewer
free -h                # Memory usage
df -h                  # Disk space
iostat                 # I/O statistics
netstat -tuln          # Network connections
```

### **Day 4-6: Log Analysis Skills**
**Goal:** Master log analysis and management

**Daily Practice:**
```bash
# Explore system logs
journalctl             # Systemd logs
tail -f /var/log/syslog    # Follow system log
grep ERROR /var/log/*  # Find error messages
logrotate              # Manage log rotation
```

### **Day 7-9: Automated Monitoring**
**Goal:** Set up automated monitoring solutions

**Daily Practice:**
- Create monitoring scripts
- Set up alerting mechanisms
- Build custom dashboards
- Practice automated responses

### **Day 10-11: Troubleshooting Mastery**
**Goal:** Diagnose and resolve system issues

**Daily Practice:**
- Simulate system problems
- Practice systematic diagnosis
- Optimize system performance
- Document troubleshooting procedures

---

## 🔧 Essential Monitoring Tools

### **Real-Time Monitoring**
```bash
# Process monitoring
top                    # Basic process monitor
htop                   # Enhanced process viewer
ps aux                 # Process snapshot
pgrep/pkill           # Find/kill processes by name

# Resource monitoring  
free -h                # Memory usage
df -h                  # Disk space
du -sh /path           # Directory size
lsof                   # Open files
```

### **Network Monitoring**
```bash
# Network connections
netstat -tuln          # Listening ports
ss -tuln               # Modern netstat replacement
lsof -i                # Network files
iftop                  # Network bandwidth usage
nload                  # Network load monitor
```

### **System Information**
```bash
# Hardware information
lscpu                  # CPU information
lsmem                  # Memory information
lsblk                  # Block devices
lspci                  # PCI devices
lsusb                  # USB devices
```

---

## 📝 Log Management Essentials

### **Important Log Locations**
```bash
# System logs
/var/log/syslog        # General system messages
/var/log/auth.log      # Authentication attempts
/var/log/kern.log      # Kernel messages
/var/log/dmesg         # Boot messages

# Service logs
/var/log/apache2/      # Web server logs
/var/log/nginx/        # Nginx logs
/var/log/mysql/        # Database logs

# Application logs
/var/log/application.log   # Custom application logs
```

### **Log Analysis Commands**
```bash
# Viewing logs
cat /var/log/syslog    # View entire log
tail -f /var/log/syslog    # Follow log in real-time
head -n 50 /var/log/syslog # First 50 lines
less /var/log/syslog   # Page through log

# Searching logs
grep "error" /var/log/syslog   # Find error messages
grep -i "failed" /var/log/*    # Case-insensitive search
awk '/ERROR/ {print $0}' /var/log/syslog   # AWK filtering
```

---

## 🎯 Hands-On Projects

### **Project 1: System Health Dashboard**
**Time:** 3-4 hours
**Goal:** Create a comprehensive system monitoring script

```bash
#!/bin/bash
# System Health Dashboard

echo "=== SYSTEM HEALTH DASHBOARD ==="
echo "Generated: $(date)"
echo ""

# CPU Usage
echo "CPU Usage:"
top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d'%' -f1

# Memory Usage
echo "Memory Usage:"
free -h | awk 'NR==2{printf "Used: %s/%s (%.2f%%)\n", $3,$2,$3*100/$2}'

# Disk Usage
echo "Disk Usage:"
df -h | awk '$NF=="/"{printf "Used: %s/%s (%s)\n", $3,$2,$5}'

# Network Connections
echo "Active Network Connections:"
netstat -tuln | wc -l

# Top Processes
echo "Top 5 CPU Processes:"
ps aux --sort=-%cpu | head -6

# System Load
echo "System Load:"
uptime

# Recent Errors
echo "Recent Errors (last 10):"
grep -i error /var/log/syslog | tail -10
```

### **Project 2: Log Analysis Toolkit**
**Time:** 2-3 hours
**Goal:** Build automated log analysis tools

```bash
#!/bin/bash
# Log Analysis Toolkit

analyze_auth_log() {
    echo "=== AUTHENTICATION ANALYSIS ==="
    
    # Failed login attempts
    echo "Failed SSH attempts:"
    grep "Failed password" /var/log/auth.log | wc -l
    
    # Successful logins
    echo "Successful logins:"
    grep "Accepted password" /var/log/auth.log | wc -l
    
    # Top attacking IPs
    echo "Top attacking IPs:"
    grep "Failed password" /var/log/auth.log | \
    awk '{print $(NF-3)}' | sort | uniq -c | sort -nr | head -5
}

analyze_system_log() {
    echo "=== SYSTEM LOG ANALYSIS ==="
    
    # Error count
    echo "Total errors today:"
    grep "$(date +%Y-%m-%d)" /var/log/syslog | grep -i error | wc -l
    
    # Warning count
    echo "Total warnings today:"
    grep "$(date +%Y-%m-%d)" /var/log/syslog | grep -i warning | wc -l
    
    # Most common errors
    echo "Most common errors:"
    grep -i error /var/log/syslog | awk '{print $5}' | sort | uniq -c | sort -nr | head -5
}

# Run analysis
analyze_auth_log
echo ""
analyze_system_log
```

### **Project 3: Automated Monitoring Setup**
**Time:** 4-5 hours
**Goal:** Set up automated monitoring with alerts

```bash
#!/bin/bash
# Automated System Monitor with Alerts

# Configuration
CPU_THRESHOLD=80
MEMORY_THRESHOLD=85
DISK_THRESHOLD=90
LOG_FILE="/var/log/system-monitor.log"
ALERT_EMAIL="admin@company.com"

# Logging function
log_message() {
    echo "[$(date +'%Y-%m-%d %H:%M:%S')] $1" >> "$LOG_FILE"
}

# Check CPU usage
check_cpu() {
    cpu_usage=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d'%' -f1 | cut -d' ' -f1)
    if (( $(echo "$cpu_usage > $CPU_THRESHOLD" | bc -l) )); then
        log_message "ALERT: High CPU usage: ${cpu_usage}%"
        send_alert "High CPU Usage" "CPU usage is ${cpu_usage}%"
    fi
}

# Check memory usage
check_memory() {
    memory_usage=$(free | grep Mem | awk '{printf "%.0f", $3/$2 * 100.0}')
    if [ "$memory_usage" -gt "$MEMORY_THRESHOLD" ]; then
        log_message "ALERT: High memory usage: ${memory_usage}%"
        send_alert "High Memory Usage" "Memory usage is ${memory_usage}%"
    fi
}

# Check disk usage
check_disk() {
    disk_usage=$(df / | awk 'NR==2{print $5}' | cut -d'%' -f1)
    if [ "$disk_usage" -gt "$DISK_THRESHOLD" ]; then
        log_message "ALERT: High disk usage: ${disk_usage}%"
        send_alert "High Disk Usage" "Disk usage is ${disk_usage}%"
    fi
}

# Send alert (placeholder - replace with actual notification system)
send_alert() {
    local subject="$1"
    local message="$2"
    echo "ALERT: $subject - $message" | mail -s "$subject" "$ALERT_EMAIL"
    # Alternative: Send to Slack, PagerDuty, etc.
}

# Main monitoring loop
main() {
    log_message "Starting system monitoring"
    
    while true; do
        check_cpu
        check_memory
        check_disk
        sleep 60  # Check every minute
    done
}

# Run as daemon
main &
```

---

## 📊 Progress Checkpoints

### **Performance Monitoring Mastery** ✅
- [ ] Can monitor CPU, memory, and disk usage in real-time
- [ ] Understands process monitoring and can identify resource hogs
- [ ] Can analyze network performance and connections
- [ ] Knows how to identify performance bottlenecks

### **Log Management Skills** ✅
- [ ] Knows location and purpose of major system logs
- [ ] Can search and filter logs effectively
- [ ] Understands log rotation and retention policies
- [ ] Can correlate log events for troubleshooting

### **Automated Monitoring** ✅
- [ ] Can set up automated monitoring scripts
- [ ] Understands alerting thresholds and notifications
- [ ] Can create custom monitoring dashboards
- [ ] Knows how to integrate with monitoring systems

### **Troubleshooting Expertise** ✅
- [ ] Can systematically diagnose system issues
- [ ] Uses monitoring data effectively for problem resolution
- [ ] Understands performance optimization techniques
- [ ] Can document and share troubleshooting procedures

---

## 🔧 Troubleshooting Quick Reference

### **High CPU Usage**
```bash
# Identify CPU-intensive processes
top -o %CPU
ps aux --sort=-%cpu | head -10

# Check for runaway processes
ps aux | awk '$3 > 50.0 {print $0}'

# Monitor CPU over time
sar -u 1 10
```

### **Memory Issues**
```bash
# Check memory usage details
free -h
cat /proc/meminfo

# Find memory-intensive processes
ps aux --sort=-%mem | head -10

# Check for memory leaks
valgrind --leak-check=full ./program
```

### **Disk Problems**
```bash
# Check disk space
df -h
du -sh /* | sort -hr

# Find large files
find / -type f -size +100M 2>/dev/null

# Check disk I/O
iostat -x 1 5
iotop
```

### **Network Issues**
```bash
# Check network connectivity
ping google.com
traceroute google.com

# Monitor network usage
iftop
nload
ss -tuln
```

---

## ➡️ Next Steps

**You're ready for the next module when you can:**
- [ ] Monitor all system resources effectively
- [ ] Analyze logs to troubleshoot issues
- [ ] Set up automated monitoring and alerting
- [ ] Optimize system performance based on monitoring data
- [ ] Create comprehensive monitoring documentation

**Continue to:**
1. **05-Network_Configuration** - Network monitoring and security
2. **06-Shell_Scripting_Automation** - Automate monitoring tasks
3. **Module 09: Monitoring & Observability** - Advanced monitoring tools

---

📄 **File Path:** `/DevOps/02-Linux_System_Administration/04-System_Monitoring_And_Logging/README.md` 