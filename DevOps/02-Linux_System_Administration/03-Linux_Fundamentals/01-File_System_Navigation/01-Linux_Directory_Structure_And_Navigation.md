# Linux Directory Structure and Navigation

## 📖 What This File Does
Master Linux file system navigation and directory structure understanding - the foundation skill every DevOps engineer needs. You'll learn to navigate confidently through any Linux system, understand the purpose of each directory, and perform essential file operations.

## 🎯 Learning Objectives
- Understand the Linux Filesystem Hierarchy Standard (FHS)
- Navigate the file system using absolute and relative paths
- Master essential navigation commands (`cd`, `ls`, `pwd`, `find`)
- Learn the purpose and contents of critical system directories
- Perform basic file and directory operations with confidence
- Build navigation skills essential for DevOps automation

## 📋 Prerequisites
- Access to a Linux system (VM, WSL, or native installation)
- Basic understanding of what a command line is
- Completed DevOps concepts overview

---

## 🏗️ Linux Filesystem Hierarchy Standard (FHS)

### Understanding the Root Directory Structure

The Linux file system starts at the root directory `/` and branches out in a hierarchical tree structure. Every DevOps engineer must understand this layout:

```bash
# Display the root directory structure
ls -la /

# Expected output explanation:
drwxr-xr-x   2 root root  4096 Jan 15 10:30 bin     # Essential command binaries
drwxr-xr-x   3 root root  4096 Jan 15 10:30 boot    # Boot loader files
drwxr-xr-x  19 root root  3860 Jan 16 08:45 dev     # Device files
drwxr-xr-x 143 root root 12288 Jan 16 08:45 etc     # Configuration files
drwxr-xr-x   3 root root  4096 Jan 15 10:30 home    # User home directories
drwxr-xr-x  21 root root  4096 Jan 15 10:30 lib     # Shared libraries
drwx------   2 root root 16384 Jan 15 10:30 lost+found # Recovered files
drwxr-xr-x   2 root root  4096 Jan 15 10:30 media   # Removable media
drwxr-xr-x   2 root root  4096 Jan 15 10:30 mnt     # Mount point
drwxr-xr-x   3 root root  4096 Jan 15 10:30 opt     # Optional software
dr-xr-xr-x 329 root root     0 Jan 16 08:45 proc    # Process information
drwx------   8 root root  4096 Jan 16 08:45 root    # Root user home
drwxr-xr-x  39 root root  1180 Jan 16 08:45 run     # Runtime data
drwxr-xr-x   2 root root 12288 Jan 15 10:30 sbin    # System binaries
drwxr-xr-x   2 root root  4096 Jan 15 10:30 srv     # Service data
dr-xr-xr-x  13 root root     0 Jan 16 08:45 sys     # System information
drwxrwxrwt  20 root root  4096 Jan 16 09:15 tmp     # Temporary files
drwxr-xr-x  14 root root  4096 Jan 15 10:30 usr     # User programs
drwxr-xr-x  19 root root  4096 Jan 15 10:30 var     # Variable data
```

### Critical Directories for DevOps

#### `/etc` - Configuration Files
```bash
# Explore system configuration
ls -la /etc/

# Key configuration files every DevOps engineer should know:
cat /etc/passwd        # User accounts
cat /etc/group         # User groups
cat /etc/hosts         # Host name resolution
cat /etc/hostname      # System hostname
cat /etc/fstab         # File system mount table
cat /etc/crontab       # System cron jobs
ls /etc/systemd/system/ # Systemd service files
ls /etc/nginx/         # Web server configuration (if installed)
ls /etc/ssh/           # SSH configuration
```

#### `/var` - Variable Data (Logs, Databases, Web Content)
```bash
# Critical for monitoring and troubleshooting
ls -la /var/

# Most important subdirectories:
ls -la /var/log/       # System and application logs
ls -la /var/lib/       # Application data (databases, docker images)
ls -la /var/www/       # Web server document root
ls -la /var/spool/     # Print queues, mail queues
ls -la /var/run/       # Runtime process data
ls -la /var/tmp/       # Temporary files that persist across reboots

# Examples of critical log files:
tail -f /var/log/syslog     # System messages
tail -f /var/log/auth.log   # Authentication attempts
tail -f /var/log/apache2/access.log  # Web server access (if installed)
```

#### `/home` - User Home Directories
```bash
# User personal directories
ls -la /home/

# Your own home directory
echo $HOME             # Shows your home directory path
ls -la ~               # ~ is shortcut for home directory
ls -la ~/              # Same as above

# Important hidden files in home directories:
ls -la ~/.bashrc       # Bash configuration
ls -la ~/.ssh/         # SSH keys and configuration
ls -la ~/.profile      # User environment settings
```

#### `/usr` - User Programs and Data
```bash
# User-installed programs and libraries
ls -la /usr/

# Key subdirectories:
ls -la /usr/bin/       # User command binaries
ls -la /usr/sbin/      # System administration binaries
ls -la /usr/lib/       # Libraries for binaries
ls -la /usr/local/     # Locally installed software
ls -la /usr/share/     # Shared data (documentation, etc.)

# Find where commands are located:
which python3          # Shows /usr/bin/python3
which docker           # Shows where docker is installed
whereis nginx          # Shows all related files for nginx
```

#### `/tmp` - Temporary Files
```bash
# Temporary storage - cleared on reboot
ls -la /tmp/

# Anyone can write here, but only owner can delete their files
touch /tmp/test-file
ls -la /tmp/test-file
rm /tmp/test-file

# Check disk usage of tmp (important for system health)
du -sh /tmp/
```

---

## 🧭 Navigation Commands Mastery

### `pwd` - Print Working Directory
```bash
# Always know where you are
pwd

# Use in scripts to get current location
CURRENT_DIR=$(pwd)
echo "Working in: $CURRENT_DIR"

# Useful for troubleshooting scripts
echo "Script running from: $(pwd)"
```

### `cd` - Change Directory
```bash
# Basic navigation
cd /                   # Go to root directory
cd /home              # Go to /home directory
cd ~                  # Go to your home directory
cd                    # Same as cd ~ (go home)
cd ..                 # Go up one directory level
cd ../..              # Go up two directory levels
cd -                  # Go back to previous directory

# Relative vs Absolute paths
pwd                   # Shows current location
cd Documents          # Relative path (from current location)
cd /home/user/Documents  # Absolute path (from root)

# Advanced navigation
cd ~/Documents/Projects  # Go to Projects in your home
cd /var/log && pwd      # Change and show new location
cd /etc; ls -la         # Change directory and list contents
```

### `ls` - List Directory Contents
```bash
# Basic listing
ls                    # List files in current directory
ls /etc               # List files in /etc directory
ls ~                  # List files in home directory

# Detailed listings
ls -l                 # Long format (permissions, owner, size, date)
ls -la                # Long format including hidden files
ls -lh                # Human-readable file sizes
ls -lt                # Sort by modification time (newest first)
ls -lS                # Sort by file size (largest first)
ls -lr                # Reverse order

# Useful combinations for DevOps
ls -la /var/log/      # See all log files with permissions
ls -lh /var/log/      # See log file sizes
ls -lt /var/log/      # See newest log files first
ls -la | grep "^d"    # Show only directories
ls -la | grep "^-"    # Show only regular files

# Color coding (usually enabled by default)
ls --color=always     # Force color output
ls --color=never      # Disable color output

# Wildcards and patterns
ls *.txt              # All .txt files
ls test*              # Files starting with "test"
ls *log*              # Files containing "log"
ls [abc]*             # Files starting with a, b, or c
ls *.{txt,log}        # Files ending with .txt or .log
```

### Advanced Navigation Techniques

#### Using Tab Completion
```bash
# Tab completion saves time and prevents typos
cd /v<TAB>            # Completes to /var/
cd /var/l<TAB>        # Completes to /var/log/
ls /etc/sys<TAB>      # Shows /etc/systemd/ options

# Double-tab to see options
cd /etc/s<TAB><TAB>   # Shows all directories starting with 's'
```

#### Directory Stack (`pushd`, `popd`, `dirs`)
```bash
# Save current directory and change to new one
pushd /var/log        # Save current dir, go to /var/log
pushd /etc            # Save /var/log, go to /etc
pushd /home           # Save /etc, go to /home

# See directory stack
dirs                  # Shows directory stack

# Return to previous directories
popd                  # Return to /etc
popd                  # Return to /var/log
popd                  # Return to original directory
```

---

## 🔍 Finding Files and Directories

### `find` Command - The Swiss Army Knife
```bash
# Basic find syntax: find [path] [criteria] [action]

# Find by name
find / -name "nginx.conf" 2>/dev/null    # Find nginx.conf, hide errors
find /etc -name "*.conf"                 # Find all .conf files in /etc
find ~ -name "*.txt"                     # Find .txt files in home directory
find . -name "*log*"                     # Find files with "log" in name

# Find by type
find /var -type f -name "*.log"          # Find regular files ending in .log
find /etc -type d -name "*ssh*"          # Find directories with "ssh" in name
find /tmp -type l                        # Find symbolic links

# Find by size
find /var/log -type f -size +10M         # Files larger than 10MB
find /tmp -type f -size -1k              # Files smaller than 1KB
find / -type f -size +100M 2>/dev/null   # Large files system-wide

# Find by time
find /var/log -type f -mtime -1          # Modified in last 24 hours
find /tmp -type f -atime +7              # Accessed more than 7 days ago
find /home -type f -ctime -3             # Changed in last 3 days

# Find by permissions
find /etc -type f -perm 644              # Files with exact permissions 644
find /usr/bin -type f -perm -755         # Files with at least 755 permissions
find / -type f -perm /u+s 2>/dev/null    # Find SUID files

# Combine criteria
find /var/log -name "*.log" -size +1M -mtime -7  # Large recent log files
find /etc -type f -name "*.conf" -exec ls -l {} \;  # Find and list .conf files

# Execute commands on found files
find /tmp -name "*.tmp" -delete          # Find and delete .tmp files
find /var/log -name "*.log" -exec wc -l {} \;  # Count lines in log files
find /etc -name "*.conf" -exec grep -l "server" {} \;  # Find conf files containing "server"
```

### `locate` Command - Fast File Finding
```bash
# Update locate database (run as root)
sudo updatedb

# Fast file searching
locate nginx.conf     # Find files named nginx.conf
locate "*.log"        # Find all .log files
locate -i NGINX       # Case-insensitive search

# Limit results
locate -l 10 "*.conf" # Show only first 10 results

# Show statistics
locate -S             # Show database statistics
```

### `which` and `whereis` - Find Commands
```bash
# Find executable location
which python3         # Shows /usr/bin/python3
which docker          # Shows where docker command is located
which -a python       # Show all python executables in PATH

# Find command and related files
whereis python3       # Shows binary, source, and manual locations
whereis nginx         # Shows all nginx-related files
```

---

## 📁 File and Directory Operations

### Creating Directories
```bash
# Create single directory
mkdir test-dir
mkdir /tmp/my-project

# Create directory structure
mkdir -p project/src/main/java    # Create nested directories
mkdir -p logs/{2024,2023}/{01,02,03}  # Create multiple directories

# Create with specific permissions
mkdir -m 755 public-dir           # Create with 755 permissions
mkdir -m 700 private-dir          # Create with 700 permissions

# Verify creation
ls -la test-dir
ls -la project/src/main/java
```

### Copying Files and Directories
```bash
# Copy files
cp source.txt destination.txt    # Copy file
cp /etc/passwd ~/passwd-backup   # Copy to different location
cp -i source.txt dest.txt        # Interactive (ask before overwrite)
cp -v source.txt dest.txt        # Verbose (show what's being copied)

# Copy directories
cp -r source-dir dest-dir        # Recursive copy (directories)
cp -rp source-dir dest-dir       # Preserve permissions and timestamps
cp -ru source-dir dest-dir       # Update only (copy if newer)

# Advanced copying
cp -a source-dir dest-dir        # Archive mode (preserve everything)
cp --backup=numbered file.txt dest/  # Create numbered backups

# Copy multiple files
cp file1.txt file2.txt file3.txt destination-dir/
cp *.txt destination-dir/        # Copy all .txt files
cp {file1,file2,file3}.txt dest/ # Copy specific files
```

### Moving and Renaming
```bash
# Move/rename files
mv old-name.txt new-name.txt     # Rename file
mv file.txt /tmp/                # Move file to /tmp
mv file.txt /tmp/new-name.txt    # Move and rename

# Move directories
mv old-dir new-dir               # Rename directory
mv project-dir /opt/             # Move directory to /opt

# Move multiple files
mv *.log /var/log/               # Move all .log files
mv file1.txt file2.txt dest-dir/ # Move multiple files

# Interactive and verbose
mv -i source dest                # Ask before overwriting
mv -v source dest                # Show what's being moved
```

### Removing Files and Directories
```bash
# Remove files
rm file.txt                      # Remove file
rm -i file.txt                   # Interactive removal
rm -f file.txt                   # Force removal (no prompts)
rm -v file.txt                   # Verbose removal

# Remove multiple files
rm file1.txt file2.txt file3.txt
rm *.tmp                         # Remove all .tmp files
rm -f /tmp/*.log                 # Force remove all .log files in /tmp

# Remove directories
rmdir empty-dir                  # Remove empty directory only
rm -r directory                  # Remove directory and contents
rm -rf directory                 # Force remove directory and contents
rm -ri directory                 # Interactive recursive removal

# Safe removal practices
rm -i *.txt                      # Always use -i for wildcards
ls -la before-removal/           # List contents before removing
rm -rf --preserve-root /         # Protect against accidental root deletion
```

---

## 🎯 Practical Exercises

### Exercise 1: System Exploration
```bash
# Task: Explore and document your system structure
# Complete these commands and note the results:

# 1. Find your current location
pwd

# 2. List root directory contents
ls -la /

# 3. Explore critical directories
ls -la /etc | head -20
ls -la /var/log | head -10
ls -la /usr/bin | head -15

# 4. Find your home directory size
du -sh ~

# 5. Count files in various directories
find /etc -type f | wc -l
find /usr/bin -type f | wc -l
find /var/log -type f | wc -l
```

### Exercise 2: Navigation Practice
```bash
# Task: Practice navigation without using tab completion

# 1. Navigate to root and back home
cd /
pwd
cd
pwd

# 2. Use relative navigation
cd /var
cd log
pwd
cd ../lib
pwd
cd ../../etc
pwd

# 3. Use directory stack
pushd /var/log
pushd /etc
pushd /usr/bin
dirs
popd
popd
popd
```

### Exercise 3: File Operations Lab
```bash
# Task: Create a project structure and practice file operations

# 1. Create project structure
mkdir -p ~/devops-practice/{scripts,configs,logs,backups}
mkdir -p ~/devops-practice/scripts/{bash,python,monitoring}

# 2. Create test files
touch ~/devops-practice/scripts/deploy.sh
touch ~/devops-practice/configs/nginx.conf
touch ~/devops-practice/logs/app.log
echo "#!/bin/bash" > ~/devops-practice/scripts/backup.sh
echo "echo 'Hello DevOps!'" >> ~/devops-practice/scripts/backup.sh

# 3. Practice copying and moving
cp ~/devops-practice/scripts/backup.sh ~/devops-practice/scripts/backup-copy.sh
mv ~/devops-practice/scripts/backup-copy.sh ~/devops-practice/backups/
cp -r ~/devops-practice/scripts ~/devops-practice/scripts-backup

# 4. Verify your work
find ~/devops-practice -type f -name "*.sh"
ls -la ~/devops-practice/backups/
tree ~/devops-practice  # If tree is installed
```

### Exercise 4: Finding Files Challenge
```bash
# Task: Use find command to locate various files

# 1. Find all configuration files
find /etc -name "*.conf" -type f 2>/dev/null | head -10

# 2. Find large log files
find /var/log -size +1M -type f 2>/dev/null

# 3. Find recently modified files
find /tmp -mtime -1 -type f 2>/dev/null

# 4. Find executable files in your home
find ~ -type f -executable 2>/dev/null

# 5. Find and count different file types
find /usr/bin -type f -name "*python*" | wc -l
find /etc -name "*.d" -type d | wc -l
```

---

## 🔧 Troubleshooting Common Issues

### Permission Denied Errors
```bash
# Problem: Cannot access certain directories
ls /root
# ls: cannot open directory '/root': Permission denied

# Solution: Use sudo for system directories
sudo ls /root

# Check your current permissions
id
groups
```

### Path Not Found
```bash
# Problem: Command not found
some-command
# bash: some-command: command not found

# Solutions:
which some-command     # Check if command exists
echo $PATH            # Check PATH variable
whereis some-command  # Find all related files
```

### Too Many Files
```bash
# Problem: ls output is overwhelming
ls /usr/bin
# (hundreds of files scroll by)

# Solutions:
ls /usr/bin | less    # Page through output
ls /usr/bin | head    # Show first 10 files
ls /usr/bin | grep python  # Filter for specific files
ls -1 /usr/bin | wc -l     # Count files
```

---

## 📊 Progress Checkpoint

Test your knowledge with these commands:

```bash
# Navigation mastery check:
# 1. Can you navigate to /var/log/nginx without tab completion?
cd /var/log/nginx

# 2. Can you go back to your previous directory?
cd -

# 3. Can you list all .conf files in /etc recursively?
find /etc -name "*.conf" -type f 2>/dev/null

# 4. Can you create a complex directory structure in one command?
mkdir -p ~/test/{dir1/{subdir1,subdir2},dir2,dir3}

# 5. Can you find the largest file in /var/log?
find /var/log -type f -exec ls -lh {} \; 2>/dev/null | sort -k5 -hr | head -1
```

If you can complete these tasks confidently, you're ready to move to the next module!

---

## ➡️ Next Steps

You're now ready for:
1. **02-Users_Groups_Permissions** - Managing access and security
2. Understanding file ownership and permission systems
3. Creating and managing user accounts

---

📄 **File Path:** `/DevOps/02-Linux_System_Administration/01-Linux_Fundamentals/01-File_System_Navigation/01-Linux_Directory_Structure_And_Navigation.md` 