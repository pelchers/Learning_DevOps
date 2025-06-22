# IP Addressing and Subnetting

## 📖 What This File Covers
Master IP addressing fundamentals, subnetting, CIDR notation, and network design patterns. Learn how to design and configure networks for web applications, cloud infrastructure, and DevOps environments.

## 🎯 Learning Objectives
- Understand IPv4 and IPv6 addressing schemes
- Master subnetting and CIDR notation
- Design network architectures for web applications
- Configure VPCs and subnets in cloud environments
- Implement network security through proper segmentation

---

## 🌐 **IP Addressing Fundamentals**

### **IPv4 Address Classes and Private Ranges**
```
Class A: 1.0.0.0    - 126.255.255.255  (Private: 10.0.0.0/8)
Class B: 128.0.0.0  - 191.255.255.255  (Private: 172.16.0.0/12)
Class C: 192.0.0.0  - 223.255.255.255  (Private: 192.168.0.0/16)

Reserved Ranges:
- Loopback:     127.0.0.0/8     (127.0.0.1)
- Link-Local:   169.254.0.0/16  (APIPA)
- Multicast:    224.0.0.0/4
```

### **CIDR Notation and Subnet Masks**
```
CIDR    Subnet Mask        Hosts    Networks
/8      255.0.0.0         16,777,214    1
/16     255.255.0.0       65,534        256
/24     255.255.255.0     254           65,536
/25     255.255.255.128   126           131,072
/26     255.255.255.192   62            262,144
/27     255.255.255.224   30            524,288
/28     255.255.255.240   14            1,048,576
```

---

## 🏗️ **Real-World Network Design**

### **Three-Tier Web Application Architecture**
```
Production Network: 10.0.0.0/16

PUBLIC SUBNET (DMZ) - 10.0.1.0/24
├── Load Balancer: 10.0.1.10
├── Load Balancer: 10.0.1.11
└── Bastion Host:  10.0.1.50

PRIVATE SUBNET (WEB TIER) - 10.0.10.0/24
├── Web Server: 10.0.10.10
├── Web Server: 10.0.10.11
└── Web Server: 10.0.10.12

PRIVATE SUBNET (APP TIER) - 10.0.20.0/24
├── App Server: 10.0.20.10
├── App Server: 10.0.20.11
└── App Server: 10.0.20.12

PRIVATE SUBNET (DB TIER) - 10.0.30.0/24
├── DB Primary: 10.0.30.10
├── DB Replica: 10.0.30.11
└── DB Backup:  10.0.30.12
```

### **Multi-Region Cloud Infrastructure**
```
US-EAST-1 (Virginia) - Primary Region
VPC: 10.1.0.0/16
├── AZ-1a: 10.1.1.0/24
│   ├── Public:  10.1.1.0/26   (10.1.1.1-62)
│   ├── Private: 10.1.1.64/26  (10.1.1.65-126)
│   └── DB:      10.1.1.128/27 (10.1.1.129-158)
└── AZ-1b: 10.1.2.0/24
    ├── Public:  10.1.2.0/26
    ├── Private: 10.1.2.64/26
    └── DB:      10.1.2.128/27

US-WEST-2 (Oregon) - Secondary Region
VPC: 10.2.0.0/16
├── AZ-2a: 10.2.1.0/24
└── AZ-2b: 10.2.2.0/24

EU-WEST-1 (Ireland) - European Region
VPC: 10.3.0.0/16
├── AZ-3a: 10.3.1.0/24
└── AZ-3b: 10.3.2.0/24
```

---

## 🛠️ **Terraform VPC Configuration**

### **Complete VPC Setup**
```hcl
# terraform/vpc.tf
variable "vpc_cidr" {
  default = "10.0.0.0/16"
}

variable "availability_zones" {
  default = ["us-east-1a", "us-east-1b"]
}

# Main VPC
resource "aws_vpc" "main" {
  cidr_block           = var.vpc_cidr
  enable_dns_hostnames = true
  enable_dns_support   = true
  
  tags = {
    Name = "production-vpc"
  }
}

# Public Subnets
resource "aws_subnet" "public" {
  count = length(var.availability_zones)
  
  vpc_id                  = aws_vpc.main.id
  cidr_block              = cidrsubnet(var.vpc_cidr, 8, count.index + 1)
  availability_zone       = var.availability_zones[count.index]
  map_public_ip_on_launch = true
  
  tags = {
    Name = "public-${count.index + 1}"
    Type = "Public"
  }
}

# Private Subnets - Web Tier
resource "aws_subnet" "private_web" {
  count = length(var.availability_zones)
  
  vpc_id            = aws_vpc.main.id
  cidr_block        = cidrsubnet(var.vpc_cidr, 8, count.index + 10)
  availability_zone = var.availability_zones[count.index]
  
  tags = {
    Name = "web-${count.index + 1}"
    Tier = "Web"
  }
}

# Private Subnets - Database Tier
resource "aws_subnet" "private_db" {
  count = length(var.availability_zones)
  
  vpc_id            = aws_vpc.main.id
  cidr_block        = cidrsubnet(var.vpc_cidr, 8, count.index + 30)
  availability_zone = var.availability_zones[count.index]
  
  tags = {
    Name = "db-${count.index + 1}"
    Tier = "Database"
  }
}

# Internet Gateway
resource "aws_internet_gateway" "main" {
  vpc_id = aws_vpc.main.id
  
  tags = {
    Name = "production-igw"
  }
}

# NAT Gateway
resource "aws_eip" "nat" {
  count = length(var.availability_zones)
  domain = "vpc"
  
  tags = {
    Name = "nat-eip-${count.index + 1}"
  }
}

resource "aws_nat_gateway" "main" {
  count = length(var.availability_zones)
  
  allocation_id = aws_eip.nat[count.index].id
  subnet_id     = aws_subnet.public[count.index].id
  
  tags = {
    Name = "nat-gateway-${count.index + 1}"
  }
}

# Route Tables
resource "aws_route_table" "public" {
  vpc_id = aws_vpc.main.id
  
  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.main.id
  }
  
  tags = {
    Name = "public-rt"
  }
}

resource "aws_route_table" "private" {
  count = length(var.availability_zones)
  vpc_id = aws_vpc.main.id
  
  route {
    cidr_block     = "0.0.0.0/0"
    nat_gateway_id = aws_nat_gateway.main[count.index].id
  }
  
  tags = {
    Name = "private-rt-${count.index + 1}"
  }
}
```

### **Security Groups**
```hcl
# Security Groups
resource "aws_security_group" "web" {
  name_prefix = "web-"
  vpc_id      = aws_vpc.main.id
  
  # HTTP from anywhere
  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  # HTTPS from anywhere
  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  # SSH from bastion
  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["10.0.1.0/24"]  # Public subnet
  }
  
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  tags = {
    Name = "web-sg"
  }
}

resource "aws_security_group" "database" {
  name_prefix = "db-"
  vpc_id      = aws_vpc.main.id
  
  # PostgreSQL from web tier
  ingress {
    from_port   = 5432
    to_port     = 5432
    protocol    = "tcp"
    cidr_blocks = [aws_subnet.private_web[0].cidr_block, 
                   aws_subnet.private_web[1].cidr_block]
  }
  
  tags = {
    Name = "database-sg"
  }
}
```

---

## 🔧 **Network Testing Script**

### **Connectivity Test**
```bash
#!/bin/bash
# network_test.sh

VPC_CIDR="10.0.0.0/16"
SUBNETS=("10.0.1.0/24" "10.0.10.0/24" "10.0.30.0/24")

echo "🔍 Network Connectivity Test"
echo "============================="

# Test gateways
for subnet in "${SUBNETS[@]}"; do
    network=$(echo $subnet | cut -d'/' -f1)
    gateway="${network%.*}.1"
    
    echo "Testing gateway $gateway for $subnet..."
    
    if ping -c 1 -W 2 $gateway >/dev/null 2>&1; then
        echo "✅ Gateway $gateway reachable"
    else
        echo "❌ Gateway $gateway unreachable"
    fi
done

# Test external connectivity
echo ""
echo "External Connectivity:"
external_hosts=("8.8.8.8" "1.1.1.1" "google.com")

for host in "${external_hosts[@]}"; do
    if ping -c 1 -W 3 $host >/dev/null 2>&1; then
        echo "✅ $host reachable"
    else
        echo "❌ $host unreachable"
    fi
done

# Test DNS
echo ""
echo "DNS Resolution:"
test_domains=("google.com" "github.com")

for domain in "${test_domains[@]}"; do
    if nslookup $domain 8.8.8.8 >/dev/null 2>&1; then
        echo "✅ $domain resolved"
    else
        echo "❌ $domain failed to resolve"
    fi
done

echo ""
echo "Network interface info:"
ip addr show | grep -E "inet " | grep -v "127.0.0.1"

echo ""
echo "Default route:"
ip route show default
```

### **IP Calculator Script**
```python
#!/usr/bin/env python3
"""
IP Address and Subnet Calculator
"""

import ipaddress

def calculate_subnet_info(network_str):
    """Calculate subnet information"""
    try:
        network = ipaddress.IPv4Network(network_str, strict=False)
        
        print(f"Network: {network}")
        print(f"Network Address: {network.network_address}")
        print(f"Broadcast Address: {network.broadcast_address}")
        print(f"Subnet Mask: {network.netmask}")
        print(f"Wildcard Mask: {network.hostmask}")
        print(f"Total Addresses: {network.num_addresses}")
        print(f"Usable Addresses: {network.num_addresses - 2}")
        print(f"First Usable: {network.network_address + 1}")
        print(f"Last Usable: {network.broadcast_address - 1}")
        
        print("\nFirst 10 usable addresses:")
        for i, addr in enumerate(network.hosts()):
            if i >= 10:
                break
            print(f"  {addr}")
            
    except ValueError as e:
        print(f"Error: {e}")

def subnet_network(network_str, new_prefix):
    """Subnet a network into smaller subnets"""
    try:
        network = ipaddress.IPv4Network(network_str)
        subnets = list(network.subnets(new_prefix=new_prefix))
        
        print(f"Subnetting {network} into /{new_prefix} networks:")
        print(f"Total subnets: {len(subnets)}")
        print()
        
        for i, subnet in enumerate(subnets[:10]):  # Show first 10
            print(f"Subnet {i+1}: {subnet}")
            print(f"  Range: {subnet.network_address} - {subnet.broadcast_address}")
            print(f"  Usable: {list(subnet.hosts())[0]} - {list(subnet.hosts())[-1]}")
            print()
            
    except ValueError as e:
        print(f"Error: {e}")

if __name__ == "__main__":
    print("IP Address Calculator")
    print("====================")
    
    # Example calculations
    print("1. VPC Network Information:")
    calculate_subnet_info("10.0.0.0/16")
    
    print("\n" + "="*50)
    print("2. Subnetting VPC into /24 networks:")
    subnet_network("10.0.0.0/16", 24)
```

---

**💡 Key Takeaway**: Proper IP addressing and network design provides the foundation for secure, scalable infrastructure - plan your address space carefully and implement proper segmentation!
