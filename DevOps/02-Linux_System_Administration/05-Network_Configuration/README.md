# 🌐 Network Configuration: Secure Linux Networking for DevOps

## 📖 What This Module Does
Master Linux networking and security configuration - critical skills for deploying and securing production systems. Learn to configure network interfaces, manage firewalls, set up secure remote access, and troubleshoot network issues.

## 🎯 Learning Objectives
By completing this module, you will:
- ✅ Configure network interfaces and routing
- ✅ Implement firewall rules and security policies
- ✅ Set up secure SSH access and key management
- ✅ Configure DNS and network services
- ✅ Troubleshoot network connectivity issues
- ✅ Implement network monitoring and security hardening

## 📋 Prerequisites
- Completed System Monitoring and Logging module
- Comfortable with Linux command line and system administration
- Basic understanding of networking concepts (IP, TCP/UDP, ports)
- Familiarity with system services and configuration files

---

## 📚 Module Structure

### **01-Network_Fundamentals/**
**Time Investment:** 2-3 days
- Network interface configuration
- IP addressing and routing
- DNS configuration and resolution
- Network service management

### **02-Firewall_Management/**
**Time Investment:** 2-3 days
- UFW (Uncomplicated Firewall) setup
- iptables configuration
- Port management and security rules
- Network access control

### **03-SSH_And_Remote_Access/**
**Time Investment:** 2-3 days
- SSH server configuration and hardening
- SSH key management and authentication
- Secure remote access setup
- VPN and tunneling basics

### **04-Network_Troubleshooting/**
**Time Investment:** 1-2 days
- Network diagnostic tools
- Connectivity troubleshooting
- Performance analysis
- Security incident response

---

## 🎯 Learning Path

### **Day 1-3: Network Configuration Mastery**
**Goal:** Configure and manage network interfaces

**Daily Practice:**
```bash
# Network interface management
ip addr show           # Show network interfaces
ip route show          # Show routing table
nmcli device status    # NetworkManager status
systemctl status networking   # Network service status
```

### **Day 4-6: Firewall and Security**
**Goal:** Implement comprehensive network security

**Daily Practice:**
```bash
# Firewall management
ufw status             # Check firewall status
iptables -L            # List firewall rules
ss -tuln               # Check listening ports
netstat -tuln          # Alternative port check
```

### **Day 7-9: SSH and Remote Access**
**Goal:** Set up secure remote access

**Daily Practice:**
```bash
# SSH configuration
ssh-keygen -t ed25519  # Generate SSH keys
ssh-copy-id user@host  # Copy keys to remote host
ssh -i key user@host   # Connect with specific key
scp file user@host:/path   # Secure file transfer
```

### **Day 10-11: Network Troubleshooting**
**Goal:** Diagnose and resolve network issues

**Daily Practice:**
- Simulate network problems
- Practice systematic diagnosis
- Use network monitoring tools
- Document troubleshooting procedures

---

## 🔧 Network Configuration Essentials

### **Network Interface Management**
```bash
# View network interfaces
ip addr show           # Modern interface info
ifconfig              # Traditional interface info
nmcli device status    # NetworkManager devices

# Configure interfaces
sudo ip addr add 192.168.1.100/24 dev eth0    # Add IP address
sudo ip route add default via 192.168.1.1     # Add default route
sudo ip link set eth0 up                      # Bring interface up

# NetworkManager configuration
nmcli connection show                          # Show connections
nmcli connection modify eth0 ipv4.addresses 192.168.1.100/24
nmcli connection up eth0                       # Activate connection
```

### **DNS Configuration**
```bash
# DNS settings
cat /etc/resolv.conf   # View DNS servers
nslookup google.com    # DNS lookup
dig google.com         # Advanced DNS lookup
host google.com        # Simple DNS lookup

# Configure DNS
echo "nameserver 8.8.8.8" | sudo tee -a /etc/resolv.conf
systemd-resolve --status                       # systemd DNS status
```

### **Network Services**
```bash
# Service management
systemctl status networking                    # Network service
systemctl restart networking                   # Restart networking
systemctl status systemd-networkd             # systemd networking
systemctl status NetworkManager               # NetworkManager service
```

---

## 🔥 Firewall Management

### **UFW (Uncomplicated Firewall)**
```bash
# Basic UFW commands
sudo ufw status        # Check firewall status
sudo ufw enable        # Enable firewall
sudo ufw disable       # Disable firewall

# Allow/deny rules
sudo ufw allow 22      # Allow SSH
sudo ufw allow 80      # Allow HTTP
sudo ufw allow 443     # Allow HTTPS
sudo ufw deny 23       # Deny Telnet

# Advanced rules
sudo ufw allow from 192.168.1.0/24            # Allow from subnet
sudo ufw allow out 53                         # Allow outgoing DNS
sudo ufw limit ssh                            # Rate limit SSH

# Application profiles
sudo ufw app list                             # List available profiles
sudo ufw allow 'Apache Full'                  # Allow Apache
```

### **iptables Configuration**
```bash
# View current rules
sudo iptables -L       # List rules
sudo iptables -L -n    # List with numeric output
sudo iptables -L -v    # Verbose output

# Basic rules
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT    # Allow SSH
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT    # Allow HTTP
sudo iptables -A INPUT -j DROP                        # Drop all other

# Save/restore rules
sudo iptables-save > /etc/iptables/rules.v4           # Save rules
sudo iptables-restore < /etc/iptables/rules.v4        # Restore rules
```

---

## 🔐 SSH Configuration and Security

### **SSH Server Configuration**
```bash
# SSH configuration file
sudo nano /etc/ssh/sshd_config

# Key security settings:
# Port 2222                    # Change default port
# PermitRootLogin no           # Disable root login
# PasswordAuthentication no    # Disable password auth
# PubkeyAuthentication yes     # Enable key auth
# AllowUsers username          # Limit users

# Restart SSH service
sudo systemctl restart ssh
sudo systemctl status ssh
```

### **SSH Key Management**
```bash
# Generate SSH keys
ssh-keygen -t ed25519 -C "your-email@example.com"     # Ed25519 (recommended)
ssh-keygen -t rsa -b 4096 -C "your-email@example.com" # RSA 4096-bit

# Copy keys to remote server
ssh-copy-id username@remote-server                    # Copy public key
ssh-copy-id -i ~/.ssh/id_ed25519.pub user@host       # Specific key

# SSH agent for key management
eval "$(ssh-agent -s)"                               # Start SSH agent
ssh-add ~/.ssh/id_ed25519                            # Add key to agent
ssh-add -l                                           # List loaded keys
```

### **Secure SSH Usage**
```bash
# Connect with specific key
ssh -i ~/.ssh/id_ed25519 user@host

# SSH tunneling
ssh -L 8080:localhost:80 user@host                   # Local port forwarding
ssh -R 8080:localhost:80 user@host                   # Remote port forwarding
ssh -D 1080 user@host                               # SOCKS proxy

# Secure file transfer
scp file.txt user@host:/remote/path                  # Copy file
rsync -avz local/ user@host:/remote/                 # Sync directories
```

---

## 📊 Progress Checkpoints

### **Network Configuration Mastery** ✅
- [ ] Can configure network interfaces and routing
- [ ] Understands DNS configuration and resolution
- [ ] Can manage network services effectively
- [ ] Knows how to troubleshoot connectivity issues

### **Firewall and Security** ✅
- [ ] Can configure UFW and iptables firewalls
- [ ] Understands port management and security rules
- [ ] Can implement network access controls
- [ ] Knows how to monitor for security threats

### **SSH and Remote Access** ✅
- [ ] Can configure secure SSH servers
- [ ] Manages SSH keys and authentication effectively
- [ ] Can set up secure remote access solutions
- [ ] Understands VPN and tunneling concepts

### **Network Troubleshooting** ✅
- [ ] Can diagnose network connectivity problems
- [ ] Uses network diagnostic tools effectively
- [ ] Can analyze network performance issues
- [ ] Knows how to respond to security incidents

---

## 🔧 Network Troubleshooting Quick Reference

### **Connectivity Issues**
```bash
# Basic connectivity tests
ping google.com                    # Test internet connectivity
ping 192.168.1.1                  # Test gateway connectivity
traceroute google.com             # Trace network path
mtr google.com                     # Real-time traceroute

# Interface diagnostics
ip addr show                       # Show interface configuration
ip link show                       # Show interface status
ethtool eth0                       # Show interface details
```

### **DNS Problems**
```bash
# DNS diagnostics
nslookup google.com               # Basic DNS lookup
dig google.com                    # Detailed DNS lookup
host google.com                   # Simple DNS lookup
cat /etc/resolv.conf              # Check DNS configuration
```

### **Port and Service Issues**
```bash
# Port diagnostics
ss -tuln                          # Show listening ports
netstat -tuln                     # Alternative port listing
nc -zv host 80                    # Test specific port
telnet host 80                    # Interactive port test
```

### **Firewall Diagnostics**
```bash
# Firewall status
ufw status verbose                # UFW detailed status
iptables -L -n -v                 # iptables rules with stats
iptables -t nat -L                # NAT table rules
```

---

## ➡️ Next Steps

**You're ready for the next module when you can:**
- [ ] Configure network interfaces and services confidently
- [ ] Implement comprehensive firewall security
- [ ] Set up secure SSH access and key management
- [ ] Troubleshoot complex network issues
- [ ] Monitor network security and performance
- [ ] Document network configurations and procedures

**Continue to:**
1. **06-Shell_Scripting_Automation** - Automate network management tasks
2. **Module 07: Infrastructure as Code** - Network automation with Terraform
3. **Module 10: Security and Compliance** - Advanced network security

---

📄 **File Path:** `/DevOps/02-Linux_System_Administration/05-Network_Configuration/README.md` 