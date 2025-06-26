# Python Equivalents for Linux Commands: Bridging Programming and System Administration

## 📖 What This File Does
Help Python developers understand Linux commands by showing equivalent Python code. Learn how Bash scripting relates to Python programming and when to use each approach in DevOps.

## 🎯 Learning Objectives
- Understand Linux commands through familiar Python concepts
- Learn when to use Bash vs Python for automation
- Master file operations in both environments
- Bridge the gap between programming and system administration
- Choose the right tool for DevOps tasks

## 📋 Prerequisites
- Basic Python knowledge (variables, functions, file operations)
- Familiarity with Python's `os`, `subprocess`, and `pathlib` modules
- Understanding of Linux navigation from previous lessons

---

## 🤝 **How Bash and Python Relate**

### **🔍 Fundamental Differences:**

**Bash (Shell Scripting):**
- **Purpose**: Automate command-line operations
- **Strength**: System administration, file operations, process management
- **Best for**: Simple automation, system scripts, CI/CD pipelines

**Python:**
- **Purpose**: General programming language
- **Strength**: Complex logic, data processing, API interactions
- **Best for**: Complex automation, data analysis, application development

### **🎯 When to Use Each:**

```bash
# Use Bash for:
ls -la | grep ".txt" | wc -l        # Simple file operations
systemctl start nginx              # System service management
chmod +x deploy.sh                 # File permissions
cp backup.tar.gz /backups/         # File operations

# Use Python for:
# - Complex data processing
# - API interactions
# - Error handling and logging
# - Multi-step business logic
# - Cross-platform compatibility
```

---

## 📁 **File Operations: Python vs Linux**

### **1. Navigation and Listing**

#### **Linux Commands:**
```bash
pwd                    # Print working directory
ls                     # List files
ls -la                 # List with details
cd /path/to/directory  # Change directory
cd ..                  # Go up one level
cd ~                   # Go to home directory
```

#### **Python Equivalents:**
```python
import os
from pathlib import Path

# Print working directory
print(os.getcwd())
# or
print(Path.cwd())

# List files
print(os.listdir('.'))
# or
print(list(Path('.').iterdir()))

# List with details (like ls -la)
import stat
from datetime import datetime

def detailed_list(path='.'):
    for item in Path(path).iterdir():
        stats = item.stat()
        size = stats.st_size
        modified = datetime.fromtimestamp(stats.st_mtime)
        permissions = stat.filemode(stats.st_mode)
        print(f"{permissions} {size:>8} {modified} {item.name}")

detailed_list()

# Change directory
os.chdir('/path/to/directory')
# or
os.chdir(Path.home())  # Go to home directory
```

### **2. Creating Files and Directories**

#### **Linux Commands:**
```bash
mkdir project                    # Create directory
mkdir -p path/to/nested/dirs     # Create nested directories
touch file.txt                   # Create empty file
echo "content" > file.txt        # Create file with content
echo "more" >> file.txt          # Append to file
```

#### **Python Equivalents:**
```python
from pathlib import Path

# Create directory
Path('project').mkdir()

# Create nested directories
Path('path/to/nested/dirs').mkdir(parents=True, exist_ok=True)

# Create empty file
Path('file.txt').touch()

# Create file with content
Path('file.txt').write_text('content\n')

# Append to file
with open('file.txt', 'a') as f:
    f.write('more\n')

# Alternative using pathlib
content = Path('file.txt').read_text()
Path('file.txt').write_text(content + 'more\n')
```

### **3. Copying and Moving Files**

#### **Linux Commands:**
```bash
cp source.txt dest.txt           # Copy file
cp -r source-dir dest-dir        # Copy directory recursively
mv oldname.txt newname.txt       # Rename/move file
mv file.txt /new/location/       # Move to different directory
```

#### **Python Equivalents:**
```python
import shutil
from pathlib import Path

# Copy file
shutil.copy2('source.txt', 'dest.txt')
# or
shutil.copy('source.txt', 'dest.txt')

# Copy directory recursively
shutil.copytree('source-dir', 'dest-dir')

# Rename/move file
Path('oldname.txt').rename('newname.txt')
# or
shutil.move('oldname.txt', 'newname.txt')

# Move to different directory
shutil.move('file.txt', '/new/location/')
```

### **4. Removing Files and Directories**

#### **Linux Commands:**
```bash
rm file.txt                      # Remove file
rm -r directory/                 # Remove directory recursively
rm -f file.txt                   # Force remove
rmdir empty-directory/           # Remove empty directory
```

#### **Python Equivalents:**
```python
import os
import shutil
from pathlib import Path

# Remove file
os.remove('file.txt')
# or
Path('file.txt').unlink()

# Remove directory recursively
shutil.rmtree('directory/')

# Force remove (ignore errors)
try:
    Path('file.txt').unlink()
except FileNotFoundError:
    pass  # Ignore if file doesn't exist

# Remove empty directory
os.rmdir('empty-directory')
# or
Path('empty-directory').rmdir()
```

---

## 🔍 **Finding and Searching: Python vs Linux**

### **Finding Files**

#### **Linux Commands:**
```bash
find . -name "*.txt"             # Find all .txt files
find . -type f -name "script*"   # Find files starting with "script"
find . -size +1M                 # Find files larger than 1MB
find /home -user john            # Find files owned by john
```

#### **Python Equivalents:**
```python
from pathlib import Path
import fnmatch

# Find all .txt files
txt_files = list(Path('.').rglob('*.txt'))
print(txt_files)

# Find files starting with "script"
script_files = []
for file in Path('.').rglob('*'):
    if file.is_file() and file.name.startswith('script'):
        script_files.append(file)

# Find files larger than 1MB
large_files = []
for file in Path('.').rglob('*'):
    if file.is_file() and file.stat().st_size > 1024*1024:
        large_files.append(file)

# More Pythonic approach using generators
def find_files(pattern, path='.'):
    """Find files matching pattern"""
    return [f for f in Path(path).rglob('*') 
            if f.is_file() and fnmatch.fnmatch(f.name, pattern)]

txt_files = find_files('*.txt')
script_files = find_files('script*')
```

### **Searching File Contents**

#### **Linux Commands:**
```bash
grep "pattern" file.txt          # Search for pattern in file
grep -r "pattern" directory/     # Search recursively in directory
grep -i "pattern" file.txt       # Case-insensitive search
grep -n "pattern" file.txt       # Show line numbers
```

#### **Python Equivalents:**
```python
import re
from pathlib import Path

# Search for pattern in file
def grep_file(pattern, filename, ignore_case=False, show_line_numbers=False):
    """Python equivalent of grep"""
    flags = re.IGNORECASE if ignore_case else 0
    
    try:
        with open(filename, 'r') as f:
            for line_num, line in enumerate(f, 1):
                if re.search(pattern, line, flags):
                    if show_line_numbers:
                        print(f"{filename}:{line_num}:{line.strip()}")
                    else:
                        print(f"{filename}:{line.strip()}")
    except FileNotFoundError:
        print(f"File not found: {filename}")

# Search recursively in directory
def grep_recursive(pattern, directory='.', file_pattern='*'):
    """Recursively search for pattern in files"""
    for file_path in Path(directory).rglob(file_pattern):
        if file_path.is_file():
            try:
                with open(file_path, 'r', encoding='utf-8', errors='ignore') as f:
                    for line_num, line in enumerate(f, 1):
                        if pattern.lower() in line.lower():
                            print(f"{file_path}:{line_num}:{line.strip()}")
            except Exception:
                continue  # Skip files that can't be read

# Usage examples
grep_file("error", "log.txt", ignore_case=True, show_line_numbers=True)
grep_recursive("TODO", ".", "*.py")
```

---

## 🔒 **File Permissions: Python vs Linux**

### **Linux Commands:**
```bash
ls -l file.txt                   # Show permissions
chmod +x script.sh               # Make executable
chmod 755 script.sh              # Set specific permissions
chmod u+w,go-w file.txt          # Modify permissions
chown user:group file.txt        # Change ownership
```

### **Python Equivalents:**
```python
import os
import stat
from pathlib import Path

# Show permissions (like ls -l)
def show_permissions(filename):
    """Show file permissions like ls -l"""
    path = Path(filename)
    if path.exists():
        stats = path.stat()
        mode = stat.filemode(stats.st_mode)
        print(f"{mode} {path.name}")
    else:
        print(f"File not found: {filename}")

# Make executable (chmod +x)
def make_executable(filename):
    """Make file executable"""
    path = Path(filename)
    if path.exists():
        current_mode = path.stat().st_mode
        path.chmod(current_mode | stat.S_IEXEC)
        print(f"Made {filename} executable")

# Set specific permissions (chmod 755)
def set_permissions(filename, permissions):
    """Set specific permissions using octal notation"""
    path = Path(filename)
    if path.exists():
        path.chmod(permissions)
        print(f"Set permissions {oct(permissions)} on {filename}")

# Check if file is executable
def is_executable(filename):
    """Check if file is executable"""
    path = Path(filename)
    return path.exists() and os.access(path, os.X_OK)

# Usage examples
show_permissions("script.sh")
make_executable("deploy.py")
set_permissions("config.txt", 0o644)  # rw-r--r--
set_permissions("script.sh", 0o755)   # rwxr-xr-x

print(f"Is executable: {is_executable('script.sh')}")
```

---

## 💻 **System Information: Python vs Linux**

### **Linux Commands:**
```bash
ps aux                           # Show running processes
top                              # Monitor system resources
df -h                            # Show disk usage
free -h                          # Show memory usage
uname -a                         # Show system information
whoami                           # Show current user
pwd                              # Show current directory
date                             # Show current date/time
```

### **Python Equivalents:**
```python
import os
import platform
import psutil
from datetime import datetime
from pathlib import Path

# Show current user
print(f"Current user: {os.getenv('USER') or os.getenv('USERNAME')}")

# Show current directory
print(f"Current directory: {Path.cwd()}")

# Show current date/time
print(f"Current time: {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}")

# Show system information
print(f"System: {platform.system()}")
print(f"Node: {platform.node()}")
print(f"Release: {platform.release()}")
print(f"Architecture: {platform.architecture()}")

# Show running processes (requires psutil: pip install psutil)
def show_processes():
    """Show running processes like ps aux"""
    print(f"{'PID':<8} {'USER':<10} {'CPU%':<6} {'MEM%':<6} {'COMMAND'}")
    print("-" * 50)
    
    for proc in psutil.process_iter(['pid', 'username', 'cpu_percent', 'memory_percent', 'name']):
        try:
            info = proc.info
            print(f"{info['pid']:<8} {info['username']:<10} {info['cpu_percent']:<6.1f} {info['memory_percent']:<6.1f} {info['name']}")
        except (psutil.NoSuchProcess, psutil.AccessDenied):
            continue

# Show disk usage (like df -h)
def show_disk_usage():
    """Show disk usage like df -h"""
    print(f"{'Filesystem':<20} {'Size':<10} {'Used':<10} {'Avail':<10} {'Use%':<6} {'Mounted on'}")
    print("-" * 80)
    
    for partition in psutil.disk_partitions():
        try:
            usage = psutil.disk_usage(partition.mountpoint)
            size = usage.total // (1024**3)  # GB
            used = usage.used // (1024**3)   # GB
            free = usage.free // (1024**3)   # GB
            percent = (usage.used / usage.total) * 100
            
            print(f"{partition.device:<20} {size:<10}G {used:<10}G {free:<10}G {percent:<6.1f}% {partition.mountpoint}")
        except PermissionError:
            continue

# Show memory usage (like free -h)
def show_memory_usage():
    """Show memory usage like free -h"""
    memory = psutil.virtual_memory()
    swap = psutil.swap_memory()
    
    def bytes_to_gb(bytes_value):
        return bytes_value // (1024**3)
    
    print("Memory Usage:")
    print(f"  Total: {bytes_to_gb(memory.total)}G")
    print(f"  Used:  {bytes_to_gb(memory.used)}G ({memory.percent}%)")
    print(f"  Free:  {bytes_to_gb(memory.available)}G")
    
    print("Swap Usage:")
    print(f"  Total: {bytes_to_gb(swap.total)}G")
    print(f"  Used:  {bytes_to_gb(swap.used)}G ({swap.percent}%)")
    print(f"  Free:  {bytes_to_gb(swap.free)}G")

# Usage
show_processes()
show_disk_usage()
show_memory_usage()
```

---

## 🔧 **Running Commands: Python vs Linux**

### **Running Other Programs**

#### **Linux/Bash:**
```bash
# Run commands directly
ls -la
python script.py
docker ps
git status

# Capture output
result=$(ls -la)
echo "Output: $result"

# Run in background
python long_script.py &

# Chain commands
ls -la && echo "Success" || echo "Failed"
```

#### **Python Equivalents:**
```python
import subprocess
import os
from pathlib import Path

# Run commands and capture output
def run_command(command, shell=True):
    """Run a shell command and return output"""
    try:
        result = subprocess.run(
            command, 
            shell=shell, 
            capture_output=True, 
            text=True,
            check=True
        )
        return result.stdout.strip()
    except subprocess.CalledProcessError as e:
        print(f"Command failed: {e}")
        return None

# Examples
output = run_command("ls -la")
print("Directory contents:", output)

# Run Python script
run_command("python script.py")

# Run Docker commands
containers = run_command("docker ps --format '{{.Names}}'")
print("Running containers:", containers)

# Git operations
git_status = run_command("git status --porcelain")
if git_status:
    print("Uncommitted changes found")

# More control with subprocess
def advanced_run(command, cwd=None, env=None):
    """Advanced command execution with more control"""
    process = subprocess.Popen(
        command,
        shell=True,
        stdout=subprocess.PIPE,
        stderr=subprocess.PIPE,
        text=True,
        cwd=cwd,
        env=env
    )
    
    stdout, stderr = process.communicate()
    
    return {
        'returncode': process.returncode,
        'stdout': stdout,
        'stderr': stderr,
        'success': process.returncode == 0
    }

# Usage
result = advanced_run("ls /nonexistent", cwd="/tmp")
if not result['success']:
    print(f"Error: {result['stderr']}")
```

---

## 🚀 **Practical DevOps Examples**

### **1. Log File Analysis**

#### **Bash Approach:**
```bash
# Count error lines in log
grep -c "ERROR" /var/log/app.log

# Show last 100 lines and follow
tail -f -n 100 /var/log/app.log

# Find unique IP addresses
grep -o '[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}\.[0-9]\{1,3\}' access.log | sort | uniq
```

#### **Python Approach:**
```python
import re
from collections import Counter
from pathlib import Path

def analyze_log(log_file):
    """Comprehensive log analysis"""
    log_path = Path(log_file)
    
    if not log_path.exists():
        print(f"Log file not found: {log_file}")
        return
    
    error_count = 0
    ip_addresses = []
    
    with open(log_path, 'r') as f:
        for line in f:
            # Count errors
            if 'ERROR' in line:
                error_count += 1
            
            # Extract IP addresses
            ip_match = re.search(r'\b(?:[0-9]{1,3}\.){3}[0-9]{1,3}\b', line)
            if ip_match:
                ip_addresses.append(ip_match.group())
    
    print(f"Error count: {error_count}")
    print("Top 10 IP addresses:")
    for ip, count in Counter(ip_addresses).most_common(10):
        print(f"  {ip}: {count}")

def tail_log(log_file, lines=100, follow=False):
    """Python equivalent of tail -f"""
    log_path = Path(log_file)
    
    if follow:
        # Simple follow implementation
        import time
        with open(log_path, 'r') as f:
            # Go to end
            f.seek(0, 2)
            while True:
                line = f.readline()
                if line:
                    print(line.strip())
                else:
                    time.sleep(0.1)
    else:
        # Show last N lines
        with open(log_path, 'r') as f:
            all_lines = f.readlines()
            for line in all_lines[-lines:]:
                print(line.strip())

# Usage
analyze_log('/var/log/app.log')
tail_log('/var/log/app.log', lines=50)
```

### **2. System Health Check**

#### **Bash Approach:**
```bash
#!/bin/bash
# health-check.sh

echo "=== System Health Check ==="
echo "Date: $(date)"
echo "Uptime: $(uptime)"
echo "Disk Usage:"
df -h | grep -v tmpfs
echo "Memory Usage:"
free -h
echo "Top 5 Processes:"
ps aux --sort=-%cpu | head -6
```

#### **Python Approach:**
```python
#!/usr/bin/env python3
import psutil
from datetime import datetime, timedelta

def system_health_check():
    """Comprehensive system health check"""
    print("=== System Health Check ===")
    print(f"Date: {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}")
    
    # Uptime
    boot_time = datetime.fromtimestamp(psutil.boot_time())
    uptime = datetime.now() - boot_time
    print(f"Uptime: {uptime}")
    
    # Disk usage
    print("\nDisk Usage:")
    for partition in psutil.disk_partitions():
        try:
            usage = psutil.disk_usage(partition.mountpoint)
            percent = (usage.used / usage.total) * 100
            if percent > 80:  # Alert if > 80%
                status = "⚠️  HIGH"
            else:
                status = "✅ OK"
            
            print(f"  {partition.mountpoint}: {percent:.1f}% used {status}")
        except PermissionError:
            continue
    
    # Memory usage
    memory = psutil.virtual_memory()
    print(f"\nMemory Usage: {memory.percent}% used")
    if memory.percent > 90:
        print("  ⚠️  Memory usage is HIGH!")
    
    # CPU usage
    cpu_percent = psutil.cpu_percent(interval=1)
    print(f"CPU Usage: {cpu_percent}%")
    
    # Top processes
    print("\nTop 5 Processes by CPU:")
    processes = []
    for proc in psutil.process_iter(['pid', 'name', 'cpu_percent', 'memory_percent']):
        try:
            processes.append(proc.info)
        except (psutil.NoSuchProcess, psutil.AccessDenied):
            continue
    
    top_processes = sorted(processes, key=lambda x: x['cpu_percent'], reverse=True)[:5]
    for proc in top_processes:
        print(f"  PID {proc['pid']}: {proc['name']} - CPU: {proc['cpu_percent']}% MEM: {proc['memory_percent']:.1f}%")
    
    # Network connections
    connections = psutil.net_connections()
    listening = [c for c in connections if c.status == 'LISTEN']
    print(f"\nListening ports: {len(listening)}")

if __name__ == "__main__":
    system_health_check()
```

### **3. Backup Script**

#### **Bash Approach:**
```bash
#!/bin/bash
# backup.sh

SOURCE="/home/user/important"
DEST="/backups"
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_NAME="backup_$DATE.tar.gz"

echo "Starting backup..."
tar -czf "$DEST/$BACKUP_NAME" "$SOURCE"

if [ $? -eq 0 ]; then
    echo "Backup successful: $BACKUP_NAME"
    # Keep only last 5 backups
    cd "$DEST"
    ls -t backup_*.tar.gz | tail -n +6 | xargs rm -f
else
    echo "Backup failed!"
    exit 1
fi
```

#### **Python Approach:**
```python
#!/usr/bin/env python3
import shutil
import tarfile
from datetime import datetime
from pathlib import Path
import logging

# Setup logging
logging.basicConfig(level=logging.INFO, format='%(asctime)s - %(message)s')
logger = logging.getLogger(__name__)

def create_backup(source_dir, dest_dir, keep_count=5):
    """Create compressed backup and manage retention"""
    source_path = Path(source_dir)
    dest_path = Path(dest_dir)
    
    if not source_path.exists():
        logger.error(f"Source directory not found: {source_dir}")
        return False
    
    dest_path.mkdir(parents=True, exist_ok=True)
    
    # Create backup filename with timestamp
    timestamp = datetime.now().strftime('%Y%m%d_%H%M%S')
    backup_name = f"backup_{timestamp}.tar.gz"
    backup_path = dest_path / backup_name
    
    try:
        logger.info(f"Starting backup of {source_dir}")
        
        # Create compressed backup
        with tarfile.open(backup_path, 'w:gz') as tar:
            tar.add(source_path, arcname=source_path.name)
        
        logger.info(f"Backup successful: {backup_name}")
        
        # Cleanup old backups (keep only last N)
        cleanup_old_backups(dest_path, keep_count)
        
        return True
        
    except Exception as e:
        logger.error(f"Backup failed: {e}")
        # Remove incomplete backup
        if backup_path.exists():
            backup_path.unlink()
        return False

def cleanup_old_backups(backup_dir, keep_count):
    """Remove old backups, keeping only the most recent ones"""
    backup_files = sorted(
        backup_dir.glob('backup_*.tar.gz'),
        key=lambda x: x.stat().st_mtime,
        reverse=True
    )
    
    # Remove old backups
    for old_backup in backup_files[keep_count:]:
        logger.info(f"Removing old backup: {old_backup.name}")
        old_backup.unlink()

def get_backup_info(backup_dir):
    """Get information about existing backups"""
    backup_path = Path(backup_dir)
    backups = list(backup_path.glob('backup_*.tar.gz'))
    
    print(f"Found {len(backups)} backups:")
    for backup in sorted(backups, key=lambda x: x.stat().st_mtime, reverse=True):
        size = backup.stat().st_size // (1024*1024)  # MB
        mtime = datetime.fromtimestamp(backup.stat().st_mtime)
        print(f"  {backup.name}: {size}MB, {mtime}")

if __name__ == "__main__":
    # Configuration
    SOURCE = "/home/user/important"
    DEST = "/backups"
    
    # Run backup
    if create_backup(SOURCE, DEST):
        print("Backup completed successfully!")
        get_backup_info(DEST)
    else:
        print("Backup failed!")
        exit(1)
```

---

## 🤔 **When to Use Bash vs Python?**

### **Use Bash When:**
```bash
# Simple file operations
cp config.txt config.txt.backup
chmod +x deploy.sh

# System service management
systemctl restart nginx
systemctl status docker

# Quick data processing
cat access.log | grep "ERROR" | wc -l

# Environment setup
export PATH=$PATH:/new/bin
source ~/.bashrc

# Simple automation
for file in *.txt; do
    mv "$file" "backup_$file"
done
```

### **Use Python When:**
```python
# Complex logic and error handling
try:
    result = complex_calculation()
    if result > threshold:
        send_alert(result)
except Exception as e:
    log_error(e)
    handle_gracefully()

# Data processing and analysis
import pandas as pd
df = pd.read_csv('data.csv')
analysis = df.groupby('category').mean()

# API interactions
import requests
response = requests.post('https://api.example.com', json=data)
if response.status_code == 200:
    process_response(response.json())

# Cross-platform compatibility
import platform
if platform.system() == 'Windows':
    handle_windows()
else:
    handle_unix()

# Object-oriented design
class DeploymentManager:
    def __init__(self, config):
        self.config = config
    
    def deploy(self):
        # Complex deployment logic
        pass
```

---

## 📋 **Quick Reference: Command Equivalents**

| Task | Linux Command | Python Equivalent |
|------|---------------|-------------------|
| **List files** | `ls -la` | `list(Path('.').iterdir())` |
| **Change directory** | `cd /path` | `os.chdir('/path')` |
| **Create file** | `touch file.txt` | `Path('file.txt').touch()` |
| **Write to file** | `echo "text" > file` | `Path('file').write_text('text')` |
| **Copy file** | `cp src dest` | `shutil.copy2('src', 'dest')` |
| **Move file** | `mv old new` | `Path('old').rename('new')` |
| **Delete file** | `rm file` | `Path('file').unlink()` |
| **Make executable** | `chmod +x script` | `path.chmod(path.stat().st_mode \| stat.S_IEXEC)` |
| **Find files** | `find . -name "*.py"` | `list(Path('.').rglob('*.py'))` |
| **Search content** | `grep pattern file` | `re.search(pattern, content)` |
| **Run command** | `command args` | `subprocess.run(['command', 'args'])` |
| **Get file size** | `ls -lh file` | `Path('file').stat().st_size` |
| **Show processes** | `ps aux` | `psutil.process_iter()` |
| **System info** | `uname -a` | `platform.uname()` |

---

## 💡 **Pro Tips for DevOps**

### **Combining Both Approaches:**
```python
#!/usr/bin/env python3
"""
Hybrid approach: Use Python for logic, Bash for system operations
"""
import subprocess
from pathlib import Path

def deploy_application(environment):
    """Complex deployment with both Python logic and Bash commands"""
    
    # Python logic for validation
    config_file = Path(f"config/{environment}.yml")
    if not config_file.exists():
        raise ValueError(f"Config not found for {environment}")
    
    # Use Bash for system operations
    commands = [
        "docker build -t myapp:latest .",
        f"docker tag myapp:latest myapp:{environment}",
        "kubectl apply -f k8s/",
        f"kubectl set image deployment/myapp myapp=myapp:{environment}"
    ]
    
    for cmd in commands:
        print(f"Running: {cmd}")
        result = subprocess.run(cmd, shell=True, capture_output=True, text=True)
        
        if result.returncode != 0:
            print(f"Command failed: {result.stderr}")
            return False
        
        print(f"Success: {result.stdout}")
    
    return True

# Usage
if deploy_application("production"):
    print("Deployment successful!")
else:
    print("Deployment failed!")
```

### **Best Practices:**
1. **Start with Bash** for simple automation
2. **Move to Python** when logic becomes complex
3. **Use Python for error handling** and logging
4. **Use Bash for system operations** and file management
5. **Combine both** in larger automation projects

---

## ➡️ **Next Steps**

**You're ready to advance when you can:**
- [ ] Translate simple Linux commands to Python equivalents
- [ ] Understand when to use Bash vs Python for different tasks
- [ ] Create file operation scripts in both languages
- [ ] Run system commands from Python using subprocess
- [ ] Choose the appropriate tool for each DevOps scenario

**Continue to:**
1. **03-Package_Management** - Installing and managing software
2. **04-Basic_System_Operations** - System processes and services
3. **Advanced Bash Scripting** - More complex automation
4. **Python for DevOps** - Advanced Python automation techniques

---

📄 **File Path:** `/DevOps/02-Linux_System_Administration/02-Linux_Basics_For_Beginners/01-Essential_Commands/02-Python_Equivalents_For_Linux_Commands.md`