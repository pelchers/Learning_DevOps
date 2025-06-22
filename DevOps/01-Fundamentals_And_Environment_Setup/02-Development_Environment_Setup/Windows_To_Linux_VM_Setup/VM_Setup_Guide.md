# 🖥️ Windows to Linux VM Setup Guide

## 📋 Learning Objectives

By completing this guide, you will:
- ✅ Set up a Linux virtual machine on your Windows computer
- ✅ Configure Ubuntu for DevOps development work
- ✅ Understand virtualization concepts and best practices
- ✅ Prepare your Linux environment for the learning journey

---

## 🎯 Prerequisites

### **System Requirements:**
- **Windows 10/11** with admin privileges
- **Minimum 8GB RAM** (16GB recommended)
- **50GB free disk space** (100GB recommended)
- **Virtualization enabled** in BIOS/UEFI
- **Stable internet connection** for downloads

### **Before You Begin:**
- Backup important data on your Windows machine
- Ensure Windows is up to date
- Check if Hyper-V is enabled (we'll disable if needed)
- Have your Windows admin password ready

---

## 🔧 Hypervisor Options Comparison

### **Option 1: VirtualBox (Recommended for Beginners)** 📦
**Pros:**
- Free and open-source
- Easy to use interface
- Good performance for learning
- Cross-platform compatibility
- Extensive documentation

**Cons:**
- Slightly lower performance than VMware
- Limited advanced features

### **Option 2: VMware Workstation Pro** ⚡
**Pros:**
- Excellent performance
- Advanced features and snapshots
- Professional-grade virtualization
- Better 3D acceleration

**Cons:**
- Paid software ($199.99)
- More complex for beginners
- Larger resource footprint

### **Option 3: Windows Subsystem for Linux (WSL2)** 🪟
**Pros:**
- Native integration with Windows
- Excellent performance
- Smaller resource usage
- Easy file sharing

**Cons:**
- Windows-specific solution
- Some limitations for certain DevOps tools
- Less isolation than full VM

**Our Choice:** We'll use **VirtualBox** for this guide as it's free, reliable, and perfect for learning DevOps concepts.

---

## 📥 Step 1: Download Required Software

### **Download VirtualBox:**
1. Visit: https://www.virtualbox.org/wiki/Downloads
2. Click "Windows hosts" to download VirtualBox installer
3. Also download "VirtualBox Extension Pack" for enhanced features

### **Download Ubuntu Desktop:**
1. Visit: https://ubuntu.com/download/desktop
2. Download **Ubuntu 22.04.3 LTS** (Long Term Support)
3. Choose the desktop version for easier setup
4. File size: ~4.5GB (be patient with download)

### **File Verification (Recommended):**
- Verify SHA256 checksums to ensure file integrity
- Ubuntu provides checksums on their download page
- Use Windows PowerShell: `Get-FileHash -Algorithm SHA256 filename.iso`

---

## 🛠️ Step 2: Install VirtualBox

### **Installation Process:**
1. **Run VirtualBox installer** as Administrator
2. **Accept default settings** through installation wizard
3. **Install** when prompted about device software (drivers)
4. **Reboot** if prompted by installer

### **Install Extension Pack:**
1. **Open VirtualBox** after installation
2. Go to **File > Preferences > Extensions**
3. **Click** the package icon to add extension pack
4. **Browse** to downloaded Extension Pack file
5. **Install** and accept license agreement

### **Verify Installation:**
- VirtualBox should open without errors
- Check version in Help > About VirtualBox
- Version should be 7.0 or newer

---

## 🏗️ Step 3: Create Ubuntu Virtual Machine

### **VM Creation Steps:**

#### **1. Create New VM:**
```
VirtualBox > New
Name: DevOps-Ubuntu
Type: Linux
Version: Ubuntu (64-bit)
Memory: 4096 MB (4GB) minimum, 8192 MB (8GB) recommended
```

#### **2. Create Virtual Hard Disk:**
```
Create a virtual hard disk now > Create
Hard disk file type: VDI (VirtualBox Disk Image)
Storage: Dynamically allocated
Size: 50 GB minimum, 100 GB recommended
```

#### **3. Configure VM Settings:**
Right-click VM > Settings:

**System Tab:**
- **Motherboard**: Enable I/O APIC
- **Processor**: Assign 2-4 CPU cores (half of your physical cores)
- **Acceleration**: Enable VT-x/AMD-V if available

**Display Tab:**
- **Video Memory**: 64 MB minimum, 128 MB recommended
- **Acceleration**: Enable 3D acceleration

**Network Tab:**
- **Adapter 1**: Attached to NAT (default)
- **Advanced**: Allow All for Promiscuous Mode

**Storage Tab:**
- **Attach Ubuntu ISO** to optical drive
- Click optical drive icon > Choose disk file > Select Ubuntu ISO

---

## 🚀 Step 4: Install Ubuntu Linux

### **Start Installation:**
1. **Start** the VM (click Start button)
2. **Boot** from Ubuntu ISO (should auto-boot)
3. **Select language** and click "Install Ubuntu"

### **Installation Configuration:**

#### **Keyboard Layout:**
- Choose your keyboard layout (usually English US)
- Test in the text box to verify

#### **Updates and Software:**
- **Select**: "Normal installation"
- **Check**: "Download updates while installing Ubuntu"
- **Check**: "Install third-party software" (for graphics drivers)

#### **Installation Type:**
- **Select**: "Erase disk and install Ubuntu"
- Don't worry - this only affects the virtual disk, not Windows

#### **User Account Setup:**
```
Your name: Your Full Name
Computer name: devops-ubuntu
Username: devops (keep it simple)
Password: (choose a secure but memorable password)
☑ Log in automatically (recommended for VM)
```

#### **Time Zone:**
- Select your appropriate time zone
- This will be auto-detected in most cases

### **Complete Installation:**
1. **Wait** for installation to complete (20-45 minutes)
2. **Restart** when prompted
3. **Remove** installation medium when prompted (VM does this automatically)
4. **Login** to your new Ubuntu system

---

## ⚙️ Step 5: Initial Ubuntu Configuration

### **Update System:**
Open Terminal (Ctrl+Alt+T) and run:
```bash
sudo apt update && sudo apt upgrade -y
sudo reboot
```

### **Install Essential Tools:**
```bash
# Development tools
sudo apt install -y curl wget git vim nano build-essential

# Network tools
sudo apt install -y net-tools openssh-server

# System utilities
sudo apt install -y htop tree unzip software-properties-common apt-transport-https ca-certificates gnupg lsb-release
```

### **Configure SSH (Optional but Recommended):**
```bash
# Enable SSH service
sudo systemctl enable ssh
sudo systemctl start ssh

# Check SSH status
sudo systemctl status ssh

# Find your VM's IP address
ip addr show
```

---

## 🔧 Step 6: Install VirtualBox Guest Additions

Guest Additions provide better integration between host and guest systems.

### **Installation Steps:**
1. **Start Ubuntu VM** and log in
2. **VirtualBox menu**: Devices > Insert Guest Additions CD Image
3. **Open Terminal** and run:
```bash
sudo apt update
sudo apt install -y gcc make perl

# Mount and install Guest Additions
sudo mkdir /mnt/cdrom
sudo mount /dev/cdrom /mnt/cdrom
cd /mnt/cdrom
sudo ./VBoxLinuxAdditions.run

# Reboot to activate
sudo reboot
```

### **Verify Installation:**
After reboot, you should have:
- **Shared clipboard** between Windows and Ubuntu
- **Drag and drop** file support
- **Auto-resize** VM window
- **Better graphics** performance

---

## 📁 Step 7: Configure Shared Folders (Optional)

Share folders between Windows and Ubuntu for easy file transfer.

### **Setup Process:**
1. **VirtualBox**: VM Settings > Shared Folders
2. **Add new** shared folder:
   - Folder Path: Browse to Windows folder (e.g., C:\DevOps)
   - Folder Name: devops-shared
   - ☑ Auto-mount
   - ☑ Make Permanent

3. **In Ubuntu**, add user to vboxsf group:
```bash
sudo usermod -aG vboxsf $USER
sudo reboot
```

4. **Access shared folder** at: `/media/sf_devops-shared`

---

## 🎨 Step 8: Customize Development Environment

### **Install ZSH and Oh-My-Zsh (Optional):**
```bash
# Install ZSH
sudo apt install -y zsh

# Install Oh-My-Zsh
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"

# Set as default shell
chsh -s $(which zsh)
```

### **Configure Git:**
```bash
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
git config --global init.defaultBranch main
```

### **Create Development Directory Structure:**
```bash
mkdir -p ~/Development/{projects,scripts,learning,tools}
mkdir -p ~/Development/learning/devops
```

---

## 📊 Step 9: VM Performance Optimization

### **VirtualBox Settings:**
- **RAM**: Use 50-75% of physical RAM for VM
- **CPU**: Assign 50-75% of CPU cores to VM
- **Video Memory**: 128MB for desktop usage
- **Hard Disk**: Use SSD if available

### **Ubuntu Performance:**
```bash
# Disable unnecessary services
sudo systemctl disable bluetooth
sudo systemctl disable cups-browsed

# Install preload for faster application loading
sudo apt install -y preload

# Configure swappiness (SSD optimization)
echo 'vm.swappiness=10' | sudo tee -a /etc/sysctl.conf
```

### **Resource Monitoring:**
```bash
# Check system resources
htop
free -h
df -h
```

---

## 🔍 Step 10: Testing and Validation

### **System Verification:**
```bash
# Check Ubuntu version
lsb_release -a

# Check memory and CPU
free -h
nproc

# Check network connectivity
ping -c 4 google.com

# Check disk space
df -h

# Test SSH (if enabled)
ssh localhost
```

### **Development Tools Test:**
```bash
# Test Git
git --version

# Test curl and wget
curl --version
wget --version

# Test development tools
gcc --version
make --version
```

---

## 🚨 Troubleshooting Common Issues

### **Issue: VM Won't Start**
**Solution:**
- Check if virtualization is enabled in BIOS
- Disable Hyper-V if enabled: `bcdedit /set hypervisorlaunchtype off`
- Restart Windows after making changes

### **Issue: Poor Performance**
**Solution:**
- Increase RAM allocation to VM
- Enable VT-x/AMD-V in BIOS
- Install Guest Additions
- Close unnecessary Windows applications

### **Issue: Network Not Working**
**Solution:**
- Check VM network settings (NAT mode)
- Restart network service: `sudo systemctl restart NetworkManager`
- Reset VM network adapter in VirtualBox

### **Issue: Shared Clipboard Not Working**
**Solution:**
- Install Guest Additions properly
- Restart VM after installation
- Check VirtualBox settings: Devices > Shared Clipboard > Bidirectional

### **Issue: Cannot Access Shared Folders**
**Solution:**
- Add user to vboxsf group: `sudo usermod -aG vboxsf $USER`
- Reboot Ubuntu VM
- Check folder permissions and mounting

---

## 📚 Best Practices

### **Backup and Snapshots:**
- **Create snapshots** before major changes
- **Export VM** regularly for backup
- **Document** your configuration changes

### **Security:**
- **Keep Ubuntu updated**: `sudo apt update && sudo apt upgrade`
- **Use strong passwords** for user accounts
- **Enable firewall**: `sudo ufw enable`
- **Regular security updates** and patches

### **Resource Management:**
- **Shutdown VM** when not in use
- **Monitor resource usage** on host machine
- **Close unnecessary applications** in VM
- **Use headless mode** for background tasks

---

## 🎯 Next Steps

Now that your Ubuntu VM is set up:

1. **Familiarize yourself** with the Ubuntu desktop environment
2. **Practice basic Linux commands** in terminal
3. **Install additional development tools** as needed
4. **Configure your preferred text editor** (vim, nano, or VS Code)
5. **Start learning Linux system administration** basics

Your development environment is now ready for the DevOps learning journey!

---

## 📖 Additional Resources

### **VirtualBox Documentation:**
- VirtualBox User Manual: https://www.virtualbox.org/manual/
- VirtualBox Forums: https://forums.virtualbox.org/

### **Ubuntu Resources:**
- Ubuntu Desktop Guide: https://help.ubuntu.com/
- Ubuntu Community Documentation: https://help.ubuntu.com/community/
- Ubuntu Forums: https://ubuntuforums.org/

### **Alternative Approaches:**
- WSL2 Setup: https://docs.microsoft.com/en-us/windows/wsl/
- VMware Workstation: https://www.vmware.com/products/workstation-pro.html
- Parallels Desktop (Mac): https://www.parallels.com/

---

📄 **File Path:** `/DevOps/01-Fundamentals_And_Environment_Setup/02-Development_Environment_Setup/Windows_To_Linux_VM_Setup/VM_Setup_Guide.md` 