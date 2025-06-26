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
- [ ] Troubleshoot basic navigation issues

**Continue to:**
1. **02-Working_With_Files** - Text editing and file permissions
2. **03-Package_Management** - Installing and managing software
3. **04-Basic_System_Operations** - System information and processes

---

📄 **File Path:** `/DevOps/02-Linux_System_Administration/02-Linux_Basics_For_Beginners/01-Essential_Commands/01-Navigation_And_File_Operations.md` 