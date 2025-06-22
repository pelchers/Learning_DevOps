# Basic Automation Scripts for DevOps

## 📖 What This File Does
This guide covers fundamental Python automation scripts for DevOps tasks. It provides practical examples for system administration, deployment automation, and infrastructure management.

## 🎯 Learning Objectives
- Create Python scripts for system administration tasks
- Automate file operations and directory management
- Build scripts for remote server management
- Implement logging and error handling in automation scripts
- Practice with configuration management automation

## 📋 Prerequisites
- Python installed and configured (see `../Python_Basics/01-Python_Installation_And_Setup.md`)
- Basic Python programming knowledge
- Understanding of system administration concepts
- Command-line experience

---

## System Administration Scripts

### 1. System Information Collector
```python
#!/usr/bin/env python3
"""
System Information Collector
Gathers system metrics and information for monitoring
"""

import psutil
import platform
import json
from datetime import datetime

def get_system_info():
    """Collect comprehensive system information"""
    return {
        'timestamp': datetime.now().isoformat(),
        'hostname': platform.node(),
        'os': {
            'system': platform.system(),
            'release': platform.release(),
            'version': platform.version(),
            'architecture': platform.architecture()[0]
        },
        'cpu': {
            'physical_cores': psutil.cpu_count(logical=False),
            'logical_cores': psutil.cpu_count(logical=True),
            'usage_percent': psutil.cpu_percent(interval=1),
            'frequency': psutil.cpu_freq()._asdict() if psutil.cpu_freq() else None
        },
        'memory': {
            'total': psutil.virtual_memory().total,
            'available': psutil.virtual_memory().available,
            'used': psutil.virtual_memory().used,
            'percentage': psutil.virtual_memory().percent
        },
        'disk': {
            partition.device: {
                'mountpoint': partition.mountpoint,
                'file_system': partition.fstype,
                'total': psutil.disk_usage(partition.mountpoint).total,
                'used': psutil.disk_usage(partition.mountpoint).used,
                'free': psutil.disk_usage(partition.mountpoint).free,
                'percentage': (psutil.disk_usage(partition.mountpoint).used / 
                             psutil.disk_usage(partition.mountpoint).total) * 100
            }
            for partition in psutil.disk_partitions()
        },
        'network': {
            interface: {
                'bytes_sent': stats.bytes_sent,
                'bytes_recv': stats.bytes_recv,
                'packets_sent': stats.packets_sent,
                'packets_recv': stats.packets_recv
            }
            for interface, stats in psutil.net_io_counters(pernic=True).items()
        }
    }

def save_system_info(filename='system_info.json'):
    """Save system information to file"""
    try:
        info = get_system_info()
        with open(filename, 'w') as f:
            json.dump(info, f, indent=2, default=str)
        print(f"System information saved to {filename}")
        return True
    except Exception as e:
        print(f"Error saving system info: {e}")
        return False

if __name__ == "__main__":
    save_system_info()
```

### 2. Log File Analyzer
```python
#!/usr/bin/env python3
"""
Log File Analyzer
Analyzes log files for patterns, errors, and statistics
"""

import re
import json
from collections import Counter, defaultdict
from datetime import datetime
import argparse

class LogAnalyzer:
    def __init__(self, log_file):
        self.log_file = log_file
        self.patterns = {
            'error': re.compile(r'ERROR|CRITICAL|FATAL', re.IGNORECASE),
            'warning': re.compile(r'WARNING|WARN', re.IGNORECASE),
            'info': re.compile(r'INFO', re.IGNORECASE),
            'ip_address': re.compile(r'\b(?:\d{1,3}\.){3}\d{1,3}\b'),
            'timestamp': re.compile(r'\d{4}-\d{2}-\d{2}\s\d{2}:\d{2}:\d{2}')
        }
    
    def analyze(self):
        """Analyze log file and return statistics"""
        stats = {
            'total_lines': 0,
            'log_levels': Counter(),
            'ip_addresses': Counter(),
            'error_messages': [],
            'hourly_distribution': defaultdict(int),
            'file_info': {
                'name': self.log_file,
                'analyzed_at': datetime.now().isoformat()
            }
        }
        
        try:
            with open(self.log_file, 'r') as f:
                for line_num, line in enumerate(f, 1):
                    stats['total_lines'] += 1
                    
                    # Count log levels
                    for level, pattern in self.patterns.items():
                        if level in ['error', 'warning', 'info'] and pattern.search(line):
                            stats['log_levels'][level.upper()] += 1
                            if level == 'error':
                                stats['error_messages'].append({
                                    'line': line_num,
                                    'message': line.strip()
                                })
                    
                    # Extract IP addresses
                    ip_matches = self.patterns['ip_address'].findall(line)
                    for ip in ip_matches:
                        stats['ip_addresses'][ip] += 1
                    
                    # Extract timestamps for hourly distribution
                    timestamp_match = self.patterns['timestamp'].search(line)
                    if timestamp_match:
                        try:
                            dt = datetime.strptime(timestamp_match.group(), '%Y-%m-%d %H:%M:%S')
                            stats['hourly_distribution'][dt.hour] += 1
                        except ValueError:
                            pass
            
            return stats
            
        except FileNotFoundError:
            print(f"Error: Log file '{self.log_file}' not found")
            return None
        except Exception as e:
            print(f"Error analyzing log file: {e}")
            return None
    
    def generate_report(self, output_file=None):
        """Generate analysis report"""
        stats = self.analyze()
        if not stats:
            return
        
        # Create readable report
        report = f"""
LOG ANALYSIS REPORT
==================
File: {stats['file_info']['name']}
Analyzed: {stats['file_info']['analyzed_at']}
Total Lines: {stats['total_lines']}

LOG LEVELS:
-----------
"""
        for level, count in stats['log_levels'].most_common():
            report += f"{level}: {count}\n"
        
        report += f"""
TOP IP ADDRESSES:
----------------
"""
        for ip, count in stats['ip_addresses'].most_common(10):
            report += f"{ip}: {count} requests\n"
        
        if stats['error_messages']:
            report += f"""
RECENT ERRORS (last 5):
----------------------
"""
            for error in stats['error_messages'][-5:]:
                report += f"Line {error['line']}: {error['message']}\n"
        
        if output_file:
            with open(output_file, 'w') as f:
                f.write(report)
            print(f"Report saved to {output_file}")
        else:
            print(report)

if __name__ == "__main__":
    parser = argparse.ArgumentParser(description='Analyze log files')
    parser.add_argument('logfile', help='Path to log file')
    parser.add_argument('-o', '--output', help='Output report file')
    
    args = parser.parse_args()
    
    analyzer = LogAnalyzer(args.logfile)
    analyzer.generate_report(args.output)
```

### 3. File Backup Script
```python
#!/usr/bin/env python3
"""
Automated File Backup Script
Creates backups of specified directories with compression and rotation
"""

import os
import shutil
import tarfile
import logging
from datetime import datetime, timedelta
from pathlib import Path
import argparse

class BackupManager:
    def __init__(self, source_dir, backup_dir, retention_days=30):
        self.source_dir = Path(source_dir)
        self.backup_dir = Path(backup_dir)
        self.retention_days = retention_days
        
        # Setup logging
        logging.basicConfig(
            level=logging.INFO,
            format='%(asctime)s - %(levelname)s - %(message)s',
            handlers=[
                logging.FileHandler('backup.log'),
                logging.StreamHandler()
            ]
        )
        self.logger = logging.getLogger(__name__)
    
    def create_backup(self):
        """Create compressed backup of source directory"""
        try:
            # Ensure backup directory exists
            self.backup_dir.mkdir(parents=True, exist_ok=True)
            
            # Generate backup filename with timestamp
            timestamp = datetime.now().strftime('%Y%m%d_%H%M%S')
            backup_name = f"{self.source_dir.name}_backup_{timestamp}.tar.gz"
            backup_path = self.backup_dir / backup_name
            
            self.logger.info(f"Starting backup of {self.source_dir}")
            self.logger.info(f"Backup destination: {backup_path}")
            
            # Create compressed backup
            with tarfile.open(backup_path, 'w:gz') as tar:
                tar.add(self.source_dir, arcname=self.source_dir.name)
            
            backup_size = backup_path.stat().st_size / (1024 * 1024)  # MB
            self.logger.info(f"Backup completed successfully. Size: {backup_size:.2f} MB")
            
            return backup_path
            
        except Exception as e:
            self.logger.error(f"Backup failed: {e}")
            return None
    
    def cleanup_old_backups(self):
        """Remove backups older than retention period"""
        try:
            cutoff_date = datetime.now() - timedelta(days=self.retention_days)
            removed_count = 0
            
            for backup_file in self.backup_dir.glob("*.tar.gz"):
                file_time = datetime.fromtimestamp(backup_file.stat().st_mtime)
                if file_time < cutoff_date:
                    backup_file.unlink()
                    removed_count += 1
                    self.logger.info(f"Removed old backup: {backup_file.name}")
            
            if removed_count > 0:
                self.logger.info(f"Cleaned up {removed_count} old backups")
            else:
                self.logger.info("No old backups to clean up")
                
        except Exception as e:
            self.logger.error(f"Cleanup failed: {e}")
    
    def run_backup(self):
        """Run complete backup process"""
        self.logger.info("=== Backup Process Started ===")
        
        # Create backup
        backup_path = self.create_backup()
        if not backup_path:
            return False
        
        # Cleanup old backups
        self.cleanup_old_backups()
        
        self.logger.info("=== Backup Process Completed ===")
        return True

if __name__ == "__main__":
    parser = argparse.ArgumentParser(description='Automated backup script')
    parser.add_argument('source', help='Source directory to backup')
    parser.add_argument('destination', help='Backup destination directory')
    parser.add_argument('--retention', type=int, default=30, 
                       help='Retention period in days (default: 30)')
    
    args = parser.parse_args()
    
    backup_manager = BackupManager(args.source, args.destination, args.retention)
    success = backup_manager.run_backup()
    
    exit(0 if success else 1)
```

---

## Remote Server Management Scripts

### 4. SSH Connection Manager
```python
#!/usr/bin/env python3
"""
SSH Connection Manager
Manages SSH connections and executes remote commands
"""

import paramiko
import json
import logging
from pathlib import Path
import argparse

class SSHManager:
    def __init__(self, config_file='ssh_config.json'):
        self.config_file = config_file
        self.connections = {}
        self.load_config()
        
        # Setup logging
        logging.basicConfig(level=logging.INFO)
        self.logger = logging.getLogger(__name__)
    
    def load_config(self):
        """Load SSH configuration from file"""
        try:
            if Path(self.config_file).exists():
                with open(self.config_file, 'r') as f:
                    self.config = json.load(f)
            else:
                self.config = {'servers': {}}
                self.save_config()
        except Exception as e:
            self.logger.error(f"Error loading config: {e}")
            self.config = {'servers': {}}
    
    def save_config(self):
        """Save SSH configuration to file"""
        try:
            with open(self.config_file, 'w') as f:
                json.dump(self.config, f, indent=2)
        except Exception as e:
            self.logger.error(f"Error saving config: {e}")
    
    def add_server(self, name, hostname, username, key_file=None, password=None, port=22):
        """Add server configuration"""
        self.config['servers'][name] = {
            'hostname': hostname,
            'username': username,
            'port': port,
            'key_file': key_file,
            'password': password
        }
        self.save_config()
        self.logger.info(f"Added server configuration: {name}")
    
    def connect(self, server_name):
        """Establish SSH connection to server"""
        if server_name not in self.config['servers']:
            raise ValueError(f"Server '{server_name}' not found in configuration")
        
        server = self.config['servers'][server_name]
        
        try:
            ssh = paramiko.SSHClient()
            ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
            
            # Connect using key or password
            if server.get('key_file'):
                ssh.connect(
                    hostname=server['hostname'],
                    port=server['port'],
                    username=server['username'],
                    key_filename=server['key_file']
                )
            else:
                ssh.connect(
                    hostname=server['hostname'],
                    port=server['port'],
                    username=server['username'],
                    password=server.get('password')
                )
            
            self.connections[server_name] = ssh
            self.logger.info(f"Connected to {server_name}")
            return ssh
            
        except Exception as e:
            self.logger.error(f"Connection failed to {server_name}: {e}")
            return None
    
    def execute_command(self, server_name, command):
        """Execute command on remote server"""
        if server_name not in self.connections:
            ssh = self.connect(server_name)
            if not ssh:
                return None
        
        try:
            ssh = self.connections[server_name]
            stdin, stdout, stderr = ssh.exec_command(command)
            
            output = stdout.read().decode('utf-8')
            error = stderr.read().decode('utf-8')
            exit_code = stdout.channel.recv_exit_status()
            
            return {
                'command': command,
                'exit_code': exit_code,
                'output': output,
                'error': error,
                'success': exit_code == 0
            }
            
        except Exception as e:
            self.logger.error(f"Command execution failed: {e}")
            return None
    
    def close_connection(self, server_name):
        """Close SSH connection"""
        if server_name in self.connections:
            self.connections[server_name].close()
            del self.connections[server_name]
            self.logger.info(f"Closed connection to {server_name}")
    
    def close_all_connections(self):
        """Close all SSH connections"""
        for server_name in list(self.connections.keys()):
            self.close_connection(server_name)

# Example usage script
if __name__ == "__main__":
    parser = argparse.ArgumentParser(description='SSH Connection Manager')
    parser.add_argument('action', choices=['add', 'connect', 'execute', 'list'])
    parser.add_argument('--server', help='Server name')
    parser.add_argument('--hostname', help='Server hostname/IP')
    parser.add_argument('--username', help='SSH username')
    parser.add_argument('--key-file', help='SSH private key file')
    parser.add_argument('--command', help='Command to execute')
    
    args = parser.parse_args()
    
    ssh_manager = SSHManager()
    
    if args.action == 'add':
        if not all([args.server, args.hostname, args.username]):
            print("Error: --server, --hostname, and --username are required for 'add'")
            exit(1)
        ssh_manager.add_server(args.server, args.hostname, args.username, args.key_file)
        
    elif args.action == 'execute':
        if not all([args.server, args.command]):
            print("Error: --server and --command are required for 'execute'")
            exit(1)
        result = ssh_manager.execute_command(args.server, args.command)
        if result:
            print(f"Exit Code: {result['exit_code']}")
            print(f"Output: {result['output']}")
            if result['error']:
                print(f"Error: {result['error']}")
        
    elif args.action == 'list':
        print("Configured servers:")
        for name, config in ssh_manager.config['servers'].items():
            print(f"  {name}: {config['username']}@{config['hostname']}:{config['port']}")
```

---

## Configuration Management Scripts

### 5. Configuration Template Manager
```python
#!/usr/bin/env python3
"""
Configuration Template Manager
Manages configuration files using Jinja2 templates
"""

import os
import yaml
from jinja2 import Environment, FileSystemLoader, Template
from pathlib import Path
import argparse

class ConfigManager:
    def __init__(self, templates_dir='templates', configs_dir='configs'):
        self.templates_dir = Path(templates_dir)
        self.configs_dir = Path(configs_dir)
        
        # Ensure directories exist
        self.templates_dir.mkdir(exist_ok=True)
        self.configs_dir.mkdir(exist_ok=True)
        
        # Setup Jinja2 environment
        self.env = Environment(
            loader=FileSystemLoader(str(self.templates_dir)),
            trim_blocks=True,
            lstrip_blocks=True
        )
    
    def load_variables(self, vars_file):
        """Load variables from YAML file"""
        try:
            with open(vars_file, 'r') as f:
                return yaml.safe_load(f)
        except Exception as e:
            print(f"Error loading variables file: {e}")
            return {}
    
    def render_template(self, template_name, variables, output_file=None):
        """Render template with variables"""
        try:
            template = self.env.get_template(template_name)
            rendered = template.render(**variables)
            
            if output_file:
                output_path = self.configs_dir / output_file
                with open(output_path, 'w') as f:
                    f.write(rendered)
                print(f"Configuration written to {output_path}")
            else:
                print(rendered)
                
            return rendered
            
        except Exception as e:
            print(f"Error rendering template: {e}")
            return None
    
    def create_sample_template(self):
        """Create sample nginx configuration template"""
        nginx_template = """
server {
    listen {{ port | default(80) }};
    server_name {{ server_name }};
    
    location / {
        proxy_pass http://{{ upstream_host }}:{{ upstream_port }};
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
    
    {% if ssl_enabled %}
    # SSL Configuration
    listen 443 ssl;
    ssl_certificate {{ ssl_cert_path }};
    ssl_certificate_key {{ ssl_key_path }};
    {% endif %}
    
    # Logging
    access_log /var/log/nginx/{{ server_name }}_access.log;
    error_log /var/log/nginx/{{ server_name }}_error.log;
}
        """.strip()
        
        template_path = self.templates_dir / 'nginx.conf.j2'
        with open(template_path, 'w') as f:
            f.write(nginx_template)
        
        # Create sample variables file
        sample_vars = {
            'server_name': 'example.com',
            'port': 80,
            'upstream_host': 'localhost',
            'upstream_port': 3000,
            'ssl_enabled': False,
            'ssl_cert_path': '/etc/ssl/certs/example.com.crt',
            'ssl_key_path': '/etc/ssl/private/example.com.key'
        }
        
        vars_path = self.configs_dir / 'nginx_vars.yml'
        with open(vars_path, 'w') as f:
            yaml.dump(sample_vars, f, default_flow_style=False)
        
        print(f"Sample template created: {template_path}")
        print(f"Sample variables file created: {vars_path}")

if __name__ == "__main__":
    parser = argparse.ArgumentParser(description='Configuration Template Manager')
    parser.add_argument('action', choices=['render', 'sample'])
    parser.add_argument('--template', help='Template file name')
    parser.add_argument('--vars', help='Variables YAML file')
    parser.add_argument('--output', help='Output configuration file')
    
    args = parser.parse_args()
    
    config_manager = ConfigManager()
    
    if args.action == 'sample':
        config_manager.create_sample_template()
        
    elif args.action == 'render':
        if not args.template:
            print("Error: --template is required for 'render'")
            exit(1)
        
        variables = {}
        if args.vars:
            variables = config_manager.load_variables(args.vars)
        
        config_manager.render_template(args.template, variables, args.output)
```

---

## Usage Examples and Scripts

### Running the Scripts
```bash
# Make scripts executable
chmod +x *.py

# System information collector
./system_info.py

# Log analyzer
./log_analyzer.py /var/log/syslog -o log_report.txt

# Backup manager
./backup_manager.py /home/user/documents /backup/location --retention 14

# SSH manager - add server
./ssh_manager.py add --server webserver1 --hostname 192.168.1.100 --username admin --key-file ~/.ssh/id_rsa

# SSH manager - execute command
./ssh_manager.py execute --server webserver1 --command "df -h"

# Configuration manager - create sample
./config_manager.py sample

# Configuration manager - render template
./config_manager.py render --template nginx.conf.j2 --vars nginx_vars.yml --output nginx.conf
```

### Automation Workflow Script
```python
#!/usr/bin/env python3
"""
DevOps Automation Workflow
Combines multiple automation tasks into a workflow
"""

import subprocess
import json
import logging
from datetime import datetime
from pathlib import Path

class DevOpsWorkflow:
    def __init__(self):
        self.setup_logging()
        self.results = []
    
    def setup_logging(self):
        """Setup logging configuration"""
        logging.basicConfig(
            level=logging.INFO,
            format='%(asctime)s - %(levelname)s - %(message)s',
            handlers=[
                logging.FileHandler('workflow.log'),
                logging.StreamHandler()
            ]
        )
        self.logger = logging.getLogger(__name__)
    
    def run_task(self, task_name, command, check_success=True):
        """Run a workflow task"""
        self.logger.info(f"Starting task: {task_name}")
        
        try:
            result = subprocess.run(
                command,
                shell=True,
                capture_output=True,
                text=True,
                timeout=300  # 5 minute timeout
            )
            
            task_result = {
                'task': task_name,
                'command': command,
                'return_code': result.returncode,
                'output': result.stdout,
                'error': result.stderr,
                'success': result.returncode == 0,
                'timestamp': datetime.now().isoformat()
            }
            
            self.results.append(task_result)
            
            if task_result['success']:
                self.logger.info(f"Task completed successfully: {task_name}")
            else:
                self.logger.error(f"Task failed: {task_name}")
                if check_success:
                    raise Exception(f"Task failed: {task_name}")
            
            return task_result
            
        except subprocess.TimeoutExpired:
            self.logger.error(f"Task timed out: {task_name}")
            raise Exception(f"Task timed out: {task_name}")
        except Exception as e:
            self.logger.error(f"Task error: {task_name} - {e}")
            raise
    
    def generate_report(self, report_file='workflow_report.json'):
        """Generate workflow execution report"""
        report = {
            'workflow_execution': {
                'start_time': self.results[0]['timestamp'] if self.results else None,
                'end_time': self.results[-1]['timestamp'] if self.results else None,
                'total_tasks': len(self.results),
                'successful_tasks': sum(1 for r in self.results if r['success']),
                'failed_tasks': sum(1 for r in self.results if not r['success'])
            },
            'tasks': self.results
        }
        
        with open(report_file, 'w') as f:
            json.dump(report, f, indent=2)
        
        self.logger.info(f"Workflow report saved to {report_file}")
        return report

# Example workflow
if __name__ == "__main__":
    workflow = DevOpsWorkflow()
    
    try:
        # Example workflow tasks
        workflow.run_task("System Check", "python3 system_info.py")
        workflow.run_task("Log Analysis", "python3 log_analyzer.py /var/log/syslog")
        workflow.run_task("Backup", "python3 backup_manager.py /home/user/docs /backup")
        
        # Generate final report
        report = workflow.generate_report()
        
        print("Workflow completed successfully!")
        print(f"Total tasks: {report['workflow_execution']['total_tasks']}")
        print(f"Successful: {report['workflow_execution']['successful_tasks']}")
        print(f"Failed: {report['workflow_execution']['failed_tasks']}")
        
    except Exception as e:
        workflow.logger.error(f"Workflow failed: {e}")
        workflow.generate_report()
        exit(1)
```

---

## Next Steps

After mastering basic automation scripts:

1. **Learn advanced Python techniques** → See `02-Advanced_Automation_Techniques.md`
2. **Explore DevOps-specific libraries** → See `../DevOps_Specific_Libraries/`
3. **Practice with cloud automation** → See cloud modules
4. **Build CI/CD automation** → See pipeline modules

---

## 🔧 Configuration Notes

- **Error Handling**: Always implement proper error handling and logging
- **Security**: Never hardcode credentials in scripts
- **Testing**: Test scripts in safe environments before production use
- **Documentation**: Keep scripts well-documented and maintainable

## 📚 Additional Resources

- [Python Automation Cookbook](https://www.packtpub.com/product/python-automation-cookbook/9781789133806)
- [Automate the Boring Stuff with Python](https://automatetheboringstuff.com/)
- [Python DevOps Tools](https://github.com/ansible/ansible)
- [Paramiko Documentation](https://docs.paramiko.org/) 