# 🔧 Shell Scripting and Automation: From Bash to Python

## 📖 What This Module Does
Learn to automate everything! Understand what scripting actually is, start with simple Bash scripts (automated Linux commands), then progress to Python scripts for more complex automation. Perfect for DevOps engineers who need to automate repetitive tasks.

## 🤔 What is Scripting? (Complete Beginner Explanation)

### **Think of Scripts Like Recipes**
Instead of cooking the same meal by remembering every step, you write down the recipe once and follow it every time. Scripts work the same way:

- **Manual way:** Type `ls`, then `df -h`, then `free -h`, then `ps aux` every time you want to check your system
- **Script way:** Write all those commands in a file once, then run the file whenever you need it

### **Two Main Types of Scripts in DevOps:**

#### **🔧 Bash Scripts (.sh files) - "Automated Terminal Commands"**
- **What it is:** Just Linux terminal commands written in a text file
- **When to use:** Simple automation, system administration, file operations
- **Example:** Backup files, restart services, check system health

```bash
#!/bin/bash
# This is just terminal commands in a file!
echo "Checking system health..."
df -h          # Same command you'd type in terminal
free -h        # Same command you'd type in terminal
uptime         # Same command you'd type in terminal
```

#### **🐍 Python Scripts (.py files) - "Real Programming"**
- **What it is:** A programming language that can also run Linux commands
- **When to use:** Complex logic, data processing, web APIs, advanced automation
- **Example:** Process log files, connect to databases, create web dashboards

```python
#!/usr/bin/env python3
# This is actual programming that can also run terminal commands
import subprocess
import json
from datetime import datetime

# Python can run Linux commands
result = subprocess.run(['df', '-h'], capture_output=True, text=True)

# But it can also do complex programming things
system_data = {
    "timestamp": datetime.now().isoformat(),
    "disk_usage": result.stdout,
    "status": "healthy" if "100%" not in result.stdout else "warning"
}

print(json.dumps(system_data, indent=2))
```

## 🎯 Learning Objectives
By completing this module, you will:
- ✅ Understand what scripts are and when to use them
- ✅ Write Bash scripts to automate Linux commands
- ✅ Create Python scripts for complex automation
- ✅ Handle errors and logging in scripts
- ✅ Integrate scripts with CI/CD pipelines
- ✅ Build a complete DevOps automation toolkit

## 📋 Prerequisites
- Completed Linux Basics for Beginners module
- Comfortable with Linux command line operations
- Basic understanding of file operations and text editing
- Completed Network Configuration module (recommended)

---

## 📚 Module Structure

### **01-What_Is_Scripting/** 🤔
**Time Investment:** 1 day
**Skill Level:** Complete Beginner
- Understanding scripts vs manual commands
- When to use Bash vs Python
- Setting up your scripting environment
- Your first "Hello World" scripts

### **02-Bash_Scripting_Fundamentals/** 🔧
**Time Investment:** 3-4 days
**Skill Level:** Beginner
- Bash script structure and syntax
- Variables, loops, and conditions
- Command line arguments and user input
- File operations and text processing

### **03-Advanced_Bash_Techniques/** 🛠️
**Time Investment:** 3-4 days
**Skill Level:** Intermediate
- Functions and modular scripts
- Error handling and debugging
- Working with APIs and web services
- System administration scripts

### **04-Python_For_DevOps/** 🐍
**Time Investment:** 4-5 days
**Skill Level:** Intermediate-Advanced
- Python basics for DevOps
- Running Linux commands from Python
- File processing and data manipulation
- API integrations and web automation

### **05-DevOps_Script_Patterns/** 🏗️
**Time Investment:** 3-4 days
**Skill Level:** Advanced
- Deployment automation scripts
- Monitoring and alerting scripts
- Log processing and analysis
- Infrastructure management scripts

### **06-CI_CD_Integration/** 🚀
**Time Investment:** 2-3 days
**Skill Level:** Advanced
- Scripts in CI/CD pipelines
- GitHub Actions automation
- Docker and container scripts
- Production deployment patterns

---

## 🚀 Learning Path

### **Day 1: Understanding Scripts**
**Goal:** Understand what scripting is and create your first scripts

**Practice:**
```bash
# Create your first bash script
echo '#!/bin/bash' > my-first-script.sh
echo 'echo "Hello DevOps World!"' >> my-first-script.sh
chmod +x my-first-script.sh
./my-first-script.sh

# Create your first Python script
echo '#!/usr/bin/env python3' > my-first-script.py
echo 'print("Hello DevOps World!")' >> my-first-script.py
chmod +x my-first-script.py
./my-first-script.py
```

### **Day 2-5: Bash Scripting Mastery**
**Goal:** Automate Linux commands with Bash scripts

**Daily Practice:**
- Write scripts to automate daily tasks
- Practice variables, loops, and conditions
- Create system administration scripts
- Handle user input and command line arguments

### **Day 6-9: Advanced Bash**
**Goal:** Create production-ready Bash scripts

**Daily Practice:**
- Add error handling to scripts
- Create modular, reusable functions
- Integrate with external APIs
- Build comprehensive system tools

### **Day 10-14: Python for DevOps**
**Goal:** Use Python for complex automation

**Daily Practice:**
- Learn Python basics for DevOps tasks
- Process files and data with Python
- Integrate with web APIs and services
- Create monitoring and alerting tools

### **Day 15-18: DevOps Patterns**
**Goal:** Build production automation scripts

**Daily Practice:**
- Create deployment automation
- Build monitoring dashboards
- Process and analyze logs
- Manage infrastructure with scripts

### **Day 19-21: CI/CD Integration**
**Goal:** Integrate scripts with DevOps pipelines

**Daily Practice:**
- Add scripts to GitHub Actions
- Create Docker automation scripts
- Build deployment pipelines
- Monitor and alert on script execution

---

## 🎯 Quick Start Examples

### **Example 1: Simple System Check (Bash)**
```bash
#!/bin/bash
# system-check.sh - Check system health

echo "=== System Health Check ==="
echo "Date: $(date)"
echo ""

echo "Disk Usage:"
df -h | grep -E "^/dev"

echo ""
echo "Memory Usage:"
free -h

echo ""
echo "CPU Load:"
uptime

echo ""
echo "Top 5 Processes:"
ps aux --sort=-%cpu | head -6
```

### **Example 2: Log Analysis (Python)**
```python
#!/usr/bin/env python3
# log-analyzer.py - Analyze system logs

import re
from collections import Counter
from datetime import datetime

def analyze_auth_log():
    """Analyze authentication log for security insights"""
    try:
        with open('/var/log/auth.log', 'r') as f:
            lines = f.readlines()
        
        # Count failed login attempts
        failed_logins = [line for line in lines if 'Failed password' in line]
        
        # Extract IP addresses from failed attempts
        ip_pattern = r'\b(?:[0-9]{1,3}\.){3}[0-9]{1,3}\b'
        attacking_ips = []
        
        for line in failed_logins:
            ips = re.findall(ip_pattern, line)
            attacking_ips.extend(ips)
        
        # Count most common attacking IPs
        ip_counts = Counter(attacking_ips)
        
        print("=== Security Analysis ===")
        print(f"Total failed login attempts: {len(failed_logins)}")
        print("\nTop attacking IPs:")
        for ip, count in ip_counts.most_common(5):
            print(f"  {ip}: {count} attempts")
            
    except FileNotFoundError:
        print("Auth log file not found")
    except PermissionError:
        print("Permission denied - run with sudo")

if __name__ == "__main__":
    analyze_auth_log()
```

### **Example 3: Deployment Script (Advanced Bash)**
```bash
#!/bin/bash
# deploy.sh - Application deployment script

set -euo pipefail  # Exit on error, undefined vars, pipe failures

# Configuration
APP_NAME="my-app"
DEPLOY_DIR="/opt/$APP_NAME"
BACKUP_DIR="/opt/backups"
LOG_FILE="/var/log/deploy.log"

# Logging function
log() {
    echo "[$(date +'%Y-%m-%d %H:%M:%S')] $1" | tee -a "$LOG_FILE"
}

# Error handling
error_exit() {
    log "ERROR: $1"
    exit 1
}

# Backup current version
backup_current() {
    if [ -d "$DEPLOY_DIR" ]; then
        local backup_name="$APP_NAME-$(date +%Y%m%d-%H%M%S)"
        log "Creating backup: $backup_name"
        cp -r "$DEPLOY_DIR" "$BACKUP_DIR/$backup_name" || error_exit "Backup failed"
    fi
}

# Deploy new version
deploy() {
    local version=$1
    log "Deploying $APP_NAME version $version"
    
    # Download and extract
    wget -O "/tmp/$APP_NAME-$version.tar.gz" \
        "https://releases.example.com/$APP_NAME-$version.tar.gz" || \
        error_exit "Download failed"
    
    # Extract to deployment directory
    mkdir -p "$DEPLOY_DIR"
    tar -xzf "/tmp/$APP_NAME-$version.tar.gz" -C "$DEPLOY_DIR" || \
        error_exit "Extraction failed"
    
    # Set permissions
    chown -R app:app "$DEPLOY_DIR"
    chmod +x "$DEPLOY_DIR/bin/$APP_NAME"
    
    log "Deployment completed successfully"
}

# Main deployment process
main() {
    local version=${1:-"latest"}
    
    log "Starting deployment of $APP_NAME version $version"
    
    backup_current
    deploy "$version"
    
    # Restart service
    systemctl restart "$APP_NAME" || error_exit "Service restart failed"
    
    # Verify deployment
    sleep 5
    if systemctl is-active --quiet "$APP_NAME"; then
        log "Deployment successful - service is running"
    else
        error_exit "Deployment failed - service is not running"
    fi
}

# Run deployment
main "$@"
```

---

## 📊 Progress Checkpoints

### **Scripting Fundamentals** ✅
- [ ] Understand the difference between Bash and Python scripts
- [ ] Can create and execute simple scripts
- [ ] Know when to use each type of script
- [ ] Can make scripts executable and run them

### **Bash Scripting Mastery** ✅
- [ ] Can write scripts with variables, loops, and conditions
- [ ] Can handle command line arguments and user input
- [ ] Can process files and text with Bash
- [ ] Can create modular scripts with functions

### **Advanced Bash Techniques** ✅
- [ ] Can implement proper error handling
- [ ] Can debug and troubleshoot scripts
- [ ] Can integrate with external APIs
- [ ] Can create production-ready system administration scripts

### **Python for DevOps** ✅
- [ ] Know Python basics for DevOps tasks
- [ ] Can run Linux commands from Python
- [ ] Can process files and data with Python
- [ ] Can integrate with web APIs and services

### **DevOps Automation Patterns** ✅
- [ ] Can create deployment automation scripts
- [ ] Can build monitoring and alerting tools
- [ ] Can process and analyze logs programmatically
- [ ] Can manage infrastructure with scripts

### **CI/CD Integration** ✅
- [ ] Can integrate scripts with GitHub Actions
- [ ] Can create Docker automation scripts
- [ ] Can build complete deployment pipelines
- [ ] Can monitor and maintain script execution

---

## 🛠️ Essential Tools and Setup

### **Development Environment**
```bash
# Install essential tools
sudo apt update
sudo apt install -y vim nano git curl wget

# Install Python and pip
sudo apt install -y python3 python3-pip

# Install useful Python packages for DevOps
pip3 install requests pyyaml jinja2 psutil

# Install shellcheck for Bash script validation
sudo apt install -y shellcheck
```

### **Script Development Best Practices**
```bash
# Always start scripts with shebang
#!/bin/bash          # For Bash scripts
#!/usr/bin/env python3   # For Python scripts

# Make scripts executable
chmod +x script.sh

# Validate Bash scripts
shellcheck script.sh

# Test Python scripts
python3 -m py_compile script.py
```

---

## ➡️ Next Steps

**You're ready for advanced DevOps modules when you can:**
- [ ] Create both Bash and Python scripts confidently
- [ ] Automate complex system administration tasks
- [ ] Handle errors and edge cases in scripts
- [ ] Integrate scripts with CI/CD pipelines
- [ ] Build monitoring and deployment automation
- [ ] Debug and maintain production scripts

**Continue to:**
1. **Module 06: CI/CD Pipelines** - Use your scripts in automated pipelines
2. **Module 07: Infrastructure as Code** - Terraform and Ansible automation
3. **Module 09: Monitoring & Observability** - Advanced monitoring scripts

---

## 💡 Pro Tips for Scripting Success

### **Start Simple**
- Begin with automating commands you already know
- Don't try to write complex scripts immediately
- Test each part of your script separately

### **Use Version Control**
- Put all your scripts in Git repositories
- Comment your code extensively
- Create README files for complex scripts

### **Security Best Practices**
- Never hardcode passwords in scripts
- Use environment variables for sensitive data
- Validate all user input
- Run scripts with minimal required permissions

### **Testing and Debugging**
- Test scripts in safe environments first
- Use `set -x` in Bash for debugging
- Add logging to track script execution
- Have rollback plans for deployment scripts

---

📄 **File Path:** `/DevOps/02-Linux_System_Administration/06-Shell_Scripting_Automation/README.md` 