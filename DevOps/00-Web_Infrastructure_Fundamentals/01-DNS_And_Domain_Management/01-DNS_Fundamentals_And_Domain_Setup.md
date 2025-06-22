# DNS Fundamentals and Domain Setup

## 📖 What This File Covers
Master Domain Name System (DNS) fundamentals, domain registration, DNS management, and real-world configuration examples. Learn how domain names translate to IP addresses and how to configure DNS for web applications.

## 🎯 Learning Objectives
- Understand DNS hierarchy and how domain resolution works
- Learn to register and manage domains effectively
- Configure DNS records for web applications and services
- Implement DNS best practices for performance and security
- Set up DNS for DevOps workflows and environments

---

## 🌐 **DNS Fundamentals**

### **How DNS Works - The Complete Journey**
```
User Types: https://myapp.company.com
     ↓
1. Browser Cache Check
     ↓ (if not found)
2. Operating System Cache Check
     ↓ (if not found)
3. Router/ISP DNS Cache Check
     ↓ (if not found)
4. Root Name Server Query
     ↓
5. TLD Name Server Query (.com)
     ↓
6. Authoritative Name Server Query (company.com)
     ↓
7. DNS Record Returned (A, AAAA, CNAME, etc.)
     ↓
8. IP Address: 203.0.113.45
     ↓
9. Browser Connects to Server
```

### **DNS Record Types Explained**

#### **A Record (IPv4 Address)**
```dns
; Basic A Record
www.company.com.    IN    A    203.0.113.45
api.company.com.    IN    A    203.0.113.46
blog.company.com.   IN    A    203.0.113.47

; Multiple A Records for Load Balancing
app.company.com.    IN    A    203.0.113.45
app.company.com.    IN    A    203.0.113.46
app.company.com.    IN    A    203.0.113.47
```

#### **CNAME Record (Canonical Name)**
```dns
; Subdomain Aliases
blog.company.com.      IN    CNAME    company.github.io.
docs.company.com.      IN    CNAME    company.gitbook.io.
status.company.com.    IN    CNAME    company.statuspage.io.

; Environment-specific subdomains
dev.company.com.       IN    CNAME    dev-server.company.com.
staging.company.com.   IN    CNAME    staging-server.company.com.
```

#### **MX Record (Mail Exchange)**
```dns
; Email Records
company.com.    IN    MX    10    mail.company.com.
company.com.    IN    MX    20    backup-mail.company.com.

; Corresponding A Records
mail.company.com.         IN    A     203.0.113.50
backup-mail.company.com.  IN    A     203.0.113.51
```

#### **TXT Record (Text/Verification)**
```dns
; SPF Record for Email Authentication
company.com.    IN    TXT    "v=spf1 include:_spf.google.com ~all"

; Domain Verification Records
company.com.    IN    TXT    "google-site-verification=abc123def456"
company.com.    IN    TXT    "facebook-domain-verification=xyz789"
```

---

## 🏗️ **Real-World DNS Configuration Examples**

### **Complete Production Setup for SaaS Application**
```dns
; Domain: techcorp.io
; Infrastructure: AWS + CloudFlare

; Root Domain
techcorp.io.                IN    A        104.21.35.42
www.techcorp.io.            IN    CNAME    techcorp.io.

; Application Subdomains
app.techcorp.io.            IN    A        203.0.113.45
api.techcorp.io.            IN    A        203.0.113.46
admin.techcorp.io.          IN    A        203.0.113.47

; Environment-Specific
dev.techcorp.io.            IN    A        10.0.1.100
staging.techcorp.io.        IN    A        10.0.2.100

; Services
status.techcorp.io.         IN    CNAME    techcorp.statuspage.io.
docs.techcorp.io.           IN    CNAME    techcorp.gitbook.io.

; Email Configuration
techcorp.io.                IN    MX       1    aspmx.l.google.com.
techcorp.io.                IN    MX       5    alt1.aspmx.l.google.com.

; Email Security
techcorp.io.                IN    TXT      "v=spf1 include:_spf.google.com ~all"

; Domain Verification
techcorp.io.                IN    TXT      "google-site-verification=abc123"
```

---

## 🛠️ **DNS Management with Infrastructure as Code**

### **Terraform DNS Configuration**
```hcl
# terraform/dns.tf
variable "domain_name" {
  description = "Primary domain name"
  type        = string
  default     = "techcorp.io"
}

# CloudFlare DNS Records
resource "cloudflare_record" "root_a" {
  zone_id = var.cloudflare_zone_id
  name    = var.domain_name
  value   = "203.0.113.45"
  type    = "A"
  proxied = true
}

resource "cloudflare_record" "app_subdomain" {
  zone_id = var.cloudflare_zone_id
  name    = "app"
  value   = "203.0.113.46"
  type    = "A"
  proxied = true
}

resource "cloudflare_record" "api_subdomain" {
  zone_id = var.cloudflare_zone_id
  name    = "api"
  value   = "203.0.113.47"
  type    = "A"
  proxied = true
}

# MX Records for Email
resource "cloudflare_record" "mx_primary" {
  zone_id  = var.cloudflare_zone_id
  name     = var.domain_name
  value    = "aspmx.l.google.com"
  type     = "MX"
  priority = 1
}

# SPF Record
resource "cloudflare_record" "spf" {
  zone_id = var.cloudflare_zone_id
  name    = var.domain_name
  value   = "v=spf1 include:_spf.google.com ~all"
  type    = "TXT"
}
```

### **Python DNS Management Script**
```python
#!/usr/bin/env python3
"""
Dynamic DNS Management for Feature Branches
"""

import requests
import os

class CloudFlareDNSManager:
    def __init__(self, api_token: str, zone_id: str):
        self.api_token = api_token
        self.zone_id = zone_id
        self.base_url = "https://api.cloudflare.com/client/v4"
        self.headers = {
            "Authorization": f"Bearer {api_token}",
            "Content-Type": "application/json"
        }
    
    def create_record(self, name: str, ip: str) -> dict:
        """Create DNS A record"""
        data = {
            "type": "A",
            "name": name,
            "content": ip,
            "ttl": 300
        }
        
        response = requests.post(
            f"{self.base_url}/zones/{self.zone_id}/dns_records",
            headers=self.headers,
            json=data
        )
        
        return response.json()
    
    def delete_record(self, record_id: str) -> bool:
        """Delete DNS record"""
        response = requests.delete(
            f"{self.base_url}/zones/{self.zone_id}/dns_records/{record_id}",
            headers=self.headers
        )
        
        return response.status_code == 200

# Usage
dns_manager = CloudFlareDNSManager(
    api_token=os.getenv("CLOUDFLARE_API_TOKEN"),
    zone_id=os.getenv("CLOUDFLARE_ZONE_ID")
)

# Create feature branch DNS
result = dns_manager.create_record("feature-auth.dev.techcorp.io", "10.0.1.123")
print(f"DNS record created: {result}")
```

---

## 🔧 **DNS Troubleshooting**

### **DNS Health Check Script**
```bash
#!/bin/bash
# DNS Health Check for techcorp.io

DOMAIN="techcorp.io"
SUBDOMAINS=("www" "app" "api" "admin")

echo "🔍 DNS Health Check for $DOMAIN"
echo "================================"

# Check each subdomain
for subdomain in "${SUBDOMAINS[@]}"; do
    hostname="$subdomain.$DOMAIN"
    echo "Checking $hostname..."
    
    result=$(dig +short $hostname A)
    if [[ -n "$result" ]]; then
        echo "✅ $hostname -> $result"
    else
        echo "❌ $hostname failed to resolve"
    fi
done

# Check MX records
echo ""
echo "Email (MX) Records:"
dig +short $DOMAIN MX

# Check nameservers
echo ""
echo "Nameservers:"
dig +short $DOMAIN NS
```

---

**💡 Key Takeaway**: Proper DNS management is the foundation of web infrastructure - master domain configuration, automate DNS provisioning, and implement monitoring!
