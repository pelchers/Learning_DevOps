# Basic Automation Scripts for DevOps

## 📖 What This File Does
This guide covers fundamental Python automation scripts for DevOps tasks including system monitoring, file management, and remote operations.

## 🎯 Learning Objectives
- Create Python scripts for system administration
- Automate file operations and backups
- Build remote server management scripts
- Implement proper logging and error handling

## 📋 Prerequisites
- Python installed (see `../Python_Basics/01-Python_Installation_And_Setup.md`)
- Basic Python knowledge
- Command-line experience

---

## System Monitoring Script

### System Information Collector
```python
#!/usr/bin/env python3
import psutil
import platform
import json
from datetime import datetime

def get_system_info():
    """Collect system information"""
    return {
        'timestamp': datetime.now().isoformat(),
        'hostname': platform.node(),
        'os': platform.system(),
        'cpu_count': psutil.cpu_count(),
        'cpu_percent': psutil.cpu_percent(interval=1),
        'memory': {
            'total': psutil.virtual_memory().total,
            'available': psutil.virtual_memory().available,
            'percent': psutil.virtual_memory().percent
        },
        'disk_usage': {
            'total': psutil.disk_usage('/').total,
            'free': psutil.disk_usage('/').free,
            'percent': psutil.disk_usage('/').percent
        }
    }

def save_system_info():
    """Save system info to file"""
    info = get_system_info()
    with open('system_info.json', 'w') as f:
        json.dump(info, f, indent=2)
    print("System information saved to system_info.json")

if __name__ == "__main__":
    save_system_info()
```

---

## File Backup Script

### Simple Backup Manager
```python
#!/usr/bin/env python3
import shutil
import os
from datetime import datetime
from pathlib import Path

def create_backup(source_dir, backup_dir):
    """Create backup of directory"""
    timestamp = datetime.now().strftime('%Y%m%d_%H%M%S')
    backup_name = f"backup_{timestamp}"
    backup_path = Path(backup_dir) / backup_name
    
    try:
        shutil.copytree(source_dir, backup_path)
        print(f"Backup created: {backup_path}")
        return backup_path
    except Exception as e:
        print(f"Backup failed: {e}")
        return None

def cleanup_old_backups(backup_dir, keep_count=5):
    """Keep only the latest backups"""
    backup_path = Path(backup_dir)
    if not backup_path.exists():
        return
    
    # Get backup directories sorted by creation time
    backups = sorted(
        [d for d in backup_path.iterdir() if d.is_dir() and d.name.startswith('backup_')],
        key=lambda x: x.stat().st_ctime,
        reverse=True
    )
    
    # Remove old backups
    for old_backup in backups[keep_count:]:
        shutil.rmtree(old_backup)
        print(f"Removed old backup: {old_backup.name}")

if __name__ == "__main__":
    source = "/path/to/source"
    destination = "/path/to/backups"
    
    create_backup(source, destination)
    cleanup_old_backups(destination)
```

---

## Configuration Management

### YAML Configuration Processor
```python
#!/usr/bin/env python3
import yaml
from jinja2 import Template

def load_config(config_file):
    """Load YAML configuration"""
    with open(config_file, 'r') as f:
        return yaml.safe_load(f)

def process_template(template_file, variables, output_file):
    """Process Jinja2 template with variables"""
    with open(template_file, 'r') as f:
        template = Template(f.read())
    
    rendered = template.render(**variables)
    
    with open(output_file, 'w') as f:
        f.write(rendered)
    
    print(f"Configuration generated: {output_file}")

# Example usage
if __name__ == "__main__":
    config = load_config('config.yml')
    process_template('nginx.conf.j2', config, 'nginx.conf')
```

---

## Remote Server Management

### SSH Command Executor
```python
#!/usr/bin/env python3
import paramiko
import sys

class SSHClient:
    def __init__(self, hostname, username, key_file=None):
        self.hostname = hostname
        self.username = username
        self.key_file = key_file
        self.client = None
    
    def connect(self):
        """Establish SSH connection"""
        try:
            self.client = paramiko.SSHClient()
            self.client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
            
            if self.key_file:
                self.client.connect(
                    hostname=self.hostname,
                    username=self.username,
                    key_filename=self.key_file
                )
            else:
                # For demo - in production, use key-based auth
                password = input(f"Password for {self.username}@{self.hostname}: ")
                self.client.connect(
                    hostname=self.hostname,
                    username=self.username,
                    password=password
                )
            
            print(f"Connected to {self.hostname}")
            return True
            
        except Exception as e:
            print(f"Connection failed: {e}")
            return False
    
    def execute_command(self, command):
        """Execute command on remote server"""
        if not self.client:
            print("Not connected")
            return None
        
        try:
            stdin, stdout, stderr = self.client.exec_command(command)
            output = stdout.read().decode('utf-8')
            error = stderr.read().decode('utf-8')
            exit_code = stdout.channel.recv_exit_status()
            
            return {
                'output': output,
                'error': error,
                'exit_code': exit_code
            }
            
        except Exception as e:
            print(f"Command execution failed: {e}")
            return None
    
    def close(self):
        """Close SSH connection"""
        if self.client:
            self.client.close()
            print("Connection closed")

# Example usage
if __name__ == "__main__":
    ssh = SSHClient('192.168.1.100', 'admin', '~/.ssh/id_rsa')
    
    if ssh.connect():
        result = ssh.execute_command('uptime')
        if result:
            print(f"Output: {result['output']}")
            print(f"Exit code: {result['exit_code']}")
        ssh.close()
```

---

## Log Analysis Script

### Basic Log Analyzer
```python
#!/usr/bin/env python3
import re
from collections import Counter
import argparse

def analyze_log(log_file):
    """Analyze log file for patterns"""
    ip_pattern = r'\b(?:\d{1,3}\.){3}\d{1,3}\b'
    error_pattern = r'ERROR|CRITICAL|FATAL'
    
    ip_addresses = []
    error_count = 0
    total_lines = 0
    
    try:
        with open(log_file, 'r') as f:
            for line in f:
                total_lines += 1
                
                # Extract IP addresses
                ips = re.findall(ip_pattern, line)
                ip_addresses.extend(ips)
                
                # Count errors
                if re.search(error_pattern, line, re.IGNORECASE):
                    error_count += 1
        
        # Generate report
        print(f"Log Analysis Report for {log_file}")
        print(f"{'='*50}")
        print(f"Total lines processed: {total_lines}")
        print(f"Total errors found: {error_count}")
        print(f"Unique IP addresses: {len(set(ip_addresses))}")
        
        if ip_addresses:
            print(f"\nTop 10 IP addresses:")
            for ip, count in Counter(ip_addresses).most_common(10):
                print(f"  {ip}: {count}")
                
    except FileNotFoundError:
        print(f"Error: File '{log_file}' not found")
    except Exception as e:
        print(f"Error analyzing log: {e}")

if __name__ == "__main__":
    parser = argparse.ArgumentParser(description='Analyze log files')
    parser.add_argument('logfile', help='Path to log file')
    args = parser.parse_args()
    
    analyze_log(args.logfile)
```

---

## Automation Workflow

### Simple Workflow Manager
```python
#!/usr/bin/env python3
import subprocess
import logging
from datetime import datetime

class WorkflowManager:
    def __init__(self):
        self.setup_logging()
        self.tasks = []
    
    def setup_logging(self):
        """Setup logging"""
        logging.basicConfig(
            level=logging.INFO,
            format='%(asctime)s - %(levelname)s - %(message)s',
            handlers=[
                logging.FileHandler('workflow.log'),
                logging.StreamHandler()
            ]
        )
        self.logger = logging.getLogger(__name__)
    
    def add_task(self, name, command):
        """Add task to workflow"""
        self.tasks.append({'name': name, 'command': command})
    
    def run_workflow(self):
        """Execute all tasks in workflow"""
        self.logger.info("Starting workflow execution")
        
        for task in self.tasks:
            self.logger.info(f"Executing task: {task['name']}")
            
            try:
                result = subprocess.run(
                    task['command'],
                    shell=True,
                    capture_output=True,
                    text=True,
                    timeout=300
                )
                
                if result.returncode == 0:
                    self.logger.info(f"Task '{task['name']}' completed successfully")
                else:
                    self.logger.error(f"Task '{task['name']}' failed with exit code {result.returncode}")
                    self.logger.error(f"Error output: {result.stderr}")
                    
            except subprocess.TimeoutExpired:
                self.logger.error(f"Task '{task['name']}' timed out")
            except Exception as e:
                self.logger.error(f"Task '{task['name']}' failed with error: {e}")
        
        self.logger.info("Workflow execution completed")

# Example usage
if __name__ == "__main__":
    workflow = WorkflowManager()
    
    # Add tasks
    workflow.add_task("System Info", "python3 system_info.py")
    workflow.add_task("Disk Check", "df -h")
    workflow.add_task("Process List", "ps aux | head -20")
    
    # Run workflow
    workflow.run_workflow()
```

---

## Usage Examples

### Running the Scripts
```bash
# Make scripts executable
chmod +x *.py

# Run system monitoring
python3 system_monitor.py

# Create backup
python3 backup_manager.py

# Analyze logs
python3 log_analyzer.py /var/log/syslog

# Execute remote commands
python3 ssh_client.py

# Run workflow
python3 workflow_manager.py
```

### Scheduling with Cron
```bash
# Add to crontab for automated execution
# Run system check every hour
0 * * * * /path/to/system_monitor.py

# Daily backup at 2 AM
0 2 * * * /path/to/backup_manager.py

# Weekly log analysis
0 0 * * 0 /path/to/log_analyzer.py /var/log/syslog
```

---

## Next Steps

After mastering basic automation scripts:

1. **Learn advanced techniques** → See `02-Advanced_Automation.md`
2. **Explore DevOps libraries** → See `../DevOps_Specific_Libraries/`
3. **Practice with APIs** → See cloud automation modules
4. **Build CI/CD scripts** → See pipeline modules

---

## 🔧 Configuration Notes

- **Error Handling**: Always implement proper error handling
- **Logging**: Use structured logging for better debugging
- **Security**: Never hardcode credentials in scripts
- **Testing**: Test scripts thoroughly before production use

## 📚 Additional Resources

### Essential Reading
- ["The Lean Startup" by Eric Ries](https://www.amazon.com/Lean-Startup-Entrepreneurs-Continuous-Innovation/dp/0307887898) - Building minimal viable automation scripts
- ["Team of Teams" by General Stanley McChrystal](https://www.amazon.com/Team-Teams-Rules-Engagement-Complex/dp/1591847486) - Coordinating automation across teams
- ["The Five Dysfunctions of a Team" by Patrick Lencioni](https://www.amazon.com/Five-Dysfunctions-Team-Leadership-Fable/dp/0787960756) - Building reliable automation workflows
- ["Fearless Change" by Linda Rising and Mary Lynn Manns](https://www.amazon.com/Fearless-Change-Patterns-Introducing-Ideas/dp/0201755920) - Introducing automation practices

### Videos
- [Python Automation Tutorial (freeCodeCamp)](https://www.youtube.com/watch?v=s8XjEuplx_U) - Complete automation course
- [System Administration with Python (Real Python)](https://www.youtube.com/watch?v=bD05uGo_sVI) - Sysadmin automation
- [Python DevOps Scripts (TechWorld with Nana)](https://www.youtube.com/watch?v=9LFbltREZU0) - Practical DevOps automation
- [SSH Automation with Python (NetworkChuck)](https://www.youtube.com/watch?v=2Qdjb_nEBBs) - Remote management automation

### Guides
- [Python subprocess documentation](https://docs.python.org/3/library/subprocess.html)
- [Paramiko SSH library](https://www.paramiko.org/)
- [Jinja2 templating](https://jinja.palletsprojects.com/)
- [PyYAML documentation](https://pyyaml.org/)
- [Python Automation Cookbook](https://www.packtpub.com/product/python-automation-cookbook/9781789133806)
- [Automate the Boring Stuff with Python](https://automatetheboringstuff.com/)

### Articles
- [Python System Administration](https://realpython.com/python-sysadmin/)
- [Building DevOps Tools with Python](https://www.digitalocean.com/community/tutorials/how-to-build-devops-tools-with-python)
- [Python Logging Best Practices](https://realpython.com/python-logging/)
- [Error Handling in Python Scripts](https://realpython.com/python-exceptions-handling/)

### Cultural Assessment Tools
- [Automation Maturity Assessment](https://www.devops-culture.com/assessment/)
- [Script Quality Evaluation](https://www.python.org/dev/peps/pep-0008/)
- [Infrastructure as Code Assessment](https://www.terraform.io/docs/cloud/sentinel/)

### Communities and Events
- [Python DevOps Community](https://www.reddit.com/r/devops/)
- [Automation Scripting Forums](https://stackoverflow.com/questions/tagged/python+automation)
- [DevOps Automation Meetups](https://www.meetup.com/topics/devops-automation/)
- [Infrastructure as Code Events](https://www.hashicorp.com/events/) 