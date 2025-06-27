# Navigation and File Operations: Your Linux Command Toolkit

## 📖 What This File Does
Master the essential Linux commands for navigating and managing files - the foundation skills you'll use every single day as a DevOps engineer. This guide provides hands-on practice with real examples and exercises.

## 🎯 Learning Objectives
- Navigate the Linux file system confidently
- Create, copy, move, and delete files and directories
- Understand absolute vs relative paths
- Use wildcards and patterns effectively
- Organize files and directories logically
- Troubleshoot common navigation issues

## 📋 Prerequisites
- Linux system installed and running (WSL2, VM, or native)
- Terminal access
- Basic familiarity with opening a terminal
- No prior Linux experience required!

## 🎯 **IMPORTANT: Using Real Linux Commands in Cursor/VS Code on Windows**

### **If You're on Windows with Cursor/VS Code:**

#### **The Problem:**
By default, Cursor's terminal on Windows gives you Windows commands:
```cmd
C:\Users\You> dir              # Windows command (NOT Linux)
C:\Users\You> type file.txt    # Windows command (NOT Linux)
```

#### **The Solution:**
Set up WSL2 + Cursor to get **real Linux commands**:

```bash
# Step 1: Install WSL2 (if not done already)
# In PowerShell as Administrator:
wsl --install

# Step 2: Open Linux folder in Cursor
# Method A: From Windows File Explorer
# Navigate to: \\wsl$\Ubuntu\home\username\
# Right-click → "Open with Cursor"

# Method B: From Cursor
# File → Open Folder → \\wsl$\Ubuntu\home\username\

# Step 3: Verify you have real Linux in terminal
# Open Cursor terminal (Ctrl+Shift+`)
# You should see Linux prompt like: username@computer:~$
whoami                         # Shows your Linux username
uname -a                       # Shows Linux kernel info
ls -la                         # Works! (not 'dir')
```

#### **Alternative: Change Cursor Terminal Default**
```bash
# In Cursor:
# 1. Ctrl+Shift+P (command palette)
# 2. Type: "Terminal: Select Default Profile"
# 3. Choose "WSL2" or "Ubuntu"
# 4. All new terminals will be Linux by default
```

#### **Verify You Have Real Linux:**
```bash
# Before starting this guide, make sure these work:
ls -la                         # Should work (not 'dir')
which bash                     # Should show: /bin/bash
uname -a                       # Should show Linux kernel info
sudo whoami                    # Should show: root (if you have sudo)
```

**✅ Now all the Linux commands in this guide will work exactly as written!**

---

## 🧭 Navigation Commands: Finding Your Way

### **Where Am I? The `pwd` Command**
```bash
# pwd = Print Working Directory
# This shows your current location in the file system

pwd

# Example output:
# /home/username
```

**🎯 Try This Right Now:**
1. Open your terminal
2. Type `pwd` and press Enter
3. Note the output - this is your current location

### **What's Here? The `ls` Command**
```bash
# ls = List directory contents
# Shows files and folders in current location

ls                    # Basic listing
ls -l                 # Long format (detailed)
ls -a                 # Show hidden files (starting with .)
ls -la                # Long format + hidden files
ls -lh                # Human-readable file sizes
ls -lt                # Sort by modification time
```

**🎯 Practice Session:**
```bash
# Try each of these commands and observe the differences:
ls
ls -l
ls -a
ls -la
ls -lh

# What do you notice about the different outputs?
```

**Understanding `ls -la` Output:**
```
drwxr-xr-x  2 user group 4096 Jan 15 10:30 Documents
-rw-r--r--  1 user group  123 Jan 15 09:15 file.txt
```
- First character: `d` = directory, `-` = file
- Next 9 characters: permissions (we'll learn this later)
- Numbers: links, owner, group, size, date, name

### **Going Places: The `cd` Command**
```bash
# cd = Change Directory
# Move to different locations in the file system

cd /                  # Go to root directory
cd ~                  # Go to home directory
cd                    # Also goes to home (shortcut)
cd ..                 # Go up one level (parent directory)
cd ../..              # Go up two levels
cd Documents          # Go to Documents folder (relative path)
cd /home/username     # Go to specific location (absolute path)
cd -                  # Go back to previous directory
```

**🎯 Navigation Exercise:**
```bash
# Let's take a tour of your system!
# Type each command and observe where you end up:

pwd                   # Note starting location
cd /                  # Go to root
pwd                   # See where you are now
ls                    # See what's in root directory
cd home               # Go to home directory
ls                    # See user directories
cd username           # Replace 'username' with your actual username
pwd                   # Confirm you're in your home directory
cd Documents          # Go to Documents (if it exists)
cd ..                 # Go back to home
cd -                  # Go back to Documents
cd ~                  # Go home using shortcut
```

---

## 📁 File and Directory Operations

### **Creating Things: `mkdir` and `touch`**
```bash
# mkdir = Make Directory
mkdir my-folder                    # Create single directory
mkdir folder1 folder2 folder3     # Create multiple directories
mkdir -p path/to/nested/folders    # Create nested directories (-p = parents)

# touch = Create empty file
touch file.txt                    # Create single file
touch file1.txt file2.txt         # Create multiple files
```

**🎯 Practice Creating:**
```bash
# Create a practice workspace
mkdir -p ~/practice/projects/web
mkdir -p ~/practice/projects/mobile
mkdir -p ~/practice/scripts
mkdir -p ~/practice/notes

# Navigate to your practice area
cd ~/practice

# Create some files
touch notes/todo.txt
touch notes/ideas.txt
touch scripts/backup.sh

# Verify your structure
ls -la
ls -la projects/
ls -la notes/
ls -la scripts/
```

### **Copying Things: The `cp` Command**
```bash
# cp = Copy files or directories
cp source.txt destination.txt     # Copy file
cp source.txt /path/to/dest/       # Copy file to directory
cp -r source-dir dest-dir          # Copy directory recursively
cp *.txt backup/                   # Copy all .txt files to backup/
cp file.txt file.txt.backup        # Create backup copy
```

**🎯 Copy Practice:**
```bash
# Go to your practice area
cd ~/practice

# Create a test file with content
echo "This is my first Linux file!" > test.txt

# Practice copying
cp test.txt test-copy.txt          # Copy file
cp test.txt notes/                 # Copy to notes directory
cp -r notes/ notes-backup/         # Copy entire directory

# Verify copies worked
ls -la
ls -la notes/
ls -la notes-backup/
cat test-copy.txt                  # View copied content
```

### **Moving and Renaming: The `mv` Command**
```bash
# mv = Move or rename files/directories
mv oldname.txt newname.txt         # Rename file
mv file.txt /path/to/destination/  # Move file
mv directory/ /new/location/       # Move directory
mv *.txt documents/                # Move all .txt files
```

**🎯 Move Practice:**
```bash
# In your practice area
cd ~/practice

# Create test files
touch temp1.txt temp2.txt temp3.txt

# Practice renaming
mv temp1.txt renamed-file.txt

# Practice moving
mv temp2.txt notes/
mv temp3.txt scripts/

# Verify moves
ls -la
ls -la notes/
ls -la scripts/
```

### **Removing Things: The `rm` Command**
```bash
# rm = Remove files and directories
rm file.txt                       # Remove file
rm -i file.txt                    # Interactive removal (asks confirmation)
rm *.txt                          # Remove all .txt files
rm -r directory/                  # Remove directory and contents
rm -rf directory/                 # Force remove (be careful!)
rmdir empty-directory/            # Remove empty directory only
```

**⚠️ Safety First!**
```bash
# ALWAYS be careful with rm command!
# These commands are DANGEROUS:
# rm -rf /          # DON'T DO THIS! (Deletes entire system)
# rm -rf *          # DON'T DO THIS! (Deletes everything in current directory)

# Safe practices:
ls file.txt                       # Check file exists first
rm -i file.txt                    # Use interactive mode
rm file.txt                       # Then remove
```

**🎯 Safe Removal Practice:**
```bash
# Create test files for deletion practice
cd ~/practice
touch delete-me.txt delete-me-too.txt

# Safe removal practice
ls delete-me*                     # See what will be deleted
rm -i delete-me.txt               # Interactive removal (type 'y' to confirm)
rm delete-me-too.txt              # Direct removal

# Verify deletion
ls delete-me*                     # Should show "No such file"
```

---

## 🔒 File Permissions and Ownership

### **Understanding Linux Permissions**

Every file and directory in Linux has **three types of permissions** for **three types of users**:

**🎯 Permission Types:**
- **r** = Read (view file contents or list directory)
- **w** = Write (modify file or create/delete files in directory) 
- **x** = Execute (run file as program or enter directory)

**👥 User Types:**
- **Owner** = The user who owns the file
- **Group** = Users in the same group as the file
- **Others** = Everyone else on the system

### **Reading Permission Display**
```bash
# Use ls -l to see permissions
ls -l file.txt
# Output: -rw-r--r-- 1 user group 1024 Oct 15 10:30 file.txt
#         ^^^^ ^^^^ ^^^^
#         |    |    |
#         |    |    └── Others permissions
#         |    └── Group permissions  
#         └── Owner permissions

# Breaking down -rw-r--r--:
# Position 1: - = regular file (d = directory, l = link)
# Positions 2-4: rw- = owner can read, write, not execute
# Positions 5-7: r-- = group can read only
# Positions 8-10: r-- = others can read only
```

**🎯 Permission Examples:**
```bash
# Common permission patterns
ls -l examples/
-rw-r--r--  file.txt         # Regular file: owner read/write, others read
-rwxr-xr-x  script.sh        # Executable: owner full, others read/execute
drwxr-xr-x  directory/       # Directory: owner full, others read/enter
-rw-------  secret.txt       # Private: only owner can read/write
-rwxrwxrwx  public.sh        # Public: everyone can do everything
```

### **The `chmod` Command - Changing Permissions**

#### **Symbolic Method (Easier to Understand):**
```bash
# chmod [who][operation][permission] filename
# who: u=user/owner, g=group, o=others, a=all
# operation: +=add, -=remove, ==set exactly
# permission: r=read, w=write, x=execute

# Add execute permission for owner
chmod u+x script.sh

# Remove write permission for group and others
chmod go-w file.txt

# Give everyone read and execute permission
chmod a+rx script.sh

# Set exact permissions: owner read/write, others read only
chmod u=rw,go=r file.txt

# Make file executable for everyone
chmod +x script.sh           # Shortcut for a+x
```

**🎯 Symbolic chmod Practice:**
```bash
# Create test files
cd ~/practice
touch test-file.txt
echo "#!/bin/bash" > test-script.sh
echo "echo 'Hello World'" >> test-script.sh

# Check initial permissions
ls -l test*

# Make script executable
chmod +x test-script.sh
ls -l test-script.sh         # Should show x permissions

# Test the script
./test-script.sh             # Should print "Hello World"

# Practice different permissions
chmod u+w,g+r,o-r test-file.txt    # Complex permission change
chmod a=r test-file.txt             # Set exact permissions for all
ls -l test*                         # Check results
```

#### **Numeric Method (Advanced but Common):**
```bash
# Permissions as numbers:
# r = 4, w = 2, x = 1
# Add numbers together for each user type

# Common numeric permissions:
chmod 755 script.sh          # rwxr-xr-x (owner: 7=4+2+1, others: 5=4+1)
chmod 644 file.txt           # rw-r--r-- (owner: 6=4+2, others: 4=read only)
chmod 600 private.txt        # rw------- (owner only: 6=4+2)
chmod 777 public.sh          # rwxrwxrwx (everyone: 7=4+2+1)
chmod 700 secure-script.sh   # rwx------ (owner only, full access)
```

**🔢 Numeric Permission Calculator:**
```bash
# Calculate permissions:
# Owner permissions:  4(r) + 2(w) + 1(x) = ?
# Group permissions:  4(r) + 0(w) + 1(x) = ?  
# Others permissions: 4(r) + 0(w) + 0(x) = ?

# Example: Want owner to read/write/execute, group to read/execute, others to read only
# Owner: 4+2+1 = 7
# Group: 4+0+1 = 5  
# Others: 4+0+0 = 4
# Result: chmod 754 filename
```

**🎯 Numeric chmod Practice:**
```bash
# Create more test files
cd ~/practice
touch document.txt config.conf script.py

# Apply common DevOps permissions
chmod 644 document.txt       # Standard document: rw-r--r--
chmod 600 config.conf        # Config file (private): rw-------
chmod 755 script.py          # Executable script: rwxr-xr-x

# Verify permissions
ls -l document.txt config.conf script.py

# Practice reading permissions
ls -l | grep document        # Should show -rw-r--r--
ls -l | grep config          # Should show -rw-------
ls -l | grep script          # Should show -rwxr-xr-x
```

### **Directory Permissions (Important!)**

Directory permissions work differently:
- **r** = Can list directory contents (`ls`)
- **w** = Can create/delete files in directory
- **x** = Can enter directory (`cd`) and access files

```bash
# Directory permission examples
mkdir test-dir
chmod 755 test-dir           # Standard directory: drwxr-xr-x
# Owner: can list, create/delete, enter
# Others: can list and enter, but not create/delete

chmod 700 test-dir           # Private directory: drwx------
# Only owner can access

chmod 644 test-dir           # Broken directory: drw-r--r--
# Others can list but can't enter! (missing x)
```

**🎯 Directory Permission Practice:**
```bash
# Create directory structure for testing
cd ~/practice
mkdir -p permissions-test/{public,private,shared}
touch permissions-test/public/readme.txt
touch permissions-test/private/secret.txt  
touch permissions-test/shared/team-file.txt

# Set different directory permissions
chmod 755 permissions-test/public/     # Public: everyone can list and enter
chmod 700 permissions-test/private/    # Private: owner only
chmod 750 permissions-test/shared/     # Shared: owner full, group enter/list

# Test permissions
ls -la permissions-test/
ls -la permissions-test/public/        # Should work
ls -la permissions-test/private/       # Should work (you're owner)
```

### **File Ownership: `chown` and `chgrp`**

```bash
# chown = Change owner
# chgrp = Change group
# Note: You usually need sudo for ownership changes

# Change file owner (requires sudo)
sudo chown newuser file.txt

# Change file group  
sudo chgrp newgroup file.txt

# Change both owner and group
sudo chown newuser:newgroup file.txt

# Change ownership recursively
sudo chown -R user:group directory/
```

### **Common DevOps Permission Scenarios**

#### **Web Server Files:**
```bash
# HTML/CSS files (readable by web server)
chmod 644 index.html style.css

# Web server directories  
chmod 755 /var/www/html/

# Web server config files (private)
chmod 600 /etc/nginx/nginx.conf
```

#### **Script Files:**
```bash
# Deployment scripts (executable)
chmod +x deploy.sh
chmod 755 deploy.sh          # Same as above

# Configuration scripts
chmod 700 setup-secrets.sh   # Owner only (contains passwords)
chmod 755 install-tools.sh   # Everyone can execute
```

#### **SSH and Keys:**
```bash
# SSH private keys MUST be private
chmod 600 ~/.ssh/id_rsa

# SSH public keys  
chmod 644 ~/.ssh/id_rsa.pub

# SSH config
chmod 600 ~/.ssh/config

# SSH directory
chmod 700 ~/.ssh/
```

#### **Docker and Container Files:**
```bash
# Dockerfile (readable)
chmod 644 Dockerfile

# Docker build scripts
chmod +x docker-build.sh

# Docker secrets
chmod 600 docker-secrets.env
```

### **Permission Troubleshooting**

#### **"Permission denied" when running script:**
```bash
# Check if script is executable
ls -l script.sh
# If no 'x' permission, add it:
chmod +x script.sh
```

#### **"Permission denied" when accessing directory:**
```bash
# Check directory permissions
ls -ld directory/
# Need 'x' permission to enter:
chmod +x directory/
```

#### **"Permission denied" when editing file:**
```bash
# Check file permissions
ls -l file.txt
# Need 'w' permission to edit:
chmod u+w file.txt
```

**🎯 Troubleshooting Practice:**
```bash
# Create problematic files
cd ~/practice
echo "test content" > broken-file.txt
echo "#!/bin/bash" > broken-script.sh
mkdir broken-dir

# Break the permissions
chmod 000 broken-file.txt    # No permissions at all
chmod 644 broken-script.sh   # Script without execute
chmod 644 broken-dir         # Directory without execute

# Try to use them (these should fail)
cat broken-file.txt          # Permission denied
./broken-script.sh           # Permission denied  
cd broken-dir               # Permission denied

# Fix the permissions
chmod 644 broken-file.txt    # Now readable
chmod +x broken-script.sh    # Now executable
chmod 755 broken-dir         # Now accessible

# Test again (should work now)
cat broken-file.txt
./broken-script.sh
cd broken-dir && pwd && cd ..
```

---

## 🔍 Finding and Viewing Files

### **Finding Files: The `find` Command**
```bash
# find = Search for files and directories
find . -name "filename"           # Find file by name in current directory
find /home -name "*.txt"          # Find all .txt files in /home
find . -type f -name "*.sh"       # Find regular files ending in .sh
find . -type d -name "project*"   # Find directories starting with "project"
find . -size +1M                  # Find files larger than 1MB
```

**🎯 Finding Practice:**
```bash
# In your practice area
cd ~/practice

# Create files to find
touch scripts/deploy.sh
touch scripts/backup.sh
touch notes/meeting-notes.txt
touch projects/web/index.html

# Practice finding
find . -name "*.sh"               # Find shell scripts
find . -name "*.txt"              # Find text files
find . -type d                    # Find directories
find . -name "*backup*"           # Find anything with "backup" in name
```

### **Viewing File Contents**
```bash
# Different ways to view files
cat filename                      # Show entire file
less filename                     # View file page by page (q to quit)
head filename                     # Show first 10 lines
head -n 5 filename                # Show first 5 lines
tail filename                     # Show last 10 lines
tail -n 5 filename                # Show last 5 lines
tail -f filename                  # Follow file changes (useful for logs)
```

**🎯 Viewing Practice:**
```bash
# Create a file with multiple lines
cd ~/practice
cat > sample.txt << 'EOF'
Line 1: This is the first line
Line 2: This is the second line
Line 3: This is the third line
Line 4: This is the fourth line
Line 5: This is the fifth line
Line 6: This is the sixth line
Line 7: This is the seventh line
Line 8: This is the eighth line
Line 9: This is the ninth line
Line 10: This is the tenth line
Line 11: This is the eleventh line
Line 12: This is the twelfth line
EOF

# Practice different viewing methods
cat sample.txt                    # View entire file
head sample.txt                   # First 10 lines
head -n 3 sample.txt              # First 3 lines
tail sample.txt                   # Last 10 lines
tail -n 3 sample.txt              # Last 3 lines
less sample.txt                   # Page by page (press q to quit)
```

---

## 📋 **ESSENTIAL LINUX COMMANDS: What They Actually Do**

### **Text Processing & Analysis Commands**

#### **`grep` - Search and Filter Text**
**What it does:** Finds and displays lines containing specific patterns

```bash
# Basic grep usage - FUNDAMENTAL
grep "error" logfile.txt         # Find lines containing "error"
grep "user123" /var/log/auth.log # Find specific user activity
grep -i "ERROR" file.txt         # Case-insensitive search

# Real DevOps examples
grep "Failed password" /var/log/auth.log     # Find login failures
grep -c "200" access.log                    # Count successful HTTP requests
grep -v "GET" access.log                    # Show everything EXCEPT GET requests
ps aux | grep nginx                         # Find nginx processes (very common!)

# Pattern matching
grep "^Error" file.txt           # Lines starting with "Error"
grep "\.py$" file.txt            # Lines ending with ".py"
grep "[0-9]\{1,3\}\.[0-9]\{1,3\}" file.txt  # Find IP addresses
```

#### **`cat` - Display File Contents**
**What it does:** Shows entire file content, combines files, creates files

```bash
# Basic cat usage
cat file.txt                     # Display entire file
cat file1.txt file2.txt          # Display multiple files in sequence
cat > newfile.txt                # Create new file (type content, Ctrl+D to save)

# Real DevOps examples
cat /etc/passwd                  # View user accounts
cat /proc/version                # Check kernel version
cat /var/log/syslog | tail -50   # View system log (last 50 lines)
cat << 'EOF' > script.sh         # Create script with content
#!/bin/bash
echo "Hello World"
EOF

# Combining with other commands
cat access.log | grep "404"      # Find 404 errors in web logs
cat config.conf | grep -v "^#"   # Show config without comments
```

#### **`wc` - Count Words, Lines, Characters**
**What it does:** Counts lines, words, characters in files (great for analysis)

```bash
# Basic wc usage
wc file.txt                      # Shows lines, words, characters
wc -l file.txt                   # Count lines only (very common)
wc -w file.txt                   # Count words only
wc -c file.txt                   # Count characters only

# Real DevOps examples
wc -l /etc/passwd                # How many user accounts exist
ps aux | wc -l                   # How many processes are running
grep "ERROR" /var/log/app.log | wc -l    # Count error entries
cat access.log | wc -l           # Total number of requests
find /var/log -name "*.log" | wc -l      # Count log files
```

#### **`sort` - Sort Lines of Text**
**What it does:** Arranges lines in alphabetical/numerical order

```bash
# Basic sort usage
sort file.txt                    # Sort alphabetically
sort -n numbers.txt              # Sort numerically
sort -r file.txt                 # Reverse sort
sort -u file.txt                 # Sort and remove duplicates

# Real DevOps examples
sort /etc/passwd                 # Sort users alphabetically
ps aux --sort=-%cpu              # Sort processes by CPU usage
du -sh */ | sort -hr             # Sort directories by size (largest first)
cat access.log | cut -d' ' -f1 | sort | uniq -c | sort -nr  # Top IP addresses
```

#### **`uniq` - Remove Duplicate Lines**
**What it does:** Removes or counts duplicate adjacent lines (must sort first!)

```bash
# Basic uniq usage
sort file.txt | uniq             # Remove duplicates
sort file.txt | uniq -c          # Count occurrences of each line
sort file.txt | uniq -d          # Show only duplicate lines
sort file.txt | uniq -u          # Show only unique lines

# Real DevOps examples
cat access.log | cut -d' ' -f1 | sort | uniq    # Unique IP addresses
ps aux | awk '{print $1}' | sort | uniq         # Unique users running processes
grep "Failed password" /var/log/auth.log | cut -d' ' -f11 | sort | uniq -c  # Failed login attempts by IP
```

#### **`cut` - Extract Columns from Text**
**What it does:** Extracts specific columns/fields from structured text

```bash
# Basic cut usage
cut -d' ' -f1 file.txt           # Extract 1st field (space-delimited)
cut -d':' -f1,3 /etc/passwd      # Extract 1st and 3rd fields (colon-delimited)
cut -c1-10 file.txt              # Extract characters 1-10

# Real DevOps examples
cut -d' ' -f1 /var/log/nginx/access.log | sort | uniq -c  # IP address frequency
cut -d':' -f1 /etc/passwd                # Extract usernames only
ps aux | cut -c1-20,60-          # Extract user and command columns
cat /proc/meminfo | cut -d':' -f2        # Extract memory values
```

### **Advanced Text Processing Commands**

#### **`sed` - Stream Editor (Find and Replace)**
**What it does:** Edits text streams (find/replace, delete lines, insert text)

```bash
# Basic sed usage
sed 's/old/new/' file.txt        # Replace first occurrence of "old" with "new"
sed 's/old/new/g' file.txt       # Replace ALL occurrences (global)
sed '5d' file.txt                # Delete line 5
sed -n '5,10p' file.txt          # Print only lines 5-10

# Real DevOps examples
sed 's/localhost/production.server.com/g' config.conf  # Update config
sed '/^#/d' config.conf          # Remove comment lines
sed 's/ERROR/⚠️ ERROR/g' logfile.txt       # Highlight errors
sed -n '/ERROR/p' logfile.txt    # Extract only error lines (like grep)
ps aux | sed '1d'                # Remove header line from ps output
```

#### **`awk` - Pattern Processing Language**
**What it does:** Processes structured text (like a mini programming language)

```bash
# Basic awk usage
awk '{print $1}' file.txt        # Print first column
awk '{print $1, $3}' file.txt    # Print columns 1 and 3
awk '/pattern/ {print}' file.txt # Print lines matching pattern

# Real DevOps examples
ps aux | awk '{print $1, $11}'   # Show user and command
awk -F: '{print $1}' /etc/passwd # Extract usernames (colon separator)
df -h | awk '$5 > 80 {print}'    # Show filesystems >80% full
awk '{sum+=$1} END {print sum}' numbers.txt  # Sum a column of numbers
netstat -an | awk '/LISTEN/ {print $4}'     # Show listening ports
```

### **System Monitoring Commands**

#### **`ps` - Process Status**
**What it does:** Shows running processes (who's using the system)

```bash
# Basic ps usage
ps                               # Show your processes
ps aux                           # Show ALL processes (most common)
ps -ef                           # Show all processes with parent info
ps -u username                   # Show processes for specific user

# Real DevOps examples
ps aux | grep nginx              # Find web server processes
ps -eo pid,ppid,cmd,cpu,mem      # Custom format for monitoring
ps aux --sort=-%cpu | head -10   # Top CPU users
ps -ef | grep -v "grep"          # Exclude grep from results
```

#### **`top` and `htop` - Live Process Monitor**
**What they do:** Show real-time system usage (like Task Manager)

```bash
# Basic top usage
top                              # Live system monitor
htop                             # Better version of top (if installed)
top -u username                  # Monitor specific user's processes
top -p 1234                      # Monitor specific process ID

# Real DevOps examples - these run interactively:
# - Press 'M' to sort by memory
# - Press 'P' to sort by CPU
# - Press 'k' to kill a process
# - Press 'q' to quit
```

#### **`df` and `du` - Disk Usage**
**What they do:** Show disk space usage (critical for system monitoring)

```bash
# Basic df usage (disk free)
df                               # Show disk usage
df -h                            # Human-readable sizes (GB, MB)
df -h /var/log                   # Check specific directory

# Basic du usage (disk usage)
du directory/                    # Size of directory
du -h directory/                 # Human-readable size
du -sh directory/                # Summary only (total size)

# Real DevOps examples
df -h | grep -v tmpfs            # Exclude temporary filesystems
du -sh */ | sort -hr             # Largest directories first
du -sh /var/log/*                # Size of each log directory
df -h | awk '$5 > 80 {print}'    # Alert on >80% disk usage
```

### **Network and Connectivity Commands**

#### **`ping` - Test Network Connectivity**
**What it does:** Tests if you can reach another computer

```bash
# Basic ping usage
ping google.com                  # Test internet connectivity
ping -c 4 server.com             # Send only 4 packets
ping 192.168.1.1                 # Test local network

# Real DevOps examples
ping -c 1 database.server.com && echo "DB reachable" || echo "DB down"
ping -c 3 8.8.8.8                # Test DNS resolution
```

#### **`curl` - Transfer Data from Servers**
**What it does:** Downloads web content, tests APIs, uploads files

```bash
# Basic curl usage
curl https://api.example.com     # Get web page/API response
curl -o file.zip https://example.com/file.zip  # Download file
curl -I https://example.com      # Get headers only

# Real DevOps examples
curl -f -s https://api.health.com/status || echo "Service down"  # Health check
curl -X POST -d '{"key":"value"}' -H "Content-Type: application/json" https://api.com/data
curl -u user:pass https://api.com/secure  # API with authentication
```

#### **`wget` - Download Files**
**What it does:** Downloads files from the internet

```bash
# Basic wget usage
wget https://example.com/file.zip    # Download file
wget -O newname.zip https://example.com/file.zip  # Download with new name
wget -r https://example.com/         # Download entire website (recursive)

# Real DevOps examples
wget https://get.docker.com/ -O - | bash  # Download and run install script
wget --spider -q https://example.com && echo "Site up" || echo "Site down"  # Check if site exists
```

### **Archive and Compression Commands**

#### **`tar` - Archive Files**
**What it does:** Bundles files into archives (like ZIP but for Linux)

```bash
# Basic tar usage
tar -cf archive.tar files/       # Create archive
tar -xf archive.tar              # Extract archive
tar -tf archive.tar              # List archive contents

# With compression (very common)
tar -czf backup.tar.gz /data/    # Create compressed backup
tar -xzf backup.tar.gz           # Extract compressed archive
tar -tzf backup.tar.gz           # List compressed archive

# Real DevOps examples
tar -czf logs-$(date +%Y%m%d).tar.gz /var/log/  # Backup logs with date
tar -xzf deployment.tar.gz -C /opt/              # Extract to specific directory
```

#### **`gzip` and `gunzip` - Compress Files**
**What they do:** Compress individual files to save space

```bash
# Basic gzip usage
gzip largefile.txt               # Compress file (creates largefile.txt.gz)
gunzip largefile.txt.gz          # Decompress file
gzip -d largefile.txt.gz         # Alternative decompress

# Real DevOps examples
gzip /var/log/*.log              # Compress all log files
find /var/log -name "*.log" -mtime +7 -exec gzip {} \;  # Compress old logs
```

### **Process Management Commands**

#### **`kill` and `killall` - Stop Processes**
**What they do:** Terminate running processes

```bash
# Basic kill usage
kill 1234                        # Stop process with ID 1234
kill -9 1234                     # Force kill process (last resort)
killall nginx                    # Kill all nginx processes

# Real DevOps examples
ps aux | grep nginx | awk '{print $2}' | xargs kill  # Kill all nginx processes
sudo killall -9 hung_process     # Force kill hung processes
```

#### **`nohup` and `&` - Background Processes**
**What they do:** Run commands in background or survive logout

```bash
# Basic background usage
command &                        # Run in background
nohup long_script.sh &           # Run in background, survive logout
nohup python app.py > app.log 2>&1 &  # Background with logging

# Real DevOps examples
nohup ./deploy.sh > deploy.log 2>&1 &    # Long deployment in background
python -m http.server 8000 &             # Quick web server in background
```

---

## 🎯 Hands-On Project: File Organization System

### **Project: Create Your DevOps Workspace**
```bash
# Create a comprehensive workspace structure
cd ~
mkdir -p devops-workspace/{projects,scripts,configs,logs,backups}
mkdir -p devops-workspace/projects/{web-apps,mobile-apps,infrastructure}
mkdir -p devops-workspace/scripts/{deployment,monitoring,backup}
mkdir -p devops-workspace/configs/{nginx,docker,kubernetes}

# Navigate to your workspace
cd devops-workspace

# Create some sample files
echo "# DevOps Project Ideas" > projects/project-ideas.md
echo "# Daily Tasks" > daily-tasks.txt
echo "#!/bin/bash" > scripts/deployment/deploy.sh
echo "# System Health Check" > scripts/monitoring/health-check.sh

# Make scripts executable (now you understand what this does!)
chmod +x scripts/deployment/deploy.sh
chmod +x scripts/monitoring/health-check.sh

# Verify the scripts are now executable
ls -l scripts/deployment/deploy.sh
ls -l scripts/monitoring/health-check.sh

# View your structure
find . -type f                    # Show all files
find . -type d                    # Show all directories
```

### **Project: File Management Practice**
```bash
# Practice copying and organizing
cd ~/devops-workspace

# Create backup of important files
cp -r projects/ backups/projects-backup-$(date +%Y%m%d)
cp daily-tasks.txt backups/

# Create some log files to practice with
echo "$(date): System started" > logs/system.log
echo "$(date): Application deployed" >> logs/system.log
echo "$(date): Backup completed" >> logs/system.log

# Practice viewing logs
tail logs/system.log
cat logs/system.log

# Find specific files
find . -name "*.sh"
find . -name "*.md"
find . -name "*backup*"
```

---

## 🔧 Advanced Navigation Techniques

### **Wildcards and Patterns**
```bash
# * = matches any characters
ls *.txt                          # All files ending in .txt
ls file*                          # All files starting with "file"
ls *backup*                       # All files containing "backup"

# ? = matches single character
ls file?.txt                      # file1.txt, file2.txt, etc.
ls ???.txt                        # Any 3-character name + .txt

# [] = matches any character in brackets
ls file[123].txt                  # file1.txt, file2.txt, file3.txt
ls file[a-z].txt                  # filea.txt, fileb.txt, etc.

# {} = matches any of the comma-separated patterns
ls *.{txt,md,sh}                  # Files ending in .txt, .md, or .sh
```

**🎯 Wildcard Practice:**
```bash
# Create test files
cd ~/practice
touch file1.txt file2.txt file3.txt
touch document.md readme.md
touch script.sh backup.sh

# Practice wildcards
ls *.txt                          # Text files
ls file?.txt                      # Numbered files
ls *.{txt,md}                     # Text and markdown files
ls *script*                       # Files with "script" in name
```

### **Absolute vs Relative Paths**
```bash
# Absolute paths start with / (from root)
cd /home/username/Documents       # Absolute path
ls /etc/passwd                    # Absolute path

# Relative paths don't start with / (from current location)
cd Documents                      # Relative path
ls ../Downloads                   # Relative path (parent directory)
ls ./file.txt                     # Relative path (current directory)
```

**🎯 Path Practice:**
```bash
# Practice understanding paths
pwd                               # See current location
cd /                              # Go to root (absolute)
pwd                               # Confirm location
cd home                           # Relative from root
pwd                               # See where you are
cd username                       # Replace with your username
pwd                               # Should be in home directory
cd ./Documents                    # Relative path with ./
cd ../                            # Go back to home
pwd                               # Confirm location
```

---

## 🔧 Troubleshooting Common Issues

### **"No such file or directory"**
```bash
# Check if file/directory exists
ls -la filename
ls -la directory/

# Check current location
pwd

# Use absolute path if needed
ls /full/path/to/file

# Check spelling and case sensitivity
ls File.txt vs ls file.txt        # Linux is case-sensitive!
```

### **"Permission denied"**
```bash
# Check permissions
ls -la filename

# For directories, you need execute permission to enter
ls -la directory/

# Use sudo if needed (for system files)
sudo ls /root/
```

### **"Command not found"**
```bash
# Check if you're using the right command
which ls                          # Should show /bin/ls
man ls                           # Read manual

# Check for typos
ls vs lls                        # Common typo
```

---

## 🔤 **Understanding Terminal Symbols and Special Characters**

### **Command Prompt Symbols**

#### **$ - Regular User Prompt**
```bash
# The $ symbol indicates you're logged in as a regular user
username@computer:~$ ls -la
username@computer:~$ pwd
username@computer:~$ whoami

# What $ means:
# ✅ Regular user privileges
# ✅ Can access your files and home directory
# ❌ Cannot modify system files
# ❌ Cannot install software (need sudo)
```

#### **# - Root/Administrator Prompt**
```bash
# The # symbol indicates you're logged in as root (administrator)
root@computer:~# apt install nginx
root@computer:~# systemctl start nginx
root@computer:~# chmod 755 /etc/

# What # means:
# ✅ Full system privileges
# ✅ Can modify any file
# ✅ Can install software
# ⚠️ DANGEROUS - can break system
```

#### **% - Alternative User Prompt (Zsh shell)**
```bash
# Some shells (like Zsh) use % instead of $
username@computer ~ % ls -la
username@computer ~ % cd Documents

# What % means:
# ✅ Same as $ - regular user
# ✅ Just a different shell (Zsh vs Bash)
# ✅ All same commands work
```

### **Command Line Special Characters**

#### **& - Background Processes**
```bash
# & runs commands in the background
python long-running-script.py &      # Runs in background
./deploy.sh &                       # Background deployment
nohup backup.sh &                   # Background + survives logout

# What & does:
# ✅ Command runs in background
# ✅ Terminal is free for other commands
# ✅ Process continues even if terminal closes (with nohup)

# Example usage:
# Start a web server in background
python -m http.server 8000 &
# Terminal is free to use while server runs
ls -la                              # Can run other commands
jobs                                # See background jobs
```

**📖 What This Does**
The `&` symbol tells Linux to run the command in the background, returning control to your terminal immediately. This is essential for long-running processes like web servers, backup scripts, or monitoring tools.

**🔧 Configuration Notes**
- Use `nohup command &` to keep processes running after you log out
- Use `jobs` to see background processes and `fg` to bring them to foreground
- Background processes still send output to terminal unless redirected

#### **&& - Conditional Execution (AND)**
```bash
# && runs second command only if first succeeds
mkdir new-project && cd new-project
apt update && apt upgrade
git add . && git commit -m "update" && git push

# What && means:
# ✅ "AND" - both commands must succeed
# ✅ If first fails, second won't run
# ✅ Chains multiple commands safely

# Example:
ls file.txt && cat file.txt         # Only cat if file exists
```

#### **|| - Conditional Execution (OR)**
```bash
# || runs second command only if first fails
command_that_might_fail || echo "Command failed!"
ping google.com || echo "No internet connection"
docker start container || docker run -it ubuntu

# What || means:
# ✅ "OR" - runs backup command if first fails
# ✅ Error handling and fallbacks
# ✅ Provides alternatives

# Example:
curl -f https://api.com/data || echo "API is down"
```

#### **| - Pipe (Connect Commands)**
```bash
# | connects output of first command to input of second
ls -la | grep ".txt"                # List files, filter for .txt
ps aux | grep nginx                 # List processes, find nginx
cat logfile.log | grep "ERROR" | wc -l  # Count error lines

# What | means:
# ✅ Connects commands together
# ✅ Output of left becomes input of right
# ✅ Creates powerful command chains

# More examples:
history | grep "git"                # Find git commands in history
df -h | sort -k5 -nr               # Disk usage sorted by percentage
```

**📖 What This Does**
The pipe `|` is one of the most powerful Linux concepts. It takes the output from one command and feeds it as input to the next command, allowing you to chain simple commands together to perform complex operations.

**🔧 Configuration Notes**
- Pipes are processed left to right in sequence
- Each command in the pipe runs simultaneously (not one after another)
- If any command in the pipe fails, the pipe continues but results may be incomplete

#### **> and >> - Output Redirection**
```bash
# > redirects output to file (overwrites)
echo "Hello World" > greeting.txt
ls -la > file-list.txt
date > current-time.txt

# >> redirects output to file (appends)
echo "New line" >> greeting.txt
ls /var/log >> all-logs.txt
echo "$(date): Backup completed" >> backup.log

# What > and >> mean:
# > = Overwrite file with output
# >> = Append output to file
# ✅ Save command output to files
# ✅ Create logs and reports
```

**📖 What This Does**
Output redirection captures the text output of commands and saves it to files instead of displaying it on screen. This is essential for creating logs, saving reports, and automating documentation.

**🔧 Configuration Notes**
- `>` completely overwrites the target file (be careful!)
- `>>` safely adds to the end of existing files
- Use `2>` to redirect error messages: `command 2> error.log`
- Use `&>` to redirect both output and errors: `command &> all-output.log`

#### **< - Input Redirection**
```bash
# < redirects file content as input to command
mysql database < backup.sql
wc -l < file.txt                    # Count lines from file
sort < unsorted.txt > sorted.txt    # Sort file contents

# What < means:
# ✅ Feed file content to command
# ✅ Alternative to cat file | command
# ✅ More efficient for large files
```

#### **; - Command Separator**
```bash
# ; separates multiple commands on one line
cd /tmp; ls -la; pwd                # Run three commands in sequence
mkdir test; cd test; touch file.txt # Create, enter, and create file

# What ; means:
# ✅ Runs commands in sequence
# ✅ Each command runs regardless of previous success/failure
# ✅ Different from && (which stops if command fails)

# Example:
date; whoami; pwd; ls               # Multiple info commands
```

#### **~ - Home Directory Shortcut**
```bash
# ~ represents your home directory
cd ~                                # Go to home directory
ls ~/Documents                      # List Documents in home
cp file.txt ~/backup/               # Copy to backup in home

# What ~ means:
# ✅ Shortcut for /home/username
# ✅ Works in any path
# ✅ Faster than typing full path

# Examples:
echo ~                              # Shows: /home/username
ls -la ~/.*                         # Show hidden files in home
```

**📖 What This Does**
The tilde `~` is a shortcut that always represents your home directory (`/home/username`). This makes it easy to reference files in your personal space without typing the full path.

**🔧 Configuration Notes**
- `~` always points to the current user's home directory
- `~username` points to another user's home directory (if you have permission)
- Works in any command that accepts file paths
- Essential for script portability across different users

#### **. and .. - Current and Parent Directory**
```bash
# . represents current directory
cp file.txt .                       # Copy to current directory
./script.sh                         # Run script in current directory
find . -name "*.txt"                # Find txt files starting here

# .. represents parent directory
cd ..                               # Go up one level
cp ../file.txt .                    # Copy from parent to current
ls ../..                            # List grandparent directory

# What . and .. mean:
# . = Current directory (where you are now)
# .. = Parent directory (one level up)
# ✅ Essential for navigation
# ✅ Used in relative paths
```

#### *** and ? - Wildcards**
```bash
# * matches any number of characters
ls *.txt                            # All files ending in .txt
rm temp*                            # Remove files starting with temp
cp /var/log/*.log ./backup/         # Copy all log files

# ? matches single character
ls file?.txt                        # file1.txt, file2.txt, etc.
rm temp?.tmp                        # temp1.tmp, tempA.tmp, etc.

# What * and ? mean:
# * = Match zero or more characters
# ? = Match exactly one character
# ✅ Pattern matching for files
# ✅ Bulk operations
```

**📖 What This Does**
Wildcards are pattern-matching characters that let you work with multiple files at once without typing each filename. They're essential for efficient file management and automation.

**🔧 Configuration Notes**
- `*` matches everything including nothing (zero characters)
- `?` matches exactly one character - no more, no less
- Use `[abc]` to match any single character from the brackets
- Use `[0-9]` for number ranges, `[a-z]` for letter ranges
- Always test wildcard patterns with `ls` before using with `rm`

#### **{} - Brace Expansion**
```bash
# {} creates multiple variations
mkdir {docs,scripts,configs}         # Creates three directories
cp file.txt{,.backup}              # Creates file.txt.backup
echo {1..5}                         # Prints: 1 2 3 4 5
echo {a..z}                         # Prints alphabet

# What {} means:
# ✅ Generates multiple strings
# ✅ Creates sequences
# ✅ Efficient bulk operations

# More examples:
touch file{1..10}.txt               # Creates file1.txt through file10.txt
mkdir project-{dev,test,prod}       # Creates three project directories
```

#### **$() and `` - Command Substitution**
```bash
# $() executes command and uses output
echo "Today is $(date)"
mkdir backup-$(date +%Y%m%d)
echo "You are in $(pwd)"

# `` (backticks) - older syntax, same function
echo "Current user: `whoami`"
echo "Files: `ls | wc -l`"

# What $() and `` mean:
# ✅ Execute command inside
# ✅ Use output as part of larger command
# ✅ Dynamic command building

# Examples:
cp important.txt backup-$(date +%Y%m%d).txt
echo "Disk usage: $(df -h | grep '/$')"
```

**📖 What This Does**
Command substitution executes a command and replaces it with the command's output. This allows you to use the result of one command as part of another command, enabling dynamic and flexible scripts.

**🔧 Configuration Notes**
- `$()` is preferred over backticks `` ` ` `` for better readability and nesting
- The command inside executes first, then its output replaces the substitution
- Can be nested: `$(dirname $(which python))`
- Essential for creating timestamps, getting system info, and building dynamic commands

#### **! - History and Negation**
```bash
# ! accesses command history
!!                                  # Repeat last command
!ls                                 # Repeat last command starting with 'ls'
!123                                # Repeat command number 123
sudo !!                             # Repeat last command with sudo

# ! for negation in find
find . ! -name "*.txt"              # Find files NOT ending in .txt

# What ! means:
# ✅ History expansion
# ✅ Logical negation
# ✅ Quick command repetition

# Examples:
ls -la /etc/passwd                  # Command fails (permission denied)
sudo !!                             # Repeats with sudo: sudo ls -la /etc/passwd
```

### **Environment Variables with $**

#### **$Variable - Variable Expansion**
```bash
# $ accesses environment variables
echo $HOME                          # Shows: /home/username
echo $USER                          # Shows: username
echo $PATH                          # Shows: /usr/bin:/bin:/usr/local/bin...

# Custom variables
NAME="John"
echo "Hello $NAME"                  # Shows: Hello John
echo "Welcome $USER to $HOME"       # Shows: Welcome username to /home/username

# What $Variable means:
# ✅ Access environment variables
# ✅ System configuration
# ✅ Dynamic values in scripts
```

#### **Common Environment Variables**
```bash
# Essential environment variables
echo $HOME                          # User's home directory
echo $USER                          # Current username
echo $PWD                           # Current working directory
echo $PATH                          # Command search paths
echo $SHELL                         # Current shell (/bin/bash)
echo $EDITOR                        # Default text editor

# System information
echo $HOSTNAME                      # Computer name
echo $TERM                          # Terminal type
echo $LANG                          # Language setting
```

### **Process Control Symbols**

#### **Ctrl+C, Ctrl+Z, and Job Control**
```bash
# Ctrl+C - Stop/Kill current command
ping google.com                     # Starts continuous ping
# Press Ctrl+C to stop

# Ctrl+Z - Pause/Suspend current command
./long-running-script.sh
# Press Ctrl+Z to pause
jobs                                # See paused jobs
fg                                  # Bring job to foreground
bg                                  # Send job to background

# What these do:
# Ctrl+C = Terminate process (SIGTERM)
# Ctrl+Z = Suspend process (can resume later)
# fg = Resume in foreground
# bg = Resume in background
```

### **DevOps-Specific Symbol Usage**

#### **Docker and Container Symbols**
```bash
# Docker uses many special characters
docker run -it ubuntu:latest /bin/bash    # Interactive terminal
docker run -d -p 8080:80 nginx           # Background with port mapping
docker exec -it container_name bash       # Execute in running container
docker logs container_name | grep ERROR   # Pipe logs to grep

# Kubernetes
kubectl get pods | grep Running           # Filter running pods
kubectl logs pod-name | tail -f           # Follow logs
```

#### **Git and Version Control**
```bash
# Git operations with symbols
git add . && git commit -m "update" && git push  # Chain git commands
git log --oneline | head -10              # Last 10 commits
git status | grep modified               # Find modified files
```

#### **Log Analysis**
```bash
# Common log analysis patterns
tail -f /var/log/syslog | grep ERROR     # Follow live errors
grep "404" access.log | wc -l            # Count 404 errors
cat error.log | grep "$(date +%Y-%m-%d)" # Today's errors
```

### **🎯 Symbol Quick Reference**

| Symbol | Name | Usage | Example |
|--------|------|-------|---------|
| `$` | User prompt | Regular user terminal | `user@host:~$` |
| `#` | Root prompt | Administrator terminal | `root@host:~#` |
| `%` | Zsh prompt | Zsh shell user | `user@host ~ %` |
| `&` | Background | Run command in background | `script.sh &` |
| `&&` | AND | Run if previous succeeds | `cmd1 && cmd2` |
| `\|\|` | OR | Run if previous fails | `cmd1 \|\| cmd2` |
| `\|` | Pipe | Connect commands | `cmd1 \| cmd2` |
| `>` | Redirect | Save output to file | `cmd > file` |
| `>>` | Append | Add output to file | `cmd >> file` |
| `<` | Input | Read from file | `cmd < file` |
| `;` | Separator | Run commands in sequence | `cmd1; cmd2` |
| `~` | Home | Home directory | `cd ~` |
| `.` | Current | Current directory | `cp file .` |
| `..` | Parent | Parent directory | `cd ..` |
| `*` | Wildcard | Match multiple chars | `*.txt` |
| `?` | Wildcard | Match single char | `file?.txt` |
| `{}` | Expansion | Generate variations | `{1..5}` |
| `$()` | Substitution | Execute and use output | `$(date)` |
| `!` | History | Repeat commands | `!!` |

---

## 📊 Progress Checklist

**✅ Navigation Mastery**
- [ ] Can use `pwd` to see current location
- [ ] Can use `ls` with different options (-l, -a, -h)
- [ ] Can navigate with `cd` using both absolute and relative paths
- [ ] Understand the difference between `/home/user` and `~/`
- [ ] Can go back to previous directory with `cd -`

**✅ File Operations**
- [ ] Can create directories with `mkdir` and `mkdir -p`
- [ ] Can create files with `touch`
- [ ] Can copy files and directories with `cp` and `cp -r`
- [ ] Can move/rename with `mv`
- [ ] Can safely delete with `rm` and `rm -r`

**✅ Finding and Viewing**
- [ ] Can find files with `find` command
- [ ] Can view files with `cat`, `less`, `head`, `tail`
- [ ] Can use wildcards (* ? []) effectively
- [ ] Can search for files by name, type, and size

**✅ File Permissions Mastery**
- [ ] Can read permission displays (rwx format)
- [ ] Can use `chmod +x` to make scripts executable
- [ ] Can use `chmod` with symbolic notation (u+x, go-w, etc.)
- [ ] Can use `chmod` with numeric notation (755, 644, 600)
- [ ] Understand directory permissions vs file permissions
- [ ] Can troubleshoot "Permission denied" errors

**✅ Organization Skills**
- [ ] Can create logical directory structures
- [ ] Can organize files by type and purpose
- [ ] Can create backups of important files
- [ ] Can navigate complex directory structures

---

## 📋 Quick Reference: Common Permissions

### **Essential Permission Patterns**
```bash
# Files
chmod 644 file.txt           # Regular file (rw-r--r--)
chmod 600 secret.conf        # Private file (rw-------)
chmod 755 script.sh          # Executable file (rwxr-xr-x)
chmod +x script.sh           # Make executable (shortcut)

# Directories  
chmod 755 directory/         # Standard directory (drwxr-xr-x)
chmod 700 private-dir/       # Private directory (drwx------)

# DevOps specific
chmod 600 ~/.ssh/id_rsa      # SSH private key
chmod 644 ~/.ssh/id_rsa.pub  # SSH public key
chmod 700 ~/.ssh/            # SSH directory
chmod +x deploy.sh           # Deployment script
```

### **Permission Problem Solving**
```bash
# Can't run script?
chmod +x script.sh

# Can't access directory?
chmod +x directory/

# Can't edit file?
chmod u+w file.txt

# Need to see permissions?
ls -l filename
ls -ld directory/
```

## 💡 Pro Tips for Beginners

### **Terminal Shortcuts**
```bash
# Tab completion - press Tab to auto-complete
cd Doc[TAB]                       # Completes to Documents/
ls file[TAB]                      # Shows matching files

# Command history - use arrow keys
# Press ↑ to see previous commands
# Press ↓ to go forward in history

# Clear screen
clear                             # Clear terminal
# Or press Ctrl+L

# Cancel command
# Press Ctrl+C to stop current command
```

### **Safety Habits**
```bash
# Always check before deleting
ls file.txt                       # Verify file exists
rm file.txt                       # Then delete

# Use interactive mode for important operations
rm -i important-file.txt          # Asks for confirmation
cp -i source.txt dest.txt         # Asks before overwriting

# Create backups
cp important.txt important.txt.backup
```

### **Efficiency Tips**
```bash
# Use .. to go up directories quickly
cd ../../                         # Go up two levels
cd ../../../                      # Go up three levels

# Use ~ for home directory
cd ~/Documents                    # Go to Documents in home
ls ~/Downloads                    # List Downloads in home

# Combine commands with &&
mkdir new-project && cd new-project
```

---

## ➡️ Next Steps

**You're ready for the next topic when you can:**
- [ ] Navigate anywhere in the file system confidently
- [ ] Create, copy, move, and delete files without hesitation
- [ ] Find files using various criteria
- [ ] Organize files in logical directory structures
- [ ] Use wildcards and patterns effectively
- [ ] **Understand and manage file permissions with chmod**
- [ ] **Make scripts executable and troubleshoot permission errors**
- [ ] **Use essential commands like grep, cat, wc, sort for text processing**
- [ ] **Monitor system resources with ps, top, df, du commands**
- [ ] **Understand and use command flags effectively**
- [ ] Troubleshoot basic navigation issues

**Continue to:**
1. **02-Working_With_Files** - Text editing and advanced file manipulation
2. **03-Package_Management** - Installing and managing software
3. **04-Basic_System_Operations** - System information and processes

---

📄 **File Path:** `/DevOps/02-Linux_System_Administration/02-Linux_Basics_For_Beginners/01-Essential_Commands/01-Navigation_And_File_Operations.md` 