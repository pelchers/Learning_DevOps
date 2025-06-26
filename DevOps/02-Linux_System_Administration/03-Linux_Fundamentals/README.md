# 🐧 Linux Fundamentals: Building Your DevOps Foundation

## 📖 What This Module Does
Master the essential Linux skills that form the foundation of all DevOps operations. You'll learn file system navigation, user management, permissions, process control, and system administration tasks that are crucial for every DevOps engineer.

## 🎯 Learning Objectives
By completing this module, you will:
- ✅ Navigate the Linux file system with confidence
- ✅ Understand and manage users, groups, and permissions
- ✅ Monitor and control system processes effectively
- ✅ Perform essential system administration tasks
- ✅ Troubleshoot common Linux issues
- ✅ Build foundational skills for DevOps automation

## 📋 Prerequisites
- Access to a Linux system (VM, WSL, or native)
- Basic understanding of command-line concepts
- Completed Module 01: DevOps Concepts and Culture

---

## 📚 Module Structure

### **📁 01-File_System_Navigation/**
**Time Investment:** 3-4 days
- Linux directory structure (`/`, `/home`, `/var`, `/etc`)
- Essential navigation commands (`cd`, `ls`, `pwd`, `find`)
- File and directory operations (`cp`, `mv`, `rm`, `mkdir`)
- File content viewing (`cat`, `less`, `head`, `tail`, `grep`)

### **👥 02-Users_Groups_Permissions/**
**Time Investment:** 3-4 days  
- User account management (`useradd`, `usermod`, `userdel`)
- Group management (`groupadd`, `groupmod`, `groups`)
- File permissions and ownership (`chmod`, `chown`, `chgrp`)
- Access control and security principles

### **⚙️ 03-Process_Management/**
**Time Investment:** 2-3 days
- Understanding processes and PIDs
- Process monitoring (`ps`, `top`, `htop`, `pgrep`)
- Process control (`kill`, `killall`, `jobs`, `nohup`)
- Service management (`systemctl`, `service`)

### **🔧 04-System_Administration_Basics/**
**Time Investment:** 3-4 days
- System information (`uname`, `df`, `du`, `free`, `lscpu`)
- Package management (apt/yum/dnf basics)
- Environment variables and PATH
- Basic system configuration

---

## 🚀 Learning Path

### **Day 1-4: File System Mastery**
```bash
# Daily practice commands
pwd && ls -la
find /var -name "*.log" -type f
grep "error" /var/log/syslog
```

### **Day 5-8: User & Permission Management**
```bash
# Daily practice tasks
sudo useradd testuser
sudo chmod 755 /home/testuser
sudo chown testuser:testuser /home/testuser/file.txt
```

### **Day 9-11: Process Control**
```bash
# Daily monitoring practice
ps aux | grep nginx
top -p $(pgrep nginx)
sudo systemctl status nginx
```

### **Day 12-15: System Administration**
```bash
# Daily admin tasks
df -h
free -h
sudo apt update && apt list --upgradable
```

---

## 🎯 Hands-On Exercises

### **Exercise 1: File System Explorer**
Create a script that explores and documents your system's directory structure.

### **Exercise 2: User Management Lab**
Set up multiple users with different permission levels and shared directories.

### **Exercise 3: Process Monitor**
Build a simple process monitoring script using basic Linux commands.

### **Exercise 4: System Health Check**
Create a system information gathering script for troubleshooting.

---

## 📊 Progress Checkpoints

**File System Navigation** ✅
- [ ] Can navigate to any directory without using tab completion
- [ ] Can find files using multiple search criteria
- [ ] Can manipulate files and directories confidently
- [ ] Understands absolute vs relative paths

**User & Permission Management** ✅
- [ ] Can create and manage user accounts
- [ ] Understands and applies file permissions correctly
- [ ] Can troubleshoot permission-related issues
- [ ] Knows when to use sudo appropriately

**Process Management** ✅
- [ ] Can identify and monitor running processes
- [ ] Can start, stop, and restart services
- [ ] Understands process hierarchy and relationships
- [ ] Can troubleshoot hung or problematic processes

**System Administration** ✅
- [ ] Can gather comprehensive system information
- [ ] Understands basic package management
- [ ] Can configure environment variables
- [ ] Can perform routine maintenance tasks

---

## 🔗 Integration with DevOps

This module prepares you for:
- **Module 03: Cloud Fundamentals AWS** - Linux VMs in the cloud
- **Module 04: Containerization Docker** - Linux containers and processes
- **Module 06: CI/CD Pipelines** - Linux-based build agents
- **Module 07: Infrastructure as Code** - Linux server provisioning

---

## ➡️ Next Steps

Upon completion, you'll be ready for:
1. **02-System_Monitoring_And_Logging/** - Advanced system observability
2. **03-Network_Configuration/** - Linux networking and security
3. **04-Shell_Scripting_Automation/** - Automating your Linux skills

---

📄 **File Path:** `/DevOps/02-Linux_System_Administration/01-Linux_Fundamentals/README.md` 