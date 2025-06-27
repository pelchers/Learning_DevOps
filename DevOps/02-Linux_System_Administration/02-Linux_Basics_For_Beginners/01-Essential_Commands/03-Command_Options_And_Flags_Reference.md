# Linux Command Options and Flags: Complete Reference Guide

## 📖 What This File Does
Master ALL the command-line options and flags (the `-` parameters) used with Linux commands. This comprehensive reference covers every major command option, categorized by purpose, with practical examples and real-world usage scenarios.

## 🎯 Learning Objectives
- Understand what command flags and options are
- Master file permission flags (`-r`, `-w`, `-x`, etc.)
- Learn file operation flags (`-f`, `-i`, `-v`, etc.)
- Use directory and navigation options
- Apply search and filtering flags
- Combine multiple options effectively
- Troubleshoot using diagnostic flags

## 📋 Prerequisites
- Basic Linux navigation knowledge
- Understanding of file operations
- Familiarity with terminal usage

---

## 🔍 **Understanding Command Options**

### **What Are Command Flags?**
```bash
# Basic structure: command -flag argument
ls -l                    # -l is a flag (long format)
rm -rf directory/        # -r and -f are combined flags
find . -name "*.txt"     # -name is an option with value

# Flags can be:
# Short form: -l, -a, -r (single dash, single letter)
# Long form: --long, --all, --recursive (double dash, full word)
# Combined: -la, -rf (multiple short flags together)
```

### **Flag Categories Overview:**
- **📁 File & Directory Operations** - `-r`, `-f`, `-i`, `-v`
- **🔒 Permissions & Ownership** - `-x`, `-w`, `-u`, `-g`, `-o`
- **📊 Display & Formatting** - `-l`, `-a`, `-h`, `-s`, `-t`
- **🔍 Search & Filtering** - `-n`, `-i`, `-v`, `-c`, `-o`
- **⚡ Performance & Behavior** - `-f`, `-q`, `-v`, `-x`
- **🛠️ Debugging & Diagnostics** - `-v`, `-d`, `-x`, `-e`

---

## 📁 **File & Directory Operations Flags**

### **`ls` (List) Command Options**

#### **Display Format Options:**
```bash
# Basic display options
ls -l                    # Long format (detailed info)
ls -a                    # Show hidden files (starting with .)
ls -A                    # Show hidden files except . and ..
ls -la                   # Long format + hidden files (VERY COMMON)
ls -lh                   # Long format + human-readable sizes
ls -ld directory/        # Show directory info, not contents

# Size and space options
ls -s                    # Show file sizes in blocks
ls -S                    # Sort by file size (largest first)
ls -sh                   # Show sizes in human-readable format
```

#### **Sorting Options:**
```bash
# Time-based sorting
ls -t                    # Sort by modification time (newest first)
ls -tr                   # Sort by modification time (oldest first)
ls -ltr                  # Long format + time sorted (VERY USEFUL)
ls -u                    # Sort by access time
ls -c                    # Sort by change time

# Other sorting
ls -r                    # Reverse order
ls -S                    # Sort by size
ls -X                    # Sort by extension
```

#### **Practical `ls` Combinations:**
```bash
# Most commonly used combinations
ls -la                   # Show everything with details
ls -lh                   # Detailed view with readable sizes
ls -ltr                  # Show files by time (oldest to newest)
ls -latr                 # Everything, detailed, by time
ls -lS                   # Detailed view sorted by size

# DevOps specific usage
ls -la /etc/             # Check system config files
ls -ltr /var/log/        # Check recent log files
ls -lh *.tar.gz          # Check backup file sizes
```

### **`cp` (Copy) Command Options**

#### **Copy Behavior Options:**
```bash
# Basic copy options
cp -r source/ dest/      # Recursive copy (for directories)
cp -R source/ dest/      # Same as -r (recursive)
cp -a source/ dest/      # Archive mode (preserves everything)
cp -p file1 file2        # Preserve timestamps and permissions

# Safety options
cp -i source dest        # Interactive (ask before overwriting)
cp -n source dest        # No overwrite (don't replace existing)
cp -u source dest        # Update only (copy if newer)
cp -b source dest        # Backup existing files before overwriting
```

#### **Verbose and Force Options:**
```bash
# Information options
cp -v source dest        # Verbose (show what's being copied)
cp -f source dest        # Force (overwrite without asking)

# Advanced options
cp -l source dest        # Create hard links instead of copying
cp -s source dest        # Create symbolic links instead of copying
cp -x source/ dest/      # Stay on same filesystem
```

#### **Practical `cp` Combinations:**
```bash
# Safe copying with feedback
cp -riv source/ backup/  # Recursive, interactive, verbose
cp -ruv source/ dest/    # Recursive, update only, verbose

# DevOps backup scenarios
cp -rp /etc/ /backup/etc-$(date +%Y%m%d)/  # Backup with date
cp -au /home/ /backup/   # Archive mode, update only
cp -rlv configs/ /tmp/   # Link files for testing
```

### **`mv` (Move/Rename) Command Options**

#### **Move Behavior Options:**
```bash
# Basic move options
mv -i source dest        # Interactive (ask before overwriting)
mv -f source dest        # Force (overwrite without asking)
mv -n source dest        # No overwrite (don't replace existing)
mv -u source dest        # Update only (move if newer)
mv -v source dest        # Verbose (show what's being moved)
mv -b source dest        # Backup existing files before overwriting
```

#### **Practical `mv` Usage:**
```bash
# Safe file operations
mv -iv oldname newname   # Interactive rename with feedback
mv -uv source/ dest/     # Update and show progress

# Bulk operations
mv -v *.txt documents/   # Move all text files with feedback
mv -i *.log /var/log/    # Safely move log files
```

### **`rm` (Remove) Command Options**

#### **Remove Behavior Options:**
```bash
# Basic remove options
rm -r directory/         # Recursive (remove directories)
rm -R directory/         # Same as -r (recursive)
rm -f file               # Force (no error if file doesn't exist)
rm -i file               # Interactive (ask before each deletion)
rm -I directory/         # Interactive once for 3+ files

# Safety and information
rm -v file               # Verbose (show what's being removed)
rm --preserve-root       # Prevent removing root directory (safety)
```

#### **⚠️ DANGEROUS Combinations:**
```bash
# EXTREMELY DANGEROUS - NEVER USE THESE:
# rm -rf /               # DELETES ENTIRE SYSTEM!
# rm -rf *               # DELETES EVERYTHING IN CURRENT DIRECTORY!

# Safe practices instead:
rm -riv directory/       # Interactive, recursive, verbose
rm -I *.tmp              # Interactive for multiple files
ls files_to_delete*      # CHECK FIRST what you're deleting
rm -v files_to_delete*   # Then delete with verbose output
```

#### **Practical `rm` Usage:**
```bash
# Safe deletion practices
rm -iv unwanted.txt      # Interactive deletion with feedback
rm -rfv old_project/     # Remove directory with feedback
rm -I *.log              # Interactive for multiple files

# DevOps cleanup scenarios
find . -name "*.tmp" -exec rm -v {} \;  # Clean temp files safely
rm -rfv /tmp/build-*     # Clean build directories
```

---

## 🔒 **Permission & Ownership Flags**

### **`chmod` (Change Mode) Command Options**

#### **Numeric Permission Flags:**
```bash
# Common numeric permissions
chmod 755 script.sh      # rwxr-xr-x (owner full, others read/execute)
chmod 644 file.txt       # rw-r--r-- (owner read/write, others read)
chmod 600 secret.conf    # rw------- (owner only)
chmod 777 public.sh      # rwxrwxrwx (everyone full access)
chmod 700 private/       # rwx------ (owner only for directory)

# Advanced numeric permissions
chmod 4755 script.sh     # Set SUID bit (run as owner)
chmod 2755 directory/    # Set SGID bit (inherit group)
chmod 1755 shared/       # Set sticky bit (only owner can delete)
```

#### **Symbolic Permission Flags:**
```bash
# User type flags
chmod u+x script.sh      # User/owner: add execute
chmod g+w file.txt       # Group: add write
chmod o-r secret.txt     # Others: remove read
chmod a+r public.txt     # All: add read

# Permission type flags
chmod +x script.sh       # Add execute for all (shortcut)
chmod u+rwx,go+rx file   # User: full, Group/Others: read/execute
chmod u=rw,go=r file     # Set exact permissions
```

#### **`chmod` Advanced Options:**
```bash
# Recursive and verbose options
chmod -R 755 directory/  # Recursive (apply to all subdirectories)
chmod -v +x script.sh    # Verbose (show what changed)
chmod -c +x script.sh    # Show only changes made
chmod -f +x script.sh    # Silent (suppress error messages)

# Reference permissions
chmod --reference=file1 file2  # Copy permissions from file1 to file2
```

#### **Practical `chmod` Combinations:**
```bash
# DevOps script permissions
chmod -v +x *.sh         # Make all shell scripts executable
chmod -R 755 /var/www/   # Web server permissions
chmod -R 700 ~/.ssh/     # SSH directory security

# Batch permission changes
find . -name "*.sh" -exec chmod +x {} \;  # Make all scripts executable
find . -type d -exec chmod 755 {} \;      # Set directory permissions
find . -type f -exec chmod 644 {} \;      # Set file permissions
```

### **`chown` (Change Owner) Command Options**

#### **Ownership Change Options:**
```bash
# Basic ownership changes
chown user file.txt            # Change owner
chown user:group file.txt      # Change owner and group
chown :group file.txt          # Change group only
chown -R user:group directory/ # Recursive ownership change

# Advanced ownership options
chown -v user file.txt         # Verbose (show changes)
chown -c user file.txt         # Show only actual changes
chown -f user file.txt         # Silent (suppress errors)
chown --reference=file1 file2  # Copy ownership from file1
```

#### **Practical `chown` Usage:**
```bash
# Web server file ownership
sudo chown -R www-data:www-data /var/www/html/
sudo chown -v nginx:nginx /etc/nginx/nginx.conf

# DevOps ownership scenarios
sudo chown -R user:docker /var/lib/docker/
sudo chown -v root:root /etc/crontab
```

---

## 📊 **Display & Information Flags**

### **`ps` (Process Status) Command Options**

#### **Process Display Options:**
```bash
# Basic process options
ps -a                    # Show all processes with terminals
ps -u                    # Show user-oriented format
ps -x                    # Show processes without terminals
ps aux                   # All processes, user format (VERY COMMON)
ps -ef                   # Full format listing

# Detailed process information
ps -l                    # Long format
ps -f                    # Full format
ps -j                    # Jobs format
ps -o pid,cmd,cpu,mem    # Custom output format
```

#### **Process Filtering Options:**
```bash
# User and group filtering
ps -u username           # Processes for specific user
ps -U username           # Real user ID processes
ps -g groupname          # Processes for specific group

# Process tree and hierarchy
ps -H                    # Show process hierarchy
ps --forest              # ASCII art process tree
ps -T                    # Show threads
```

#### **Practical `ps` Combinations:**
```bash
# DevOps monitoring combinations
ps aux | grep nginx      # Find nginx processes
ps -ef | grep docker     # Find docker processes
ps aux --sort=-%cpu      # Sort by CPU usage
ps aux --sort=-%mem      # Sort by memory usage

# Process troubleshooting
ps -eo pid,ppid,cmd,cpu,mem  # Custom format for analysis
ps -u www-data           # Check web server processes
ps -T -p 1234            # Show threads for specific process
```

### **`df` (Disk Free) Command Options**

#### **Disk Usage Display Options:**
```bash
# Basic disk information
df -h                    # Human-readable sizes (VERY COMMON)
df -k                    # Sizes in kilobytes
df -m                    # Sizes in megabytes
df -T                    # Show filesystem types
df -i                    # Show inode information

# Output formatting
df -a                    # Show all filesystems
df -l                    # Local filesystems only
df -x tmpfs              # Exclude specific filesystem type
df --total               # Show grand total
```

#### **Practical `df` Usage:**
```bash
# DevOps disk monitoring
df -h | grep -v tmpfs    # Exclude temporary filesystems
df -h /var/log           # Check log directory space
df -ih                   # Check inode usage (important!)
df -h --total            # Get total disk usage summary
```

### **`du` (Disk Usage) Command Options**

#### **Directory Size Options:**
```bash
# Basic size information
du -h directory/         # Human-readable sizes
du -s directory/         # Summary only (total size)
du -sh directory/        # Summary in human-readable format
du -a directory/         # Show all files and directories

# Depth and sorting options
du -d 1 directory/       # Maximum depth of 1 level
du --max-depth=2 ./      # Maximum depth of 2 levels
du -h | sort -hr         # Sort by size (largest first)
du -sh */ | sort -hr     # Directory sizes sorted
```

#### **Practical `du` Usage:**
```bash
# DevOps disk usage analysis
du -sh /var/log/*        # Check log file sizes
du -d 1 -h ~/ | sort -hr # Check home directory usage
du -ah . | head -20      # Top 20 largest files/directories
```

---

## 🔍 **Search & Filtering Flags**

### **`grep` (Global Regular Expression Print) Options**

#### **Search Behavior Options:**
```bash
# Case and pattern options
grep -i "pattern" file   # Case-insensitive search
grep -v "pattern" file   # Invert match (lines NOT containing pattern)
grep -w "word" file      # Match whole words only
grep -x "line" file      # Match whole lines only

# Context and line options
grep -n "pattern" file   # Show line numbers
grep -c "pattern" file   # Count matches only
grep -l "pattern" files* # List files containing pattern
grep -L "pattern" files* # List files NOT containing pattern
```

#### **Advanced `grep` Options:**
```bash
# Context display
grep -A 3 "pattern" file # Show 3 lines After match
grep -B 3 "pattern" file # Show 3 lines Before match
grep -C 3 "pattern" file # Show 3 lines of Context (before and after)

# Recursive and file options
grep -r "pattern" dir/   # Recursive search in directory
grep -R "pattern" dir/   # Recursive with symbolic links
grep -f patterns.txt file # Use patterns from file
```

#### **Extended and Regex Options:**
```bash
# Extended regular expressions
grep -E "pat1|pat2" file # Extended regex (OR pattern)
grep -P "\\d+" file      # Perl-compatible regex
grep -F "literal" file   # Fixed strings (no regex)

# Output formatting
grep --color=always      # Force color output
grep -o "pattern" file   # Show only matching part
grep -H "pattern" files* # Always show filename
```

#### **Practical `grep` Combinations:**
```bash
# DevOps log analysis
grep -i error /var/log/syslog                # Find errors (case-insensitive)
grep -A 5 -B 5 "failed" /var/log/auth.log   # Context around failures
grep -r "TODO" --include="*.py" .           # Find TODOs in Python files
grep -v "^#" config.conf | grep -v "^$"     # Remove comments and empty lines

# Process and system monitoring
ps aux | grep -v grep | grep nginx          # Find nginx processes (exclude grep itself)
dmesg | grep -i error                       # Check kernel errors
journalctl | grep -i failed                 # Check systemd failures
```

### **`find` Command Options**

#### **Search Type Options:**
```bash
# File type options
find . -type f           # Find regular files
find . -type d           # Find directories
find . -type l           # Find symbolic links
find . -type b           # Find block devices
find . -type c           # Find character devices

# Name and pattern options
find . -name "*.txt"     # Find by exact name pattern
find . -iname "*.TXT"    # Case-insensitive name search
find . -path "*/logs/*"  # Find by path pattern
find . -regex ".*\\.py$" # Find using regular expressions
```

#### **Size and Time Options:**
```bash
# Size-based searches
find . -size +1M         # Files larger than 1MB
find . -size -100k       # Files smaller than 100KB
find . -size 1G          # Files exactly 1GB
find . -empty            # Empty files and directories

# Time-based searches
find . -mtime -7         # Modified in last 7 days
find . -mtime +30        # Modified more than 30 days ago
find . -atime -1         # Accessed in last day
find . -ctime +7         # Changed more than 7 days ago
find . -newer file.txt   # Newer than reference file
```

#### **Permission and Ownership Options:**
```bash
# Permission-based searches
find . -perm 755         # Exact permission match
find . -perm -644        # At least these permissions
find . -perm /u+w        # Any write permission for user
find . -executable       # Executable files

# Ownership searches
find . -user username    # Files owned by user
find . -group groupname  # Files owned by group
find . -uid 1000         # Files with specific user ID
find . -gid 100          # Files with specific group ID
```

#### **Action Options:**
```bash
# Actions on found files
find . -name "*.tmp" -delete              # Delete found files
find . -name "*.sh" -exec chmod +x {} \;  # Execute command on each
find . -name "*.log" -exec ls -l {} \;    # List details of found files
find . -type f -print0 | xargs -0 wc -l  # Count lines (handle spaces)
```

#### **Practical `find` Combinations:**
```bash
# DevOps file management
find /var/log -name "*.log" -mtime +7 -delete        # Clean old logs
find . -name "*.sh" -type f -exec chmod +x {} \;     # Make scripts executable
find /tmp -user $USER -mtime +1 -delete              # Clean your old temp files
find . -name ".git" -type d -prune -o -name "*.py" -print  # Find Python files, skip .git

# Security and maintenance
find /home -perm -002 -type f                        # World-writable files (security risk)
find . -size +100M -exec ls -lh {} \;               # Find large files
find /etc -name "*.conf" -mtime -1                   # Recently modified configs
```

---

## ⚡ **Performance & Behavior Flags**

### **`tar` (Tape Archive) Command Options**

#### **Archive Operation Options:**
```bash
# Basic operations
tar -c file.tar files*   # Create archive
tar -x file.tar          # Extract archive
tar -t file.tar          # List archive contents
tar -r file.tar newfile  # Append to archive
tar -u file.tar files*   # Update archive

# Compression options
tar -z file.tar.gz       # Use gzip compression
tar -j file.tar.bz2      # Use bzip2 compression
tar -J file.tar.xz       # Use xz compression
tar -a file.tar.gz       # Auto-detect compression by extension
```

#### **Archive Behavior Options:**
```bash
# Verbose and information
tar -v                   # Verbose (show files being processed)
tar -vv                  # Very verbose (more details)
tar -f filename          # Specify archive filename
tar -C directory         # Change to directory before operation

# Preservation options
tar -p                   # Preserve permissions
tar -k                   # Keep existing files (don't overwrite)
tar -m                   # Don't restore modification times
tar --numeric-owner      # Use numeric user/group IDs
```

#### **Practical `tar` Combinations:**
```bash
# DevOps backup and deployment
tar -czf backup-$(date +%Y%m%d).tar.gz /important/data/     # Create compressed backup
tar -xzf application.tar.gz -C /opt/                        # Extract to specific directory
tar -tzf backup.tar.gz | head -20                          # Preview archive contents
tar -czf - /source/ | ssh user@server 'tar -xzf - -C /dest/' # Remote backup over SSH

# Archive maintenance
tar -df archive.tar                                         # Compare archive with filesystem
tar --delete -f archive.tar unwanted-file                   # Remove file from archive
tar -rf archive.tar new-files*                             # Add files to existing archive
```

### **`ssh` (Secure Shell) Command Options**

#### **Connection Options:**
```bash
# Basic connection options
ssh -p 2222 user@host    # Specify port
ssh -i keyfile user@host # Use specific SSH key
ssh -l username host     # Specify username
ssh -4 user@host         # Force IPv4
ssh -6 user@host         # Force IPv6

# Authentication options
ssh -o PasswordAuthentication=no user@host  # Disable password auth
ssh -o StrictHostKeyChecking=no user@host   # Skip host key checking
ssh -A user@host         # Enable agent forwarding
ssh -X user@host         # Enable X11 forwarding
```

#### **Advanced SSH Options:**
```bash
# Tunneling and forwarding
ssh -L 8080:localhost:80 user@host  # Local port forwarding
ssh -R 8080:localhost:80 user@host  # Remote port forwarding
ssh -D 1080 user@host               # SOCKS proxy

# Connection behavior
ssh -n user@host command            # Don't read stdin
ssh -f user@host command            # Background after authentication
ssh -T user@host                    # Disable pseudo-terminal
ssh -t user@host command            # Force pseudo-terminal
```

#### **Practical SSH Usage:**
```bash
# DevOps server management
ssh -i ~/.ssh/production.key deploy@server.com             # Production deployment
ssh -L 3306:db.internal:3306 bastion@jump.server.com      # Database tunnel through bastion
ssh -A -t jump.server.com ssh internal.server.com         # Jump through bastion with agent

# Batch operations
ssh -o ConnectTimeout=5 user@host 'uptime'                 # Quick health check
ssh -n user@host 'nohup ./deploy.sh > deploy.log 2>&1 &'  # Background deployment
```

---

## 🛠️ **Debugging & Diagnostic Flags**

### **`curl` Command Options**

#### **HTTP Request Options:**
```bash
# Request method options
curl -X GET url          # GET request (default)
curl -X POST url         # POST request
curl -X PUT url          # PUT request
curl -X DELETE url       # DELETE request
curl -d "data" url       # POST data
curl -F "file=@path" url # Upload file

# Header and authentication
curl -H "Header: value" url          # Add custom header
curl -u user:pass url                # Basic authentication
curl -k url                          # Ignore SSL certificate errors
curl -L url                          # Follow redirects
```

#### **Output and Information Options:**
```bash
# Output control
curl -o filename url     # Save to file
curl -O url              # Save with remote filename
curl -s url              # Silent mode (no progress)
curl -S url              # Show errors even in silent mode
curl -v url              # Verbose (show request/response details)
curl -I url              # Head request only (headers)

# Performance and timing
curl -w "%{http_code}" url           # Show HTTP status code
curl -w "%{time_total}" url          # Show total time
curl --connect-timeout 10 url        # Connection timeout
curl --max-time 30 url               # Maximum total time
```

#### **Practical `curl` Usage:**
```bash
# DevOps API testing and monitoring
curl -s -o /dev/null -w "%{http_code}" https://api.example.com/health  # Health check
curl -H "Authorization: Bearer $TOKEN" https://api.example.com/data    # API with auth
curl -X POST -H "Content-Type: application/json" -d '{"key":"value"}' url  # JSON API call

# File downloads and uploads
curl -L -o installer.sh https://get.docker.com/                        # Download script
curl -F "backup=@backup.tar.gz" https://backup.service.com/upload      # Upload backup
```

### **`systemctl` (System Control) Options**

#### **Service Management Options:**
```bash
# Basic service operations
systemctl start service   # Start service
systemctl stop service    # Stop service
systemctl restart service # Restart service
systemctl reload service  # Reload configuration
systemctl status service  # Show service status

# Service state management
systemctl enable service  # Enable at boot
systemctl disable service # Disable at boot
systemctl mask service    # Prevent service from running
systemctl unmask service  # Allow service to run
```

#### **Information and Debugging Options:**
```bash
# Service information
systemctl -l status service         # Full status (no truncation)
systemctl is-active service         # Check if service is running
systemctl is-enabled service        # Check if service is enabled
systemctl is-failed service         # Check if service failed

# System-wide operations
systemctl list-units                # List all units
systemctl list-units --failed       # List failed units
systemctl daemon-reload              # Reload systemd configuration
systemctl reboot                     # Reboot system
systemctl poweroff                   # Shutdown system
```

#### **Practical `systemctl` Usage:**
```bash
# DevOps service management
systemctl status nginx.service              # Check web server status
systemctl restart docker.service            # Restart Docker daemon
systemctl enable --now postgresql.service   # Enable and start database

# System monitoring and troubleshooting
systemctl list-units --failed               # Find failed services
systemctl -l status ssh.service             # Detailed SSH service status
journalctl -u nginx.service -f              # Follow nginx logs
```

---

## 🎯 **Command Combination Patterns**

### **Common Flag Combinations by Use Case**

#### **File Management Patterns:**
```bash
# Safe file operations
cp -riv source/ destination/     # Recursive, interactive, verbose
mv -iv oldfile newfile          # Interactive rename with feedback
rm -riv directory/              # Recursive, interactive, verbose remove
ls -latr                        # Everything, detailed, by time

# Backup and archive patterns
tar -czf backup-$(date +%Y%m%d).tar.gz /data/    # Compressed backup with date
cp -rup source/ backup/         # Recursive, update, preserve attributes
rsync -avz --delete source/ dest/  # Advanced sync with compression
```

#### **System Monitoring Patterns:**
```bash
# Process monitoring
ps aux | grep -v grep | grep process_name    # Find specific processes
ps aux --sort=-%cpu | head -10              # Top CPU users
ps aux --sort=-%mem | head -10              # Top memory users

# Disk monitoring
df -h | grep -v tmpfs           # Disk usage excluding temp filesystems
du -sh */ | sort -hr            # Directory sizes sorted largest first
find . -size +100M -exec ls -lh {} \;  # Find large files with details
```

#### **Log Analysis Patterns:**
```bash
# Log file analysis
tail -f /var/log/syslog | grep -i error     # Follow log for errors
grep -A 5 -B 5 "ERROR" /var/log/app.log     # Context around errors
journalctl -u service.service -f --since "1 hour ago"  # Recent service logs

# Log cleanup and maintenance
find /var/log -name "*.log" -mtime +7 -exec gzip {} \;     # Compress old logs
find /var/log -name "*.gz" -mtime +30 -delete              # Delete old compressed logs
```

#### **DevOps Deployment Patterns:**
```bash
# Deployment and configuration
ssh -i deploy.key user@server 'sudo systemctl restart app.service'     # Remote restart
scp -r -i deploy.key ./config/ user@server:/etc/app/                   # Copy configs
curl -f -s -o /dev/null https://app.example.com/health || echo "FAIL"  # Health check

# Security and permissions
find /var/www -type f -exec chmod 644 {} \;     # Set file permissions
find /var/www -type d -exec chmod 755 {} \;     # Set directory permissions
chown -R www-data:www-data /var/www/html/        # Set web server ownership
```

### **Flag Priority and Conflicts**

#### **Order Matters:**
```bash
# Some flags must come in specific order
tar -czf archive.tar.gz files/  # -f must come last (filename)
ssh -p 2222 -i key user@host    # Connection options before user@host
find . -name "*.txt" -exec rm {} \;  # -exec must come after search criteria
```

#### **Conflicting Flags:**
```bash
# These flags conflict with each other
ls -a -A                # -a shows all, -A shows almost all (conflict)
grep -i -F              # -i (ignore case) conflicts with -F (fixed strings)
sort -n -r              # These work together (numeric reverse sort)
```

---

## 📚 **Quick Reference by Command**

### **Essential Commands with Most Used Flags:**

| Command | Most Common Flags | What They Do |
|---------|-------------------|--------------|
| `ls` | `-la`, `-ltr`, `-lh` | List all with details, by time, human sizes |
| `cp` | `-r`, `-i`, `-v`, `-u` | Recursive, interactive, verbose, update |
| `mv` | `-i`, `-v`, `-u` | Interactive, verbose, update only |
| `rm` | `-r`, `-i`, `-f`, `-v` | Recursive, interactive, force, verbose |
| `chmod` | `-R`, `-v`, `+x` | Recursive, verbose, make executable |
| `find` | `-name`, `-type`, `-exec` | Find by name, type, execute command |
| `grep` | `-i`, `-r`, `-n`, `-v` | Ignore case, recursive, line numbers, invert |
| `ps` | `aux`, `-ef`, `-u` | All processes, full format, user processes |
| `tar` | `-czf`, `-xzf`, `-tzf` | Create/extract/list compressed archives |
| `ssh` | `-i`, `-p`, `-L`, `-A` | Key file, port, tunnel, agent forwarding |

### **Flag Combination Templates:**

#### **File Operations Template:**
```bash
# Template: command -[safety][action][verbosity] source destination
cp -riv source/ dest/        # Recursive, interactive, verbose copy
mv -iv oldname newname       # Interactive, verbose move
rm -riv directory/           # Recursive, interactive, verbose remove
```

#### **Search Template:**
```bash
# Template: find path -[type][name][action]
find . -type f -name "*.log" -exec grep "ERROR" {} \;
find /var -type d -name "*cache*" -exec du -sh {} \;
```

#### **System Monitoring Template:**
```bash
# Template: ps [format][sort] | grep [filter]
ps aux --sort=-%cpu | grep -v grep | grep nginx
ps -ef | grep -E "(apache|nginx|httpd)"
```

---

## 💡 **Pro Tips for Command Flags**

### **Remember Common Patterns:**
1. **Safety First**: Always use `-i` (interactive) for destructive operations
2. **Verbose Output**: Use `-v` when learning or debugging
3. **Recursive Operations**: Most commands use `-r` or `-R` for directories
4. **Human Readable**: Use `-h` with size-related commands
5. **Combine Wisely**: Start with basic flags, add complexity gradually

### **Flag Discovery:**
```bash
# Learn about any command's flags
man command              # Full manual
command --help           # Quick help
info command             # Info pages
whatis command           # Brief description
```

### **Common Mistakes to Avoid:**
```bash
# Wrong: rm -rf * (deletes everything!)
# Right: rm -rf specific_directory/

# Wrong: chmod 777 file (too permissive!)
# Right: chmod 644 file (appropriate permissions)

# Wrong: find / -name file (searches entire system!)
# Right: find . -name file (searches current directory)
```

---

## ➡️ **Next Steps**

**You're ready to advance when you can:**
- [ ] Use appropriate flags for file operations safely
- [ ] Combine multiple flags effectively
- [ ] Choose the right search and filter options
- [ ] Apply permission flags correctly
- [ ] Troubleshoot using diagnostic flags
- [ ] Build complex command combinations for DevOps tasks

**Continue to:**
1. **04-Text_Processing_And_Pipes** - Combining commands with pipes
2. **05-Process_Management** - Managing system processes
3. **06-Network_Commands** - Network troubleshooting commands

---

📄 **File Path:** `/DevOps/02-Linux_System_Administration/02-Linux_Basics_For_Beginners/01-Essential_Commands/03-Command_Options_And_Flags_Reference.md` 