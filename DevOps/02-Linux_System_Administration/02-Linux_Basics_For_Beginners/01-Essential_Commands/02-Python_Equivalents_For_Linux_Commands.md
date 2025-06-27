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

**📖 What This Does**  
Navigation and listing operations help you understand where you are in the file system and what files are available. Python provides programmatic ways to achieve the same results as Linux commands, with the added benefit of being able to process and manipulate the results in your code.

**🔧 Configuration Notes**
- Python: `Path.cwd()` is preferred over `os.getcwd()` for modern Python
- Python: `pathlib.Path` is more object-oriented and cross-platform than `os` module
- Linux: `ls -la` shows detailed info; Python equivalent requires multiple function calls
- Python: `os.chdir()` changes directory for the entire script, use carefully
- Both: Directory operations are essential for file management and automation scripts
- Python: Better for processing file lists programmatically vs just displaying them

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

**📖 What This Does**  
File and directory creation operations set up the structure for your projects and data. Python provides more control and error handling compared to Linux commands, and can integrate creation operations into larger automation workflows.

**🔧 Configuration Notes**
- Linux: `mkdir -p` creates nested directories; Python equivalent is `parents=True, exist_ok=True`
- Python: `Path.touch()` is cleaner than `open(file, 'w').close()`
- Linux: `>` overwrites, `>>` appends; Python uses different file modes or methods
- Python: `write_text()` is simpler for small files, `open()` context manager for larger files
- Both: Essential for setting up project structures and creating configuration files
- Python: Better error handling and integration with complex logic

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

**📖 What This Does**  
Copying and moving operations manage file locations and create backups. Python's `shutil` module provides robust file operations with better error handling and cross-platform compatibility than shell commands.

**🔧 Configuration Notes**
- Python: `shutil.copy2()` preserves metadata, `shutil.copy()` just copies content
- Linux: `cp -r` for directories; Python uses `shutil.copytree()`
- Python: `Path.rename()` for simple renames, `shutil.move()` for complex moves
- Linux: `mv` can rename or move; Python separates these concepts
- Both: Essential for backup operations, deployment processes, and file organization
- Python: Better for conditional copying and integration with file processing logic

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

**📖 What This Does**  
File and directory removal operations clean up unnecessary files and manage storage space. Python provides safer deletion with exception handling, while Linux commands offer quick removal with various force options.

**🔧 Configuration Notes**
- Linux: `rm -f` forces deletion; Python uses try/except for safe deletion
- Python: `shutil.rmtree()` for directories, `unlink()` for files
- Linux: `rm -r` removes recursively; Python separates file and directory removal
- Python: Exception handling prevents crashes from missing files
- Both: Essential for cleanup scripts, temporary file management, and space optimization
- Python: Better for conditional deletion and logging of removal operations

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

**📖 What This Does**  
File finding operations locate files based on patterns, size, ownership, or other criteria. Python provides more flexible search capabilities with the ability to process results programmatically, while Linux `find` offers powerful command-line options.

**🔧 Configuration Notes**
- Linux: `find` with various options for complex searches; Python uses loops and conditions
- Python: `Path.rglob()` for recursive pattern matching, more readable than nested loops
- Linux: `-size +1M` for size criteria; Python uses `stat().st_size` comparisons
- Python: List comprehensions and generators provide powerful filtering capabilities
- Both: Essential for file management, backup scripts, and system maintenance
- Python: Better for complex logic combining multiple search criteria

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

**📖 What This Does**  
Content searching finds specific text patterns within files, essential for log analysis, code searching, and configuration management. Python's regex capabilities provide more powerful pattern matching than basic `grep`, with better integration into data processing workflows.

**🔧 Configuration Notes**
- Linux: `grep -r` for recursive search; Python uses nested loops with file operations
- Python: Regular expressions provide more powerful pattern matching than basic grep
- Linux: `grep -i` for case-insensitive; Python uses `re.IGNORECASE` flag
- Python: Better error handling for unreadable files and encoding issues
- Both: Essential for log analysis, code review, and troubleshooting
- Python: Can easily combine search results with data processing and reporting

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

**📖 What This Does**  
File permission operations control who can read, write, or execute files. Python provides programmatic permission management that can be integrated into deployment scripts, while Linux commands offer quick permission changes for immediate needs.

**🔧 Configuration Notes**
- Linux: `chmod +x` adds execute permission; Python uses bitwise OR with `stat.S_IEXEC`
- Python: Octal notation (0o755) for permission bits, more explicit than Linux shortcuts
- Linux: `ls -l` shows permissions as text; Python `stat.filemode()` converts to readable format
- Python: `os.access()` checks actual accessibility, not just permission bits
- Both: Essential for deployment scripts, security management, and automation
- Python: Better for conditional permission changes based on file type or location

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

**📖 What This Does**  
System information operations monitor system resources, processes, and hardware status. Python with `psutil` provides cross-platform system monitoring capabilities that can be integrated into monitoring scripts, while Linux commands offer quick system status checks.

**🔧 Configuration Notes**
- Python: Requires `psutil` library (`pip install psutil`) for system monitoring
- Linux: Commands like `top`, `ps`, `df` are built-in; Python equivalent needs libraries
- Python: Returns structured data that can be processed and analyzed programmatically
- Linux: Human-readable output; Python requires formatting for display
- Both: Essential for system monitoring, performance analysis, and capacity planning
- Python: Better for automated monitoring, alerting, and data collection over time
- Python: Cross-platform compatibility vs Linux-specific commands

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

**📖 What This Does**  
Command execution bridges Python scripts with system operations, allowing Python programs to run shell commands, system utilities, and other programs. This is essential for DevOps automation where Python logic needs to control system-level operations.

**🔧 Configuration Notes**
- Python: `subprocess.run()` is the modern, secure way to execute commands
- Python: `shell=True` allows shell features but can be a security risk with user input
- Python: `capture_output=True` captures stdout/stderr for processing
- Python: `check=True` raises exception on command failure
- Linux: Direct command execution is faster but less error handling
- Python: Better for conditional execution, error handling, and result processing
- Both: Essential for deployment automation, system administration, and DevOps workflows

### **🔍 Understanding Return Codes (Exit Codes)**

Many people get confused by `if result.returncode != 0:` thinking "if results are nothing, then run this?" But that's not what return codes mean!

#### **What Return Codes Actually Are:**
```python
import subprocess

# SUCCESS CASE - returns 0
result = subprocess.run("echo 'Hello World'", shell=True, capture_output=True, text=True)
print(f"Return code: {result.returncode}")  # 0 (success)
print(f"Output: {result.stdout}")           # "Hello World" (has output!)
print(f"Error: {result.stderr}")            # "" (empty, no error)

# FAILURE CASE - returns non-zero  
result = subprocess.run("ls /this-folder-does-not-exist", shell=True, capture_output=True, text=True)
print(f"Return code: {result.returncode}")  # 2 (failure)
print(f"Output: {result.stdout}")           # "" (empty, no normal output)
print(f"Error: {result.stderr}")            # "ls: cannot access '/this-folder-does-not-exist': No such file or directory"

# ANOTHER SUCCESS CASE - returns 0 even with no visible output
result = subprocess.run("mkdir test-folder", shell=True, capture_output=True, text=True)
print(f"Return code: {result.returncode}")  # 0 (success)
print(f"Output: {result.stdout}")           # "" (empty, but still successful!)
print(f"Error: {result.stderr}")            # "" (empty, no error)
```

**📖 What This Does**  
The `returncode` (also called "exit code") is a number that every program returns when it finishes running. It's not about whether there are results - it's about whether the program succeeded or failed.

**🔧 The Universal Convention:**
- **`0` = Success** ("Everything went fine")
- **Non-zero = Failure** ("Something went wrong")

#### **Common Return Codes You'll See:**
```bash
# You can check return codes in terminal:
ls /existing-folder
echo $?  # Shows: 0 (success)

ls /nonexistent-folder  
echo $?  # Shows: 2 (failure - file not found)

grep "pattern" nonexistent-file.txt
echo $?  # Shows: 2 (failure - file not found)

grep "nonexistent-pattern" existing-file.txt  
echo $?  # Shows: 1 (failure - pattern not found)

systemctl start nginx
echo $?  # Shows: 0 (success) or non-zero (failure)
```

#### **Real DevOps Example - Deployment Script:**
```python
def deploy_application():
    """Deploy application with proper error checking"""
    
    # Step 1: Build Docker image
    result = subprocess.run("docker build -t myapp .", shell=True, capture_output=True, text=True)
    if result.returncode != 0:  # If build FAILED
        print(f"❌ Docker build failed: {result.stderr}")
        return False  # Stop deployment
    print("✅ Docker build successful!")
    
    # Step 2: Start the container  
    result = subprocess.run("docker run -d --name myapp-container myapp", shell=True, capture_output=True, text=True)
    if result.returncode != 0:  # If container start FAILED
        print(f"❌ Container start failed: {result.stderr}")
        return False  # Stop deployment
    print("✅ Container started successfully!")
    
    # Step 3: Check if container is running
    result = subprocess.run("docker ps | grep myapp-container", shell=True, capture_output=True, text=True)
    if result.returncode != 0:  # If container is NOT running
        print("❌ Container is not running!")
        return False
    print(f"✅ Container is running: {result.stdout}")
    
    return True  # All steps succeeded

# Usage
if deploy_application():
    print("🎉 Deployment successful!")
else:
    print("💥 Deployment failed!")
```

#### **The Logic Breakdown:**
```python
# This is the logic:
if result.returncode != 0:
    # This means: "If the command FAILED"
    print("Something went wrong!")
    handle_error()
else:
    # This means: "If the command SUCCEEDED"  
    print("Command worked fine!")
    continue_with_next_steps()
```

#### **Alternative Ways to Check Success/Failure:**
```python
# Method 1: Check for failure first
if result.returncode != 0:
    handle_error()
    return False

# Method 2: Check for success first
if result.returncode == 0:
    print("Success!")
    continue_processing()
else:
    handle_error()

# Method 3: Use check=True to automatically raise exception on failure
try:
    result = subprocess.run("risky-command", shell=True, capture_output=True, text=True, check=True)
    print("Command succeeded!")
except subprocess.CalledProcessError as e:
    print(f"Command failed with code {e.returncode}: {e.stderr}")

# Method 4: Boolean check (Pythonic)
success = result.returncode == 0
if success:
    continue_processing()
```

### **🛠️ Advanced Subprocess Techniques**

#### **1. Real-time Output (Like watching logs):**
```python
import subprocess
import sys

def run_with_live_output(command):
    """Run command and show output in real-time"""
    process = subprocess.Popen(
        command,
        shell=True,
        stdout=subprocess.PIPE,
        stderr=subprocess.STDOUT,
        universal_newlines=True,
        bufsize=1
    )
    
    # Print output line by line as it comes
    for line in process.stdout:
        print(line.strip())
        sys.stdout.flush()  # Force immediate display
    
    process.wait()  # Wait for completion
    return process.returncode

# Usage - perfect for watching deployments or long-running commands
success = run_with_live_output("docker build -t myapp . --progress=plain")
if success == 0:
    print("Build completed successfully!")
```

#### **2. Timeout Control:**
```python
import subprocess
from subprocess import TimeoutExpired

def run_with_timeout(command, timeout_seconds=30):
    """Run command with timeout"""
    try:
        result = subprocess.run(
            command,
            shell=True,
            capture_output=True,
            text=True,
            timeout=timeout_seconds
        )
        return result
    except TimeoutExpired:
        print(f"Command timed out after {timeout_seconds} seconds")
        return None

# Usage - prevent hanging on unresponsive commands
result = run_with_timeout("ping -c 5 google.com", timeout_seconds=10)
if result and result.returncode == 0:
    print("Ping successful!")
```

#### **3. Environment Variable Control:**
```python
import subprocess
import os

def run_with_custom_env(command, extra_env=None):
    """Run command with custom environment variables"""
    # Start with current environment
    env = os.environ.copy()
    
    # Add custom variables
    if extra_env:
        env.update(extra_env)
    
    result = subprocess.run(
        command,
        shell=True,
        capture_output=True,
        text=True,
        env=env
    )
    
    return result

# Usage - perfect for deployment scripts
custom_vars = {
    "ENVIRONMENT": "production",
    "API_KEY": "secret-key-here",
    "DATABASE_URL": "postgres://user:pass@db:5432/app"
}

result = run_with_custom_env("python deploy.py", extra_env=custom_vars)
```

#### **4. Working Directory Control:**
```python
def deploy_to_directory(app_path, commands):
    """Run deployment commands in specific directory"""
    original_dir = os.getcwd()
    
    try:
        os.chdir(app_path)
        
        for command in commands:
            print(f"Running in {app_path}: {command}")
            result = subprocess.run(command, shell=True, capture_output=True, text=True)
            
            if result.returncode != 0:
                print(f"❌ Command failed: {result.stderr}")
                return False
            print(f"✅ Success: {command}")
        
        return True
        
    finally:
        os.chdir(original_dir)  # Always return to original directory

# Usage
commands = [
    "git pull origin main",
    "pip install -r requirements.txt",
    "python manage.py migrate",
    "systemctl restart myapp"
]

if deploy_to_directory("/opt/myapp", commands):
    print("🎉 Deployment successful!")
```

#### **5. Input/Output Redirection:**
```python
def process_large_file(input_file, output_file):
    """Process large files using Unix pipes"""
    # Equivalent to: cat input.txt | grep "ERROR" | sort | uniq > output.txt
    
    # Step 1: grep for errors
    grep_process = subprocess.Popen(
        ["grep", "ERROR", input_file],
        stdout=subprocess.PIPE,
        text=True
    )
    
    # Step 2: sort the results  
    sort_process = subprocess.Popen(
        ["sort"],
        stdin=grep_process.stdout,
        stdout=subprocess.PIPE,
        text=True
    )
    
    # Step 3: remove duplicates
    uniq_process = subprocess.Popen(
        ["uniq"],
        stdin=sort_process.stdout,
        stdout=open(output_file, 'w'),
        text=True
    )
    
    # Close intermediate pipes
    grep_process.stdout.close()
    sort_process.stdout.close()
    
    # Wait for completion
    uniq_process.wait()
    
    return uniq_process.returncode == 0

# Usage
if process_large_file("app.log", "unique_errors.txt"):
    print("Log processing completed!")
```

### **🔧 DevOps Command Patterns**

#### **Common DevOps Commands and Their Patterns:**
```python
# Git operations
def git_operations():
    """Common git operations with error checking"""
    operations = [
        ("git status --porcelain", "Check for uncommitted changes"),
        ("git pull origin main", "Pull latest changes"),
        ("git add .", "Stage changes"),
        ("git commit -m 'Automated deployment'", "Commit changes"),
        ("git push origin main", "Push to remote")
    ]
    
    for command, description in operations:
        print(f"🔄 {description}")
        result = subprocess.run(command, shell=True, capture_output=True, text=True)
        
        if result.returncode != 0:
            print(f"❌ {description} failed: {result.stderr}")
            return False
        print(f"✅ {description} completed")
    
    return True

# Docker operations
def docker_operations(image_name, container_name):
    """Docker build and deploy operations"""
    operations = [
        (f"docker build -t {image_name} .", "Build Docker image"),
        (f"docker stop {container_name} || true", "Stop existing container"),
        (f"docker rm {container_name} || true", "Remove existing container"),
        (f"docker run -d --name {container_name} -p 80:8000 {image_name}", "Start new container"),
        (f"docker ps | grep {container_name}", "Verify container is running")
    ]
    
    for command, description in operations:
        print(f"🐳 {description}")
        result = subprocess.run(command, shell=True, capture_output=True, text=True)
        
        # Note: Some commands are allowed to "fail" (like stopping non-existent containers)
        if result.returncode != 0 and "|| true" not in command:
            print(f"❌ {description} failed: {result.stderr}")
            return False
        print(f"✅ {description} completed")
    
    return True

# System service operations
def service_operations(service_name):
    """System service management"""
    # Check service status
    result = subprocess.run(f"systemctl is-active {service_name}", shell=True, capture_output=True, text=True)
    
    if result.returncode == 0:
        print(f"✅ {service_name} is already running")
        return True
    else:
        print(f"🔄 Starting {service_name}")
        result = subprocess.run(f"sudo systemctl start {service_name}", shell=True, capture_output=True, text=True)
        
        if result.returncode != 0:
            print(f"❌ Failed to start {service_name}: {result.stderr}")
            return False
        
        print(f"✅ {service_name} started successfully")
        return True
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