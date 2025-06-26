# Linux User Account Management

## 📖 What This File Does
Master Linux user account management - a critical DevOps skill for securing systems, managing access, and automating user provisioning. You'll learn to create, modify, and manage user accounts, understand permission systems, and implement security best practices.

## 🎯 Learning Objectives
- Understand Linux user account structure and authentication
- Create and manage user accounts and groups effectively
- Master file permissions and ownership concepts
- Implement security best practices for user management
- Automate user management tasks with scripts
- Troubleshoot common permission and access issues

## 📋 Prerequisites
- Completed Linux file system navigation module
- Understanding of Linux directory structure
- Basic command-line proficiency
- Access to a Linux system with sudo privileges

---

## 👥 Understanding Linux User System

### User Types in Linux
```bash
# System users (UID 0-999)
# - Root user (UID 0): System administrator
# - System service users (UID 1-999): Run services and daemons

# Regular users (UID 1000+)
# - Interactive users who can log in
# - Created for people who use the system

# Check current user information
whoami                    # Current username  
id                       # Current user ID and groups
id username              # Specific user information
groups                   # Groups current user belongs to
groups username          # Groups specific user belongs to

# View all users
cat /etc/passwd          # All user accounts
getent passwd            # All users (including LDAP/NIS)
cut -d: -f1 /etc/passwd  # Just usernames
```

### User Account Files
```bash
# Primary user account files
cat /etc/passwd          # User account information
cat /etc/shadow          # Encrypted passwords (root only)
cat /etc/group           # Group information
cat /etc/gshadow         # Group passwords (rarely used)

# Understanding /etc/passwd format:
# username:password:UID:GID:GECOS:home_directory:shell
# Example: john:x:1001:1001:John Smith,,,:/home/john:/bin/bash

# Understanding /etc/shadow format:
# username:encrypted_password:last_change:min_age:max_age:warn:inactive:expire
sudo cat /etc/shadow | head -5

# Understanding /etc/group format:
# group_name:password:GID:member_list
# Example: developers:x:1002:john,jane,bob
```

---

## 🔧 User Account Management Commands

### Creating User Accounts
```bash
# Basic user creation
sudo useradd john                    # Create user with defaults
sudo useradd -m john                 # Create user with home directory
sudo useradd -m -s /bin/bash john    # Specify shell

# Advanced user creation with options
sudo useradd -m \
    -d /home/john \              # Home directory
    -s /bin/bash \               # Login shell
    -c "John Smith, DevOps Engineer" \  # Full name/comment
    -G sudo,developers \         # Additional groups
    -e 2024-12-31 \             # Account expiration date
    john

# Create system user (for services)
sudo useradd -r \                # System user
    -s /bin/false \              # No login shell
    -c "Nginx web server" \      # Comment
    -d /var/www \               # Home directory
    nginx

# Set password for new user
sudo passwd john                 # Interactive password setting
echo "john:newpassword" | sudo chpasswd  # Non-interactive (scripting)

# Force password change on first login
sudo passwd -e john             # Expire password immediately
sudo chage -d 0 john            # Alternative method
```

### Modifying User Accounts
```bash
# Change user information
sudo usermod -c "John Smith, Senior DevOps" john    # Change comment
sudo usermod -s /bin/zsh john                       # Change shell
sudo usermod -d /home/johnsmith john                # Change home directory
sudo usermod -l johnsmith john                      # Change username

# Move home directory when changing it
sudo usermod -d /home/johnsmith -m john

# Account status management
sudo usermod -L john             # Lock account (disable password)
sudo usermod -U john             # Unlock account
sudo usermod -e 2024-12-31 john  # Set expiration date
sudo usermod -e "" john          # Remove expiration date

# Add user to groups
sudo usermod -a -G sudo john     # Add to sudo group (append)
sudo usermod -a -G docker,developers john  # Add to multiple groups
sudo usermod -G sudo john        # Set primary group (overwrites existing)

# Remove user from group
sudo gpasswd -d john developers  # Remove john from developers group
```

### Password Management
```bash
# Password policies and aging
sudo chage -l john               # View password aging info
sudo chage -M 90 john            # Max password age (90 days)
sudo chage -m 7 john             # Min password age (7 days)
sudo chage -W 7 john             # Warning days before expiration
sudo chage -I 30 john            # Inactive days after expiration
sudo chage -E 2024-12-31 john    # Account expiration date

# Interactive password aging setup
sudo chage john

# Check password status
sudo passwd -S john              # Password status
sudo passwd -S -a                # All users password status

# Generate secure passwords
openssl rand -base64 12          # Generate random password
pwgen -s 16 1                    # Generate secure password (if installed)

# Password complexity (edit /etc/pam.d/common-password)
# Add: password requisite pam_cracklib.so retry=3 minlen=8 difok=3
```

### Deleting User Accounts
```bash
# Delete user account
sudo userdel john                # Delete user (keep home directory)
sudo userdel -r john             # Delete user and home directory
sudo userdel -f john             # Force deletion (even if logged in)

# Safe user deletion process
# 1. Check if user is logged in
who | grep john
ps -u john

# 2. Kill user processes if necessary
sudo pkill -u john

# 3. Backup user data if needed
sudo tar -czf /backup/john-backup-$(date +%Y%m%d).tar.gz /home/john

# 4. Delete user
sudo userdel -r john

# 5. Check for remaining files
sudo find / -user john 2>/dev/null
sudo find / -group john 2>/dev/null
```

---

## 👥 Group Management

### Creating and Managing Groups
```bash
# Create groups
sudo groupadd developers         # Create group
sudo groupadd -g 2000 admins     # Create group with specific GID
sudo groupadd -r system-service  # Create system group

# View group information
getent group developers          # Group information
grep developers /etc/group       # Group info from file
id -nG john                      # Groups for specific user

# Modify groups
sudo groupmod -n dev-team developers    # Rename group
sudo groupmod -g 2001 developers        # Change GID

# Add/remove users to/from groups
sudo gpasswd -a john developers     # Add user to group
sudo gpasswd -d john developers     # Remove user from group
sudo gpasswd -A john developers     # Make john group administrator

# Delete groups
sudo groupdel developers         # Delete group (if no users have it as primary)

# List all groups
cut -d: -f1 /etc/group | sort    # All group names
getent group | cut -d: -f1 | sort  # All groups (including external)
```

### Group Administration
```bash
# Set group administrators
sudo gpasswd -A john,jane developers    # Multiple administrators
sudo gpasswd -A "" developers           # Remove all administrators

# Group passwords (rarely used)
sudo gpasswd developers          # Set group password
newgrp developers               # Switch to group (if you're a member)

# Primary vs Secondary groups
# Primary group: Default group for new files/directories
# Secondary groups: Additional groups for permissions

# Change user's primary group
sudo usermod -g developers john  # Change primary group
id john                         # Verify change
```

---

## 🔐 File Permissions and Ownership

### Understanding Permission System
```bash
# Permission format: rwxrwxrwx (owner, group, others)
# r = read (4), w = write (2), x = execute (1)

# View file permissions
ls -l filename                  # Long format shows permissions
ls -la /home/john              # All files including hidden
stat filename                 # Detailed file information

# Permission examples:
# -rw-r--r--  (644) Regular file: owner read/write, group/others read
# drwxr-xr-x  (755) Directory: owner full, group/others read/execute
# -rwxr-xr-x  (755) Executable: owner full, group/others read/execute
# -rw-------  (600) Private file: owner read/write only
```

### Changing File Permissions
```bash
# Numeric method (octal)
chmod 644 file.txt              # rw-r--r--
chmod 755 script.sh             # rwxr-xr-x
chmod 600 private.txt           # rw-------
chmod 777 shared-file           # rwxrwxrwx (dangerous!)

# Symbolic method
chmod u+x script.sh             # Add execute for owner
chmod g+w file.txt              # Add write for group
chmod o-r private.txt           # Remove read for others
chmod a+r public.txt            # Add read for all (a=all, u=user, g=group, o=others)

# Multiple changes
chmod u+x,g+w,o-r file.txt      # Multiple operations
chmod ug+rw,o-rwx file.txt      # Owner and group read/write, others nothing

# Recursive permissions
chmod -R 755 /var/www/html      # Apply to directory and all contents
chmod -R u+x ~/scripts          # Make all scripts executable

# Common permission patterns
chmod 644 *.txt                 # Documents
chmod 755 *.sh                  # Scripts
chmod 600 ~/.ssh/id_rsa         # SSH private key
chmod 644 ~/.ssh/id_rsa.pub     # SSH public key
chmod 700 ~/.ssh                # SSH directory
```

### Changing File Ownership
```bash
# Change owner
sudo chown john file.txt         # Change owner to john
sudo chown john:developers file.txt  # Change owner and group
sudo chown :developers file.txt  # Change group only

# Recursive ownership
sudo chown -R john:john /home/john    # Change ownership recursively
sudo chown -R www-data:www-data /var/www  # Web server files

# Change group only
sudo chgrp developers file.txt   # Change group to developers
sudo chgrp -R developers /project  # Recursive group change

# Copy permissions from another file
chmod --reference=source.txt target.txt  # Copy permissions
chown --reference=source.txt target.txt  # Copy ownership
```

### Special Permissions
```bash
# Setuid (SUID) - Run as file owner
chmod u+s /usr/bin/sudo         # Setuid bit
chmod 4755 program             # Numeric with setuid (4)
ls -l /usr/bin/sudo             # Shows 's' in owner execute position

# Setgid (SGID) - Run as file group or inherit group
chmod g+s /shared/directory     # Setgid on directory
chmod 2755 directory           # Numeric with setgid (2)

# Sticky bit - Only owner can delete files
chmod +t /tmp                   # Sticky bit
chmod 1755 directory           # Numeric with sticky bit (1)
ls -ld /tmp                     # Shows 't' in others execute position

# Find files with special permissions
find / -perm -4000 2>/dev/null  # Find SUID files
find / -perm -2000 2>/dev/null  # Find SGID files
find / -perm -1000 2>/dev/null  # Find sticky bit files
```

---

## 🛡️ Security Best Practices

### Secure User Account Practices
```bash
# 1. Use strong password policies
sudo nano /etc/login.defs       # Edit password aging defaults
# PASS_MAX_DAYS   90
# PASS_MIN_DAYS   7
# PASS_WARN_AGE   7

# 2. Disable unused accounts
sudo usermod -L -e 1 olduser    # Lock and expire account
sudo usermod -s /sbin/nologin service-user  # Prevent login

# 3. Monitor user activity
last                            # Recent logins
lastlog                         # Last login for all users
who                            # Currently logged in users
w                              # Detailed current user activity

# 4. Audit sudo usage
sudo tail /var/log/auth.log | grep sudo  # Sudo commands
sudo journalctl | grep sudo     # Systemd systems

# 5. Set up proper umask
umask 022                       # Default permissions for new files
echo "umask 022" >> ~/.bashrc   # Make permanent
```

### SSH Key Management
```bash
# Generate SSH key pair
ssh-keygen -t ed25519 -C "john@company.com"  # Modern algorithm
ssh-keygen -t rsa -b 4096 -C "john@company.com"  # RSA with 4096 bits

# Set proper permissions for SSH files
chmod 700 ~/.ssh                # SSH directory
chmod 600 ~/.ssh/id_ed25519     # Private key
chmod 644 ~/.ssh/id_ed25519.pub # Public key
chmod 600 ~/.ssh/authorized_keys # Authorized keys
chmod 600 ~/.ssh/config         # SSH config

# Add public key to authorized_keys
cat ~/.ssh/id_ed25519.pub >> ~/.ssh/authorized_keys

# Copy key to remote server
ssh-copy-id user@remote-server   # Automatic method
# Or manually:
scp ~/.ssh/id_ed25519.pub user@remote-server:~/.ssh/authorized_keys
```

### Access Control Lists (ACLs)
```bash
# Check if filesystem supports ACLs
mount | grep acl                # Check mount options
tune2fs -l /dev/sda1 | grep acl # Check filesystem features

# View ACLs
getfacl filename                # Show ACL for file
getfacl -R directory           # Recursive ACL listing

# Set ACLs
setfacl -m u:john:rw file.txt   # Give john read/write access
setfacl -m g:developers:rx dir/ # Give developers group read/execute
setfacl -m o::--- file.txt      # Remove all permissions for others

# Default ACLs for directories
setfacl -d -m u:john:rw directory/  # Default ACL for new files
setfacl -d -m g:developers:rx directory/  # Default group permissions

# Remove ACLs
setfacl -x u:john file.txt      # Remove specific user ACL
setfacl -b file.txt             # Remove all ACLs

# Copy ACLs
getfacl source.txt | setfacl --set-file=- target.txt
```

---

## 🎯 Practical Exercises

### Exercise 1: User Management Lab
```bash
#!/bin/bash
# Complete User Management Exercise

# Task 1: Create a development team structure
echo "=== Creating Development Team Structure ==="

# Create groups
sudo groupadd developers
sudo groupadd testers
sudo groupadd devops

# Create users
sudo useradd -m -s /bin/bash -c "Alice Developer" -G developers alice
sudo useradd -m -s /bin/bash -c "Bob Tester" -G testers bob
sudo useradd -m -s /bin/bash -c "Carol DevOps" -G devops carol

# Set passwords (in real scenario, use secure passwords)
echo "alice:dev123" | sudo chpasswd
echo "bob:test123" | sudo chpasswd
echo "carol:ops123" | sudo chpasswd

# Task 2: Create shared project directory
sudo mkdir -p /project/{development,testing,operations}
sudo chown -R root:developers /project/development
sudo chown -R root:testers /project/testing
sudo chown -R root:devops /project/operations

# Set appropriate permissions
sudo chmod 775 /project/development
sudo chmod 775 /project/testing
sudo chmod 775 /project/operations

# Task 3: Verify setup
echo "=== Verification ==="
ls -la /project/
id alice
id bob
id carol
```

### Exercise 2: Permission Management Scenarios
```bash
#!/bin/bash
# Permission Management Exercise

# Scenario 1: Web server files
echo "=== Web Server File Permissions ==="
sudo mkdir -p /var/www/html/mysite
sudo touch /var/www/html/mysite/{index.html,style.css,script.js}

# Set web server ownership and permissions
sudo chown -R www-data:www-data /var/www/html/mysite
sudo chmod 644 /var/www/html/mysite/*.{html,css,js}
sudo chmod 755 /var/www/html/mysite

# Scenario 2: Shared development directory
echo "=== Shared Development Directory ==="
sudo mkdir -p /shared/projects
sudo chgrp developers /shared/projects
sudo chmod 2775 /shared/projects  # SGID bit ensures new files inherit group

# Test file creation
sudo -u alice touch /shared/projects/alice-file.txt
ls -la /shared/projects/

# Scenario 3: Secure configuration files
echo "=== Secure Configuration Files ==="
sudo mkdir -p /etc/myapp
sudo touch /etc/myapp/{config.yml,secrets.yml}

# Configuration readable by group, secrets only by owner
sudo chown root:myapp /etc/myapp/config.yml
sudo chmod 640 /etc/myapp/config.yml

sudo chown root:root /etc/myapp/secrets.yml
sudo chmod 600 /etc/myapp/secrets.yml

ls -la /etc/myapp/
```

### Exercise 3: User Automation Script
```bash
#!/bin/bash
# User Management Automation Script

set -euo pipefail

# Configuration
USER_DATA_FILE="users.csv"
LOG_FILE="/var/log/user-management.log"

# Logging function
log() {
    echo "[$(date '+%Y-%m-%d %H:%M:%S')] $*" | tee -a "$LOG_FILE"
}

# Create sample user data file
cat > "$USER_DATA_FILE" << 'EOF'
username,full_name,groups,shell
john,John Smith,developers,/bin/bash
jane,Jane Doe,testers,/bin/bash
mike,Mike Johnson,devops,/bin/bash
service1,Service Account,none,/bin/false
EOF

# Function to create user from CSV data
create_user() {
    local username="$1"
    local full_name="$2"
    local groups="$3"
    local shell="$4"
    
    log "Creating user: $username"
    
    # Create user
    if sudo useradd -m -s "$shell" -c "$full_name" "$username"; then
        log "Successfully created user: $username"
    else
        log "Failed to create user: $username"
        return 1
    fi
    
    # Add to groups if specified
    if [[ "$groups" != "none" ]]; then
        IFS=',' read -ra GROUP_ARRAY <<< "$groups"
        for group in "${GROUP_ARRAY[@]}"; do
            # Create group if it doesn't exist
            if ! getent group "$group" > /dev/null; then
                sudo groupadd "$group"
                log "Created group: $group"
            fi
            
            # Add user to group
            sudo usermod -a -G "$group" "$username"
            log "Added $username to group: $group"
        done
    fi
    
    # Set temporary password (should be changed on first login)
    local temp_password=$(openssl rand -base64 12)
    echo "$username:$temp_password" | sudo chpasswd
    sudo passwd -e "$username"  # Force password change on first login
    
    log "Set temporary password for $username: $temp_password"
}

# Main function
main() {
    log "Starting user creation process"
    
    # Skip header line and process each user
    tail -n +2 "$USER_DATA_FILE" | while IFS=',' read -r username full_name groups shell; do
        if [[ -n "$username" ]]; then
            create_user "$username" "$full_name" "$groups" "$shell"
        fi
    done
    
    log "User creation process completed"
    
    # Display summary
    echo "=== User Creation Summary ==="
    tail -n +2 "$USER_DATA_FILE" | while IFS=',' read -r username full_name groups shell; do
        if id "$username" &>/dev/null; then
            echo "✓ $username ($full_name) - Created successfully"
            echo "  Groups: $(groups $username)"
        else
            echo "✗ $username ($full_name) - Creation failed"
        fi
    done
}

# Execute main function
main
```

---

## 🔧 Troubleshooting Common Issues

### Permission Denied Errors
```bash
# Problem: Cannot access file/directory
ls /root/private-file
# ls: cannot open directory '/root/private-file': Permission denied

# Solutions:
# 1. Check current permissions
ls -la /root/private-file

# 2. Check if you need sudo
sudo ls -la /root/private-file

# 3. Check if you're in the right group
groups
id

# 4. Check file ownership
stat /path/to/file
```

### User Cannot Login
```bash
# Problem: User cannot log in

# Check 1: Account status
sudo passwd -S username
sudo chage -l username

# Check 2: Account expiration
sudo usermod -e "" username  # Remove expiration

# Check 3: Shell assignment
grep username /etc/passwd
sudo usermod -s /bin/bash username

# Check 4: Home directory
ls -la /home/username
sudo mkdir -p /home/username
sudo chown username:username /home/username
```

### File Permission Issues
```bash
# Problem: Cannot execute script
./myscript.sh
# bash: ./myscript.sh: Permission denied

# Solutions:
chmod +x myscript.sh          # Add execute permission
ls -la myscript.sh            # Verify permissions

# Problem: Cannot write to file
echo "test" > file.txt
# bash: file.txt: Permission denied

# Solutions:
ls -la file.txt               # Check permissions
chmod u+w file.txt            # Add write permission
sudo chown $USER file.txt     # Change ownership if needed
```

---

## 📊 Progress Checkpoint

Test your user management knowledge:

```bash
# 1. Can you create a user with specific requirements?
sudo useradd -m -s /bin/bash -c "Test User" -G sudo testuser

# 2. Can you set up proper file permissions?
mkdir test-dir
chmod 755 test-dir
touch test-dir/test-file
chmod 644 test-dir/test-file

# 3. Can you manage group membership?
sudo groupadd testgroup
sudo usermod -a -G testgroup testuser
groups testuser

# 4. Can you troubleshoot permission issues?
ls -la test-dir/test-file
stat test-dir/test-file

# 5. Can you secure sensitive files?
touch sensitive-file
chmod 600 sensitive-file
ls -la sensitive-file

# Cleanup
sudo userdel -r testuser
sudo groupdel testgroup
rm -rf test-dir sensitive-file
```

If you can complete these tasks confidently, you're ready for the next module!

---

## ➡️ Next Steps

You're now ready for:
1. **03-Process_Management** - Managing system processes and services
2. **04-System_Administration_Basics** - System monitoring and maintenance
3. Advanced security topics and automation

---

📄 **File Path:** `/DevOps/02-Linux_System_Administration/01-Linux_Fundamentals/02-Users_Groups_Permissions/01-User_Account_Management.md` 