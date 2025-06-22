# Python Fundamentals for DevOps

## 📖 What This File Does
This guide covers essential Python concepts specifically tailored for DevOps environments, focusing on cross-platform considerations, environment management, and automation fundamentals that differ from general Python programming.

## 🎯 Learning Objectives
- Understand Python's role in DevOps vs general programming
- Master cross-platform Python development (Windows, Linux, macOS)
- Learn environment-specific Python patterns
- Implement DevOps-specific Python practices
- Handle shell and environment differences in Python code

## 📋 Prerequisites
- Completed `Python_DevOps_QA.md` (conceptual understanding)
- Python installation completed (`01-Python_Installation_And_Setup.md`)
- Basic understanding of command-line interfaces

---

## 🌍 **Cross-Platform Python for DevOps**

### **Understanding Environment Differences in Code**

DevOps Python scripts often need to run across different environments. Understanding these differences is crucial:

#### **Path Handling Across Platforms**
```python
import os
from pathlib import Path

# ❌ Platform-specific path handling (brittle)
config_path_windows = "C:\\Users\\admin\\config\\app.yml"
config_path_linux = "/home/admin/config/app.yml"

# ✅ Cross-platform path handling (robust)
def get_config_path():
    """Get configuration path regardless of platform"""
    home_dir = Path.home()
    return home_dir / "config" / "app.yml"

# ✅ Using os.path for compatibility
config_dir = os.path.join(os.path.expanduser("~"), "config", "app.yml")

# ✅ Best practice: pathlib for modern Python
config_path = Path.home() / "config" / "app.yml"
```

#### **Environment Variable Handling**
```python
import os
import platform

def get_system_info():
    """Get system information cross-platform"""
    system = platform.system()
    
    if system == "Windows":
        user = os.getenv('USERNAME', 'unknown')
        home = os.getenv('USERPROFILE', 'C:\\')
        shell = os.getenv('COMSPEC', 'cmd.exe')
    else:  # Linux/macOS
        user = os.getenv('USER', 'unknown')
        home = os.getenv('HOME', '/tmp')
        shell = os.getenv('SHELL', '/bin/bash')
    
    return {
        'system': system,
        'user': user,
        'home': home,
        'shell': shell
    }

# Example output on different systems:
# Windows: {'system': 'Windows', 'user': 'admin', 'home': 'C:\\Users\\admin', 'shell': 'C:\\Windows\\System32\\cmd.exe'}
# Linux:   {'system': 'Linux', 'user': 'admin', 'home': '/home/admin', 'shell': '/bin/bash'}
```

---

## 🔧 **Shell Integration and Command Execution**

### **Running Shell Commands from Python**

DevOps automation frequently requires executing shell commands. Here's how to handle different shells:

#### **Basic Command Execution**
```python
import subprocess
import sys
import platform

def run_command(command, shell=None):
    """
    Run shell command with proper cross-platform handling
    
    Args:
        command (str or list): Command to execute
        shell (str, optional): Specific shell to use
    
    Returns:
        dict: Command result with output, error, and return code
    """
    
    # Determine shell and command format based on platform
    if platform.system() == "Windows":
        if shell is None:
            shell = True  # Use cmd.exe by default
        if isinstance(command, str):
            # Windows command as string
            cmd = command
        else:
            # Windows command as list
            cmd = command
    else:  # Linux/macOS
        if shell is None:
            shell = False  # Use direct execution by default
        if isinstance(command, str) and shell:
            # Linux shell command as string
            cmd = command
        elif isinstance(command, list):
            # Linux command as list (preferred)
            cmd = command
        else:
            # Convert string to list for direct execution
            cmd = command.split()
    
    try:
        result = subprocess.run(
            cmd,
            shell=shell,
            capture_output=True,
            text=True,
            timeout=30
        )
        
        return {
            'success': result.returncode == 0,
            'return_code': result.returncode,
            'output': result.stdout.strip(),
            'error': result.stderr.strip(),
            'command': cmd
        }
        
    except subprocess.TimeoutExpired:
        return {
            'success': False,
            'return_code': -1,
            'output': '',
            'error': 'Command timed out after 30 seconds',
            'command': cmd
        }
    except Exception as e:
        return {
            'success': False,
            'return_code': -1,
            'output': '',
            'error': str(e),
            'command': cmd
        }

# Examples of cross-platform command execution
def get_system_processes():
    """Get running processes on any platform"""
    system = platform.system()
    
    if system == "Windows":
        result = run_command("tasklist /fo csv", shell=True)
    else:  # Linux/macOS
        result = run_command(["ps", "aux"])
    
    if result['success']:
        return result['output']
    else:
        print(f"Error getting processes: {result['error']}")
        return None
```

---

## 🚀 **Next Steps**

After mastering these fundamentals:

1. **Practice automation scripts** → See `../Automation_Scripting/01-Basic_Automation_Scripts.md`
2. **Learn DevOps libraries** → See `../DevOps_Specific_Libraries/01-Cloud_SDKs_AWS_Azure_GCP.md`
3. **Build real projects** → See module project suggestions

---

**💡 Remember**: The key to successful DevOps Python development is writing code that works reliably across different environments and gracefully handles the complexities of infrastructure management! 