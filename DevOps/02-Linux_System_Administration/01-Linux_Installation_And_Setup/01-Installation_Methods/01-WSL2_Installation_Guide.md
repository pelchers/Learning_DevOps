# WSL2 Installation Guide: Linux on Windows Made Easy

## 📖 What This File Does
Learn to install Windows Subsystem for Linux 2 (WSL2) - the easiest way to get started with Linux on Windows. Perfect for beginners who want to learn DevOps without the complexity of virtual machines.

## 🎯 Learning Objectives
- Install WSL2 on Windows 10/11
- Set up Ubuntu Linux distribution
- Configure WSL2 for optimal performance
- Access Linux files from Windows and vice versa
- Troubleshoot common WSL2 issues

## 📋 Prerequisites
- Windows 10 version 2004+ or Windows 11
- Administrator access to your computer
- Stable internet connection
- Basic familiarity with Windows settings

---

## 🚀 Step-by-Step Installation

### **Step 1: Check Windows Version**
```powershell
# Open PowerShell as Administrator and run:
winver

# You need:
# Windows 10: Version 2004 (Build 19041) or higher
# Windows 11: Any version
```

**How to open PowerShell as Administrator:**
1. Press `Windows + X`
2. Click "Windows PowerShell (Admin)" or "Terminal (Admin)"
3. Click "Yes" when prompted

### **Step 2: Enable Required Windows Features**
```powershell
# Run these commands in PowerShell (Admin):

# Enable WSL feature
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart

# Enable Virtual Machine Platform
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart

# Restart your computer after running these commands
```

**What these commands do:**
- First command: Enables the basic WSL functionality
- Second command: Enables virtualization features needed for WSL2
- You MUST restart after running these!

### **Step 3: Download and Install WSL2 Linux Kernel Update**

1. **Download the kernel update:**
   - Go to: https://aka.ms/wsl2kernel
   - Download "WSL2 Linux kernel update package for x64 machines"
   - Run the downloaded file as Administrator

2. **Install the update:**
   - Double-click the downloaded `.msi` file
   - Follow the installation wizard
   - Click "Next" → "Next" → "Finish"

### **Step 4: Set WSL2 as Default Version**
```powershell
# In PowerShell (Admin), run:
wsl --set-default-version 2

# You should see: "For information on key differences with WSL 2 please visit https://aka.ms/wsl2"
```

### **Step 5: Install Ubuntu Linux Distribution**

**Method 1: Using Microsoft Store (Recommended)**
1. Open Microsoft Store
2. Search for "Ubuntu"
3. Click "Ubuntu" (the one without version numbers)
4. Click "Get" or "Install"
5. Wait for download to complete

**Method 2: Using PowerShell**
```powershell
# Install Ubuntu using command line:
wsl --install -d Ubuntu

# This will download and install Ubuntu automatically
```

### **Step 6: Initial Ubuntu Setup**

1. **Launch Ubuntu:**
   - Find "Ubuntu" in Start Menu
   - Click to launch (first launch takes a few minutes)

2. **Create your Linux user account:**
   ```
   Installing, this may take a few minutes...
   Please create a default UNIX user account. The username does not need to match your Windows username.
   For more information visit: https://aka.ms/wslusers
   Enter new UNIX username: [type your desired username]
   New password: [type a secure password]
   Retype new password: [confirm your password]
   ```

3. **Important notes:**
   - Username should be lowercase, no spaces
   - Password won't show as you type (this is normal!)
   - Remember this password - you'll need it for `sudo` commands

### **Step 7: Verify Installation**
```bash
# Once Ubuntu is running, test these commands:

# Check your Linux distribution
cat /etc/os-release

# Check WSL version
wsl.exe -l -v

# Update package list
sudo apt update

# Check available disk space
df -h
```

**Expected output:**
```
$ cat /etc/os-release
NAME="Ubuntu"
VERSION="22.04.1 LTS (Jammy Jellyfish)"
...

$ wsl.exe -l -v
  NAME      STATE           VERSION
* Ubuntu    Running         2
```

---

## 🔧 Essential Configuration

### **Configure Git (Important for DevOps)**
```bash
# Set up Git with your information:
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Verify configuration:
git config --list
```

### **Install Essential Tools**
```bash
# Update system packages:
sudo apt update && sudo apt upgrade -y

# Install essential development tools:
sudo apt install -y curl wget git vim nano tree htop

# Install build tools (needed for many DevOps tools):
sudo apt install -y build-essential
```

### **Set Up Windows-Linux File Access**

**Access Linux files from Windows:**
- Open File Explorer
- Type in address bar: `\\wsl$\Ubuntu\home\yourusername`
- Bookmark this location for easy access

**Access Windows files from Linux:**
```bash
# Windows C: drive is mounted at /mnt/c
ls /mnt/c/Users/

# Your Windows user folder:
ls /mnt/c/Users/YourWindowsUsername/

# Create a shortcut to your Windows Desktop:
ln -s /mnt/c/Users/YourWindowsUsername/Desktop ~/windows-desktop
```

### **Improve WSL2 Performance**

**Create WSL configuration file:**
```bash
# Create .wslconfig file in your Windows user directory
# Use Windows Notepad or any text editor

# File location: C:\Users\YourWindowsUsername\.wslconfig
# Content:
[wsl2]
memory=4GB
processors=2
swap=2GB
```

**Restart WSL to apply changes:**
```powershell
# In PowerShell (Admin):
wsl --shutdown
# Then restart Ubuntu from Start Menu
```

---

## 🎯 Testing Your Installation

### **Basic Functionality Test**
```bash
# Test 1: Basic commands work
pwd
ls -la
whoami

# Test 2: Package manager works
sudo apt update

# Test 3: File operations work
mkdir test-directory
cd test-directory
echo "Hello DevOps!" > test-file.txt
cat test-file.txt
cd ..
rm -rf test-directory

# Test 4: Network connectivity
ping -c 3 google.com
curl -s https://httpbin.org/ip
```

### **DevOps Tools Test**
```bash
# Test 5: Install and test a DevOps tool
# Install Docker (we'll use this throughout the course)
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Add your user to docker group
sudo usermod -aG docker $USER

# Test Docker (after logging out and back in)
docker --version
```

---

## 🔧 Troubleshooting Common Issues

### **Issue 1: "WSL 2 requires an update to its kernel component"**
**Solution:**
1. Download kernel update from: https://aka.ms/wsl2kernel
2. Install as Administrator
3. Restart WSL: `wsl --shutdown` in PowerShell

### **Issue 2: "The requested operation could not be completed due to a virtual disk system limitation"**
**Solution:**
```powershell
# In PowerShell (Admin):
wsl --shutdown
# Wait 10 seconds, then restart Ubuntu
```

### **Issue 3: Ubuntu won't start or crashes**
**Solution:**
```powershell
# Reset Ubuntu (WARNING: This deletes all Linux files):
wsl --unregister Ubuntu
# Then reinstall Ubuntu from Microsoft Store
```

### **Issue 4: Can't access Windows files from Linux**
**Solution:**
```bash
# Check if Windows drives are mounted:
ls /mnt/

# If empty, restart WSL:
# In PowerShell: wsl --shutdown
# Then restart Ubuntu
```

### **Issue 5: Slow performance**
**Solutions:**
1. **Don't store files on Windows filesystem**
   ```bash
   # SLOW: Working in /mnt/c/Users/...
   # FAST: Working in /home/yourusername/
   ```

2. **Exclude WSL from Windows Defender**
   - Open Windows Security
   - Go to Virus & threat protection
   - Add exclusion for: `%USERPROFILE%\AppData\Local\Packages\CanonicalGroupLimited.Ubuntu*`

3. **Use WSL2 (not WSL1)**
   ```powershell
   # Check version:
   wsl -l -v
   
   # Upgrade if needed:
   wsl --set-version Ubuntu 2
   ```

---

## 🎯 Quick Commands Reference

### **WSL Management (run in PowerShell)**
```powershell
# List installed distributions
wsl -l -v

# Start/stop WSL
wsl --shutdown
wsl -d Ubuntu

# Check WSL status
wsl --status

# Update WSL
wsl --update
```

### **File Navigation**
```bash
# Navigate to Windows user folder
cd /mnt/c/Users/YourWindowsUsername/

# Navigate to Linux home
cd ~
cd /home/yourusername

# Create shortcut to Windows Desktop
ln -s /mnt/c/Users/YourWindowsUsername/Desktop ~/desktop
```

---

## 📊 Success Checklist

**✅ Installation Complete When:**
- [ ] Ubuntu launches without errors
- [ ] You can run `sudo apt update` successfully
- [ ] You can access Windows files from Linux (`ls /mnt/c`)
- [ ] You can access Linux files from Windows Explorer (`\\wsl$`)
- [ ] Git is configured with your name and email
- [ ] Basic tools are installed (curl, wget, git, vim)
- [ ] You understand the file system layout

---

## ➡️ Next Steps

**You're ready for the next guide when:**
1. WSL2 Ubuntu is running smoothly
2. You can navigate between Windows and Linux filesystems
3. You can install packages with `apt`
4. You feel comfortable opening and using the terminal

**Continue to:**
- `02-Linux_Desktop_Basics/` - Learn the Ubuntu desktop environment
- `03-Terminal_Introduction/` - Master the command line
- `04-Essential_Tools_Setup/` - Set up your DevOps toolkit

---

## 💡 Pro Tips for WSL2

### **Best Practices**
1. **Store projects in Linux filesystem** (`/home/username/`) for better performance
2. **Use Windows Terminal** for better terminal experience
3. **Set up SSH keys** for GitHub/GitLab access
4. **Regular updates:** `sudo apt update && sudo apt upgrade`

### **Cool WSL2 Features**
```bash
# Open Windows Explorer from Linux
explorer.exe .

# Run Windows commands from Linux
cmd.exe /c dir

# Open VS Code from Linux
code .

# Access Linux from Windows Terminal
wsl
```

---

📄 **File Path:** `/DevOps/02-Linux_System_Administration/01-Linux_Installation_And_Setup/01-Installation_Methods/01-WSL2_Installation_Guide.md` 