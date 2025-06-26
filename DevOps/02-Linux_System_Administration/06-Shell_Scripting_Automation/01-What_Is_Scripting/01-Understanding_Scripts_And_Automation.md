# Understanding Scripts and Automation: Your First Step to DevOps Automation

## 📖 What This File Does
Learn what scripts actually are, why they're essential for DevOps, and create your very first automation scripts. Perfect for complete beginners who have never written a script before but want to understand this fundamental DevOps skill.

## 🤔 What is a Script? (Simple Explanation)

### **Think of Scripts Like Smart To-Do Lists**

Imagine you check your system health every morning by typing these commands:
```bash
# What you do manually every day:
df -h          # Check disk space
free -h        # Check memory
uptime         # Check system load
ps aux | head  # Check top processes
```

**Instead of typing these every single day**, you can:
1. **Write them once** in a text file
2. **Run the file** and all commands execute automatically
3. **Save time** and never forget a step

That text file with commands? **That's a script!**

### **Scripts = Automation = DevOps Superpower**

- **Manual DevOps:** Type 50 commands to deploy an application
- **Automated DevOps:** Run 1 script that does all 50 commands perfectly
- **Result:** Deploy faster, make fewer mistakes, focus on important work

---

## 🔧 Two Types of Scripts You'll Use

### **Type 1: Bash Scripts (.sh files) - "Terminal Commands in a File"**

**What it is:** Just the Linux commands you already know, written in a text file

**Example - Daily System Check:**
```bash
#!/bin/bash
# daily-check.sh - Check system health

echo "=== Daily System Health Check ==="
echo "Date: $(date)"
echo ""

echo "💾 Disk Usage:"
df -h | grep -v tmpfs

echo ""
echo "🧠 Memory Usage:"
free -h

echo ""
echo "⚡ System Load:"
uptime

echo ""
echo "🔥 Top 5 CPU Processes:"
ps aux --sort=-%cpu | head -6
```

**When to use Bash scripts:**
- ✅ Automate Linux commands you already know
- ✅ System administration tasks
- ✅ Simple file operations
- ✅ Starting/stopping services
- ✅ Basic automation

### **Type 2: Python Scripts (.py files) - "Real Programming Power"**

**What it is:** A programming language that can also run Linux commands

**Example - Log Analysis:**
```python
#!/usr/bin/env python3
# log-analyzer.py - Analyze system logs

import subprocess
import json
from datetime import datetime

def get_system_info():
    """Get system information using Linux commands"""
    
    # Run Linux commands from Python
    disk_result = subprocess.run(['df', '-h'], capture_output=True, text=True)
    memory_result = subprocess.run(['free', '-h'], capture_output=True, text=True)
    
    # Process the data (something Bash can't do easily)
    system_data = {
        "timestamp": datetime.now().isoformat(),
        "disk_usage": disk_result.stdout.split('\n')[1:3],  # Parse disk info
        "memory_info": memory_result.stdout.split('\n')[1],   # Parse memory info
        "status": "healthy"  # We can add logic here
    }
    
    # Output as JSON (perfect for APIs and monitoring)
    return json.dumps(system_data, indent=2)

if __name__ == "__main__":
    print(get_system_info())
```

**When to use Python scripts:**
- ✅ Complex data processing
- ✅ Web API integrations
- ✅ Advanced logic and calculations
- ✅ Working with databases
- ✅ Creating web dashboards

---

## 🎯 Your First Scripts - Hands-On Practice

### **Exercise 1: Create Your First Bash Script**

**Step 1: Create the script file**
```bash
# Create your first script
nano my-first-script.sh
```

**Step 2: Add this content**
```bash
#!/bin/bash
# My first DevOps script!

echo "🚀 Hello DevOps World!"
echo "I am: $(whoami)"
echo "Today is: $(date)"
echo "I'm located at: $(pwd)"
echo "My system info: $(uname -a)"

echo ""
echo "🎉 Congratulations! You just ran your first script!"
```

**Step 3: Make it executable and run it**
```bash
# Make the script executable
chmod +x my-first-script.sh

# Run your script
./my-first-script.sh
```

**🎉 You just automated 5 commands into 1 script!**

### **Exercise 2: Create Your First Python Script**

**Step 1: Create the Python script**
```bash
nano my-first-python-script.py
```

**Step 2: Add this content**
```python
#!/usr/bin/env python3
# My first Python DevOps script!

import os
import subprocess
from datetime import datetime

def main():
    print("🐍 Hello from Python!")
    print(f"Current user: {os.getenv('USER')}")
    print(f"Current time: {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}")
    print(f"Current directory: {os.getcwd()}")
    
    # Run a Linux command from Python
    result = subprocess.run(['whoami'], capture_output=True, text=True)
    print(f"Command output: {result.stdout.strip()}")
    
    print("\n🎉 You just ran your first Python script!")

if __name__ == "__main__":
    main()
```

**Step 3: Make it executable and run it**
```bash
# Make the script executable
chmod +x my-first-python-script.py

# Run your Python script
./my-first-python-script.py
```

**🎉 You just used Python to run Linux commands!**

---

## 🛠️ Understanding Script Structure

### **Bash Script Anatomy**
```bash
#!/bin/bash                    # ← Shebang: tells system this is a Bash script
# This is a comment            # ← Comments explain what code does

# Variables (like containers for data)
NAME="DevOps Engineer"
DATE=$(date)                   # ← Command substitution

# Output to user
echo "Hello $NAME"             # ← Using variables
echo "Today is $DATE"

# Run commands
ls -la                         # ← Same commands you type in terminal
df -h
```

### **Python Script Anatomy**
```python
#!/usr/bin/env python3         # ← Shebang: tells system this is Python
# This is a comment

# Import modules (like toolboxes)
import os
import subprocess

# Variables
name = "DevOps Engineer"       # ← Python variables
current_time = datetime.now()

# Functions (reusable code blocks)
def greet_user():
    print(f"Hello {name}")     # ← f-strings for formatting
    
# Run Linux commands
result = subprocess.run(['ls', '-la'], capture_output=True, text=True)
print(result.stdout)

# Call functions
greet_user()
```

---

## 🎯 Practical Exercise: System Information Script

Let's create a useful script that gathers system information:

### **Version 1: Bash Script**
```bash
#!/bin/bash
# system-info.sh - Gather system information

echo "=== SYSTEM INFORMATION REPORT ==="
echo "Generated: $(date)"
echo "By: $(whoami)"
echo ""

echo "🖥️  SYSTEM DETAILS:"
echo "Hostname: $(hostname)"
echo "OS: $(cat /etc/os-release | grep PRETTY_NAME | cut -d'"' -f2)"
echo "Kernel: $(uname -r)"
echo "Uptime: $(uptime -p)"

echo ""
echo "💾 STORAGE:"
df -h | grep -E "^/dev" | while read line; do
    echo "  $line"
done

echo ""
echo "🧠 MEMORY:"
free -h | grep -E "^Mem|^Swap"

echo ""
echo "🌐 NETWORK:"
ip addr show | grep -E "inet.*global" | awk '{print "  " $2 " on " $NF}'

echo ""
echo "📊 TOP PROCESSES:"
ps aux --sort=-%cpu | head -6

echo ""
echo "✅ Report complete!"
```

### **Version 2: Python Script (More Advanced)**
```python
#!/usr/bin/env python3
# system-info.py - Advanced system information

import subprocess
import json
import psutil
from datetime import datetime

def run_command(command):
    """Run a shell command and return output"""
    try:
        result = subprocess.run(command, shell=True, capture_output=True, text=True)
        return result.stdout.strip()
    except Exception as e:
        return f"Error: {e}"

def get_system_info():
    """Gather comprehensive system information"""
    
    info = {
        "timestamp": datetime.now().isoformat(),
        "hostname": run_command("hostname"),
        "os": run_command("cat /etc/os-release | grep PRETTY_NAME | cut -d'\"' -f2"),
        "kernel": run_command("uname -r"),
        "uptime": run_command("uptime -p"),
        "cpu_count": psutil.cpu_count(),
        "cpu_percent": psutil.cpu_percent(interval=1),
        "memory": {
            "total": psutil.virtual_memory().total // (1024**3),  # GB
            "available": psutil.virtual_memory().available // (1024**3),  # GB
            "percent": psutil.virtual_memory().percent
        },
        "disk": []
    }
    
    # Get disk information
    for partition in psutil.disk_partitions():
        try:
            usage = psutil.disk_usage(partition.mountpoint)
            info["disk"].append({
                "device": partition.device,
                "mountpoint": partition.mountpoint,
                "total_gb": usage.total // (1024**3),
                "used_gb": usage.used // (1024**3),
                "percent": (usage.used / usage.total) * 100
            })
        except PermissionError:
            continue
    
    return info

def main():
    print("=== ADVANCED SYSTEM INFORMATION ===")
    
    # Get system info
    info = get_system_info()
    
    # Display in readable format
    print(f"Generated: {info['timestamp']}")
    print(f"Hostname: {info['hostname']}")
    print(f"OS: {info['os']}")
    print(f"Kernel: {info['kernel']}")
    print(f"Uptime: {info['uptime']}")
    print(f"CPU: {info['cpu_count']} cores at {info['cpu_percent']}% usage")
    print(f"Memory: {info['memory']['available']}GB available of {info['memory']['total']}GB ({info['memory']['percent']}% used)")
    
    print("\nDisk Usage:")
    for disk in info['disk']:
        print(f"  {disk['device']}: {disk['used_gb']}GB/{disk['total_gb']}GB ({disk['percent']:.1f}% used)")
    
    # Also save as JSON for other tools to use
    with open('system-info.json', 'w') as f:
        json.dump(info, f, indent=2)
    
    print("\n✅ Report saved to system-info.json")

if __name__ == "__main__":
    main()
```

---

## 📊 Practice Exercises

### **Exercise 1: Backup Script (Bash)**
Create a script that backs up your important files:

```bash
#!/bin/bash
# backup.sh - Simple backup script

BACKUP_DIR="$HOME/backups"
DATE=$(date +%Y%m%d-%H%M%S)
BACKUP_NAME="backup-$DATE"

echo "🔄 Creating backup: $BACKUP_NAME"

# Create backup directory
mkdir -p "$BACKUP_DIR"

# Backup important directories
tar -czf "$BACKUP_DIR/$BACKUP_NAME.tar.gz" \
    "$HOME/Documents" \
    "$HOME/scripts" \
    "$HOME/.bashrc" \
    2>/dev/null

echo "✅ Backup completed: $BACKUP_DIR/$BACKUP_NAME.tar.gz"
echo "📊 Backup size: $(du -h "$BACKUP_DIR/$BACKUP_NAME.tar.gz" | cut -f1)"
```

### **Exercise 2: File Organizer (Python)**
Create a script that organizes files by type:

```python
#!/usr/bin/env python3
# organize-files.py - Organize files by extension

import os
import shutil
from pathlib import Path

def organize_files(directory):
    """Organize files in directory by extension"""
    
    directory = Path(directory)
    
    # File type mappings
    file_types = {
        'images': ['.jpg', '.jpeg', '.png', '.gif', '.bmp'],
        'documents': ['.pdf', '.doc', '.docx', '.txt', '.md'],
        'scripts': ['.sh', '.py', '.js', '.html', '.css'],
        'archives': ['.zip', '.tar', '.gz', '.rar']
    }
    
    # Create directories for each file type
    for folder in file_types.keys():
        (directory / folder).mkdir(exist_ok=True)
    
    # Organize files
    for file_path in directory.iterdir():
        if file_path.is_file():
            file_ext = file_path.suffix.lower()
            
            # Find which category this file belongs to
            moved = False
            for folder, extensions in file_types.items():
                if file_ext in extensions:
                    dest = directory / folder / file_path.name
                    shutil.move(str(file_path), str(dest))
                    print(f"Moved {file_path.name} to {folder}/")
                    moved = True
                    break
            
            if not moved and file_ext:
                # Create folder for unknown extensions
                ext_folder = directory / f"{file_ext[1:]}_files"
                ext_folder.mkdir(exist_ok=True)
                dest = ext_folder / file_path.name
                shutil.move(str(file_path), str(dest))
                print(f"Moved {file_path.name} to {ext_folder.name}/")

if __name__ == "__main__":
    target_dir = input("Enter directory to organize (or press Enter for current): ")
    if not target_dir:
        target_dir = "."
    
    organize_files(target_dir)
    print("✅ File organization completed!")
```

---

## 📋 Success Checklist

**✅ Understanding Scripts**
- [ ] Can explain what a script is in simple terms
- [ ] Understand the difference between Bash and Python scripts
- [ ] Know when to use each type of script
- [ ] Can identify the shebang line and its purpose

**✅ Creating Scripts**
- [ ] Can create a simple Bash script
- [ ] Can create a simple Python script
- [ ] Can make scripts executable with `chmod +x`
- [ ] Can run scripts from the command line

**✅ Script Structure**
- [ ] Understand comments and why they're important
- [ ] Can use variables in both Bash and Python
- [ ] Can run Linux commands from scripts
- [ ] Can organize code with functions (Python) or sections (Bash)

**✅ Practical Application**
- [ ] Created a system information script
- [ ] Created a backup automation script
- [ ] Can modify existing scripts for your needs
- [ ] Feel confident about learning more advanced scripting

---

## 🚀 What's Next?

Now that you understand what scripts are and have created your first ones, you're ready to:

1. **Learn Bash scripting fundamentals** - Variables, loops, conditions
2. **Master Python for DevOps** - File processing, APIs, data handling
3. **Build real automation tools** - Deployment scripts, monitoring tools
4. **Integrate with DevOps pipelines** - GitHub Actions, CI/CD automation

**Remember:** Every DevOps expert started exactly where you are now. Scripts are just tools to make your work easier and more reliable!

---

## 💡 Pro Tips for Beginners

### **Start Small**
- Begin with automating commands you already know
- Don't try to write complex scripts immediately
- Test each part of your script separately

### **Practice Daily**
- Try to automate one small task each day
- Keep a collection of useful scripts
- Share scripts with teammates and learn from others

### **Learn by Doing**
- Copy examples and modify them
- Break things and fix them (in safe environments)
- Read other people's scripts to learn new techniques

### **Document Everything**
- Add comments explaining what your scripts do
- Create README files for complex scripts
- Keep notes on what you learn

---

📄 **File Path:** `/DevOps/02-Linux_System_Administration/06-Shell_Scripting_Automation/01-What_Is_Scripting/01-Understanding_Scripts_And_Automation.md` 