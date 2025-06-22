# 💻 Linux Command Line Essentials for DevOps

## 📋 Learning Objectives

By completing this guide, you will:
- ✅ Master essential Linux commands for daily DevOps tasks
- ✅ Understand file system navigation and manipulation
- ✅ Learn process management and system monitoring
- ✅ Practice text processing and file editing skills
- ✅ Build confidence in command-line operations

---

## 🎯 Getting Started

### **Open Terminal:**
- **Ubuntu Desktop:** Ctrl + Alt + T
- **Or:** Applications > Terminal
- **Or:** Right-click desktop > Open Terminal

### **Basic Terminal Navigation:**
```bash
# Clear screen
clear

# Show current directory
pwd

# Show command history
history

# Exit terminal
exit
```

---

## 📁 File System Navigation

### **Directory Operations:**
```bash
# List files and directories
ls                    # Basic listing
ls -l                 # Detailed listing (long format)
ls -la                # Include hidden files
ls -lh                # Human-readable file sizes
ls -lt                # Sort by modification time

# Change directory
cd /home              # Go to /home directory
cd ~                  # Go to home directory
cd ..                 # Go up one directory
cd ../..              # Go up two directories
cd -                  # Go back to previous directory

# Show current location
pwd                   # Print working directory

# Create directories
mkdir my-project      # Create single directory
mkdir -p project/src/main   # Create nested directories
mkdir dir1 dir2 dir3  # Create multiple directories

# Remove directories
rmdir empty-dir       # Remove empty directory
rm -r directory       # Remove directory and contents
rm -rf directory      # Force remove (be careful!)
```

### **File Operations:**
```bash
# Create files
touch file.txt        # Create empty file
touch file1.txt file2.txt  # Create multiple files

# Copy files and directories
cp file.txt backup.txt     # Copy file
cp -r source/ destination/ # Copy directory recursively

# Move/rename files
mv old-name.txt new-name.txt  # Rename file
mv file.txt /path/to/new/location/  # Move file

# Remove files
rm file.txt           # Remove file
rm *.txt              # Remove all .txt files
rm -i file.txt        # Interactive removal (ask confirmation)

# Create symbolic links
ln -s /path/to/file link-name   # Create symbolic link
```

---

## 📄 Viewing and Editing Files

### **File Content Viewing:**
```bash
# View entire file
cat file.txt          # Display entire file
less file.txt         # View file page by page (q to quit)
more file.txt         # Similar to less
head file.txt         # Show first 10 lines
head -n 20 file.txt   # Show first 20 lines
tail file.txt         # Show last 10 lines
tail -n 5 file.txt    # Show last 5 lines
tail -f logfile.txt   # Follow file changes (useful for logs)
```

### **Text Editors:**
```bash
# Nano (beginner-friendly)
nano file.txt         # Open file in nano
# Ctrl+O to save, Ctrl+X to exit

# Vim (powerful but complex)
vim file.txt          # Open file in vim
# Press 'i' to insert, 'Esc' to command mode, ':wq' to save and quit

# Create file with content
cat > newfile.txt << EOF
Line 1 of content
Line 2 of content
EOF
```

---

## 🔍 Search and Filter

### **Finding Files:**
```bash
# Find files by name
find . -name "*.txt"          # Find all .txt files in current directory
find /home -name "config*"    # Find files starting with "config"
find . -type f -size +1M      # Find files larger than 1MB
find . -type d -name "log*"   # Find directories starting with "log"

# Locate files (faster, uses database)
locate filename.txt           # Quick file search
updatedb                      # Update locate database (as root)

# Which command shows location
which python3                 # Show location of python3 command
whereis git                   # Show locations of git binary, source, manual
```

### **Text Search:**
```bash
# Search inside files
grep "error" logfile.txt      # Find lines containing "error"
grep -i "ERROR" logfile.txt   # Case-insensitive search
grep -r "TODO" .              # Recursively search in all files
grep -n "function" script.py  # Show line numbers
grep -v "debug" logfile.txt   # Show lines NOT containing "debug"

# Advanced text processing
awk '{print $1}' file.txt     # Print first column
sed 's/old/new/g' file.txt    # Replace "old" with "new"
sort file.txt                 # Sort lines alphabetically
uniq file.txt                 # Remove duplicate lines
wc -l file.txt                # Count lines in file
```

---

## ⚙️ Process Management

### **Viewing Processes:**
```bash
# List running processes
ps                    # Show processes for current user
ps aux                # Show all processes with details
ps -ef                # Alternative format
top                   # Real-time process monitor
htop                  # Enhanced process monitor (if installed)
```

### **Process Control:**
```bash
# Start processes
command &             # Run command in background
nohup command &       # Run command immune to hangups

# Control running processes
Ctrl+C                # Stop current process
Ctrl+Z                # Suspend current process
jobs                  # List active jobs
fg                    # Bring job to foreground
bg                    # Send job to background

# Kill processes
kill PID              # Terminate process by ID
kill -9 PID           # Force kill process
killall process-name  # Kill all processes by name
pkill -f "pattern"    # Kill processes matching pattern
```

---

## 🔧 System Information

### **System Status:**
```bash
# System information
uname -a              # System information
hostname              # Computer name
whoami                # Current username
id                    # User and group IDs
uptime                # System uptime and load

# Hardware information
lscpu                 # CPU information
free -h               # Memory usage
df -h                 # Disk space usage
du -sh directory/     # Directory size
lsblk                 # Block devices (disks)
```

### **Network Information:**
```bash
# Network commands
ip addr show          # Show IP addresses
ping google.com       # Test connectivity
curl ifconfig.me      # Show public IP
netstat -tuln         # Show listening ports
ss -tuln              # Modern alternative to netstat
```

---

## 🔐 Permissions and Ownership

### **File Permissions:**
```bash
# View permissions
ls -l file.txt        # Show detailed file information

# Permission format: drwxrwxrwx
# d = directory, - = file
# rwx = read, write, execute for owner, group, others

# Change permissions
chmod 755 file.txt    # rwxr-xr-x
chmod +x script.sh    # Add execute permission
chmod -w file.txt     # Remove write permission
chmod u+x,g+r file.txt # Add execute for user, read for group

# Change ownership
chown user:group file.txt     # Change owner and group
chown user file.txt           # Change owner only
chgrp group file.txt          # Change group only
```

### **Understanding Permission Numbers:**
```
7 = rwx (read + write + execute)
6 = rw- (read + write)
5 = r-x (read + execute)
4 = r-- (read only)
0 = --- (no permissions)

Common combinations:
755 = rwxr-xr-x (executable files)
644 = rw-r--r-- (regular files)
600 = rw------- (private files)
```

---

## 🔗 Command Chaining and Redirection

### **Pipes and Redirection:**
```bash
# Redirect output
command > file.txt         # Redirect stdout to file (overwrite)
command >> file.txt        # Redirect stdout to file (append)
command 2> error.log       # Redirect stderr to file
command &> all_output.txt  # Redirect both stdout and stderr

# Pipes
ps aux | grep python       # Find python processes
cat file.txt | sort        # Sort file contents
ls -l | wc -l             # Count files in directory
```

### **Command Chaining:**
```bash
# Chain commands
command1 && command2       # Run command2 if command1 succeeds
command1 || command2       # Run command2 if command1 fails
command1; command2         # Run both commands sequentially
```

---

## 📦 Package Management (Ubuntu/Debian)

### **APT Package Manager:**
```bash
# Update package lists
sudo apt update

# Upgrade installed packages
sudo apt upgrade

# Install packages
sudo apt install package-name
sudo apt install -y package-name    # Skip confirmation

# Remove packages
sudo apt remove package-name
sudo apt purge package-name          # Remove with config files

# Search packages
apt search keyword
apt list --installed | grep keyword

# Show package information
apt show package-name
```

---

## 🔒 User and Service Management

### **User Operations:**
```bash
# User information
who                   # Show logged in users
w                     # Show user activity
last                  # Show login history

# Switch users
su username           # Switch to user
sudo command          # Run command as root
sudo -u user command  # Run command as specific user
```

### **Service Management (systemd):**
```bash
# Service operations
sudo systemctl start service-name
sudo systemctl stop service-name
sudo systemctl restart service-name
sudo systemctl enable service-name    # Start at boot
sudo systemctl disable service-name   # Don't start at boot
sudo systemctl status service-name    # Check service status

# View logs
journalctl -u service-name           # Service logs
journalctl -f                        # Follow system logs
```

---

## 🌐 Environment and Variables

### **Environment Variables:**
```bash
# View environment variables
env                   # Show all environment variables
echo $HOME            # Show HOME variable
echo $PATH            # Show PATH variable
printenv USER         # Show specific variable

# Set variables
export VAR_NAME=value        # Set environment variable
VAR_NAME=value command       # Set for single command
unset VAR_NAME               # Remove variable

# Add to PATH permanently
echo 'export PATH=$PATH:/new/path' >> ~/.bashrc
source ~/.bashrc             # Reload bash configuration
```

---

## 📚 Command Line Tips and Tricks

### **Productivity Shortcuts:**
```bash
# Navigation shortcuts
Ctrl+A               # Go to beginning of line
Ctrl+E               # Go to end of line
Ctrl+U               # Delete from cursor to beginning
Ctrl+K               # Delete from cursor to end
Ctrl+W               # Delete previous word
Ctrl+R               # Search command history

# Tab completion
ls /ho<TAB>          # Auto-complete paths
systemctl st<TAB>    # Auto-complete commands

# Command history
!!                   # Repeat last command
!n                   # Repeat command number n from history
!string              # Repeat last command starting with string
```

### **Useful Aliases (Add to ~/.bashrc):**
```bash
alias ll='ls -alF'
alias la='ls -A'
alias l='ls -CF'
alias ..='cd ..'
alias ...='cd ../..'
alias grep='grep --color=auto'
alias ports='netstat -tulanp'
alias df='df -h'
alias free='free -h'
alias psg='ps aux | grep'
```

---

## 🧪 Practice Exercises

### **Exercise 1: File Management**
```bash
# Create a project structure
mkdir -p ~/practice/{documents,scripts,backups}
cd ~/practice

# Create some files
touch documents/readme.txt
echo "Hello DevOps" > documents/greeting.txt
echo "#!/bin/bash" > scripts/deploy.sh

# Practice copying and moving
cp documents/greeting.txt backups/
mv scripts/deploy.sh scripts/deployment.sh

# List everything
find . -type f -exec ls -l {} \;
```

### **Exercise 2: Text Processing**
```bash
# Create a log file simulation
cat > sample.log << EOF
2024-01-01 10:00:00 INFO Application started
2024-01-01 10:01:00 DEBUG Loading configuration
2024-01-01 10:02:00 ERROR Failed to connect to database
2024-01-01 10:03:00 INFO Retrying connection
2024-01-01 10:04:00 INFO Database connected successfully
EOF

# Practice text processing
grep "ERROR" sample.log
grep -c "INFO" sample.log
awk '{print $3, $4}' sample.log
head -3 sample.log
```

### **Exercise 3: Process Management**
```bash
# Start a long-running process
sleep 300 &
echo $!                    # Note the process ID

# View and manage processes
jobs
ps aux | grep sleep
kill %1                    # Kill by job number
# or: kill [PID]           # Kill by process ID
```

---

## 🚨 Common Troubleshooting Commands

### **When Things Go Wrong:**
```bash
# Disk space issues
df -h                      # Check disk space
du -sh * | sort -hr        # Find large directories
find . -size +100M         # Find large files

# Permission issues
ls -la file.txt            # Check file permissions
sudo chown $USER:$USER file.txt  # Fix ownership

# Process issues
ps aux | grep process-name
kill -9 PID                # Force kill stuck process
killall process-name       # Kill all instances

# Network issues
ping 8.8.8.8               # Test internet connectivity
curl -I website.com        # Test website accessibility
netstat -tuln | grep :80   # Check if port 80 is open
```

---

## 🎯 Next Steps

Now that you've learned command line essentials:

1. **Practice daily** - Use terminal for routine tasks
2. **Create scripts** - Automate repetitive commands
3. **Learn text editors** - Master nano, vim, or VS Code
4. **Explore advanced topics** - Shell scripting, regular expressions
5. **Join the community** - Ask questions, share experiences

The command line is your most powerful DevOps tool - master it!

---

## 📖 Additional Resources

### **Command References:**
- Linux Command Line Cheat Sheet
- Bash Manual: `man bash`
- Command help: `command --help`

### **Online Learning:**
- Linux Journey (linuxjourney.com)
- OverTheWire Bandit (for practice)
- ExplainShell.com (command explanations)

---

📄 **File Path:** `/DevOps/01-Fundamentals_And_Environment_Setup/02-Development_Environment_Setup/Terminal_And_Command_Line_Basics/Linux_Command_Essentials.md` 