# DevOps Security Best Practices

## 📖 What This File Covers
Master security coding patterns essential for DevOps environments. Learn how to implement secure coding practices, manage secrets, handle authentication, and protect infrastructure from common vulnerabilities.

## 🎯 Learning Objectives
- Implement secure coding practices in DevOps automation
- Manage secrets and sensitive data securely
- Implement proper authentication and authorization
- Protect against common security vulnerabilities
- Create security-first infrastructure code

---

## 🔐 **Secrets Management Patterns**

### **Secure Secret Handling**
```python
# secure_secrets.py
import os
import base64
from cryptography.fernet import Fernet
from typing import Dict, Optional

class SecureSecretsManager:
    """Secure secrets management for DevOps applications"""
    
    def __init__(self, encryption_key: Optional[str] = None):
        """Initialize with optional encryption key"""
        if encryption_key:
            self.cipher_suite = Fernet(encryption_key.encode())
        else:
            key = os.getenv('ENCRYPTION_KEY')
            if not key:
                key = Fernet.generate_key()
                print("⚠️  Generated new encryption key - store it securely!")
            self.cipher_suite = Fernet(key if isinstance(key, bytes) else key.encode())
    
    def encrypt_secret(self, secret_value: str) -> str:
        """Encrypt a secret value"""
        try:
            encrypted_bytes = self.cipher_suite.encrypt(secret_value.encode())
            return base64.b64encode(encrypted_bytes).decode()
        except Exception as e:
            raise SecurityError(f"Failed to encrypt secret: {e}")
    
    def decrypt_secret(self, encrypted_secret: str) -> str:
        """Decrypt a secret value"""
        try:
            encrypted_bytes = base64.b64decode(encrypted_secret.encode())
            decrypted_bytes = self.cipher_suite.decrypt(encrypted_bytes)
            return decrypted_bytes.decode()
        except Exception as e:
            raise SecurityError(f"Failed to decrypt secret: {e}")
    
    def load_secrets_from_env(self, required_secrets: list) -> Dict[str, str]:
        """Load and validate required secrets from environment"""
        secrets = {}
        missing_secrets = []
        
        for secret_name in required_secrets:
            secret_value = os.getenv(secret_name)
            
            if not secret_value:
                missing_secrets.append(secret_name)
            else:
                if secret_name.endswith('_ENCRYPTED'):
                    try:
                        secret_value = self.decrypt_secret(secret_value)
                    except SecurityError:
                        missing_secrets.append(f"{secret_name} (decryption failed)")
                
                secrets[secret_name] = secret_value
        
        if missing_secrets:
            raise SecurityError(f"Missing required secrets: {', '.join(missing_secrets)}")
        
        return secrets
    
    def mask_secret_for_logging(self, secret: str, visible_chars: int = 4) -> str:
        """Mask secret for safe logging"""
        if len(secret) <= visible_chars:
            return '*' * len(secret)
        
        return secret[:visible_chars] + '*' * (len(secret) - visible_chars)

class SecurityError(Exception):
    """Custom security-related exception"""
    pass
```

---

## 🛡️ **Input Validation and Sanitization**

### **Secure Input Handling**
```python
# secure_input.py
import re
import html
from typing import Any, Dict, List

class InputValidator:
    """Secure input validation for DevOps applications"""
    
    PATTERNS = {
        'email': r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$',
        'hostname': r'^[a-zA-Z0-9]([a-zA-Z0-9\-]{0,61}[a-zA-Z0-9])?(\.[a-zA-Z0-9]([a-zA-Z0-9\-]{0,61}[a-zA-Z0-9])?)*$',
        'docker_image': r'^[a-z0-9]+(?:[._-][a-z0-9]+)*(?:/[a-z0-9]+(?:[._-][a-z0-9]+)*)*(?::[a-zA-Z0-9][a-zA-Z0-9._-]*)?$',
        'k8s_name': r'^[a-z0-9]([-a-z0-9]*[a-z0-9])?$',
        'version': r'^v?(\d+)\.(\d+)\.(\d+)(?:-([0-9A-Za-z-]+(?:\.[0-9A-Za-z-]+)*))?$'
    }
    
    def validate_kubernetes_name(self, name: str) -> bool:
        """Validate Kubernetes resource name"""
        if len(name) > 63:
            return False
        return bool(re.match(self.PATTERNS['k8s_name'], name))
    
    def validate_docker_image(self, image: str) -> bool:
        """Validate Docker image name"""
        return bool(re.match(self.PATTERNS['docker_image'], image))
    
    def sanitize_shell_input(self, input_string: str) -> str:
        """Sanitize input for shell commands"""
        dangerous_chars = [';', '|', '&', '$', '`', '(', ')', '{', '}', '[', ']']
        
        sanitized = input_string
        for char in dangerous_chars:
            sanitized = sanitized.replace(char, '')
        
        return re.sub(r'[^a-zA-Z0-9\-_./]', '', sanitized)
    
    def validate_deployment_config(self, config: Dict[str, Any]) -> Dict[str, Any]:
        """Validate deployment configuration"""
        errors = []
        
        # Required fields
        required_fields = ['service_name', 'image', 'environment']
        for field in required_fields:
            if field not in config:
                errors.append(f"Missing required field: {field}")
        
        # Validate service name
        if 'service_name' in config:
            if not self.validate_kubernetes_name(config['service_name']):
                errors.append("Invalid service name format")
        
        # Validate image
        if 'image' in config:
            if not self.validate_docker_image(config['image']):
                errors.append("Invalid Docker image format")
        
        # Validate environment
        if 'environment' in config:
            valid_environments = ['development', 'staging', 'production']
            if config['environment'] not in valid_environments:
                errors.append(f"Environment must be one of: {', '.join(valid_environments)}")
        
        return {
            'valid': len(errors) == 0,
            'errors': errors
        }
```

---

## 🔒 **Authentication and Authorization**

### **Role-Based Access Control**
```python
# rbac_system.py
from enum import Enum
from typing import List, Set
import jwt
import hashlib

class Permission(Enum):
    DEPLOY_CREATE = "deploy:create"
    DEPLOY_READ = "deploy:read"
    DEPLOY_ROLLBACK = "deploy:rollback"
    INFRA_READ = "infrastructure:read"
    INFRA_MODIFY = "infrastructure:modify"
    MONITOR_READ = "monitoring:read"

class Role:
    """Role definition with permissions"""
    
    def __init__(self, name: str, permissions: List[Permission]):
        self.name = name
        self.permissions = set(permissions)
    
    def has_permission(self, permission: Permission) -> bool:
        """Check if role has specific permission"""
        return permission in self.permissions

# Predefined roles
ROLES = {
    'developer': Role('developer', [
        Permission.DEPLOY_READ,
        Permission.INFRA_READ,
        Permission.MONITOR_READ
    ]),
    
    'devops_engineer': Role('devops_engineer', [
        Permission.DEPLOY_CREATE,
        Permission.DEPLOY_READ,
        Permission.DEPLOY_ROLLBACK,
        Permission.INFRA_READ,
        Permission.INFRA_MODIFY,
        Permission.MONITOR_READ
    ])
}

class User:
    """User with role and permissions"""
    
    def __init__(self, user_id: str, username: str, role_name: str):
        self.user_id = user_id
        self.username = username
        self.role = ROLES.get(role_name)
        
        if not self.role:
            raise SecurityError(f"Invalid role: {role_name}")
    
    def has_permission(self, permission: Permission) -> bool:
        """Check if user has specific permission"""
        return self.role.has_permission(permission)

class AuthenticationManager:
    """Manage authentication and authorization"""
    
    def __init__(self, secret_key: str):
        self.secret_key = secret_key
        self.users = {}
    
    def authenticate_user(self, username: str, password: str) -> str:
        """Authenticate user and return JWT token"""
        password_hash = hashlib.sha256(password.encode()).hexdigest()
        
        for user_data in self.users.values():
            user = user_data['user']
            if user.username == username and user_data['password_hash'] == password_hash:
                
                payload = {
                    'user_id': user.user_id,
                    'username': user.username,
                    'role': user.role.name,
                    'permissions': [p.value for p in user.role.permissions]
                }
                
                token = jwt.encode(payload, self.secret_key, algorithm='HS256')
                return token
        
        raise SecurityError("Invalid credentials")
```

---

## 🔍 **Security Logging and Monitoring**

### **Security Event Logging**
```python
# security_logging.py
import json
import logging
from datetime import datetime
from typing import Dict, Any

class SecurityLogger:
    """Specialized logger for security events"""
    
    def __init__(self, service_name: str):
        self.service_name = service_name
        self.logger = logging.getLogger(f"security.{service_name}")
    
    def log_authentication_attempt(self, username: str, success: bool, ip_address: str):
        """Log authentication attempt"""
        event_data = {
            'event_type': 'authentication',
            'username': username,
            'success': success,
            'ip_address': ip_address,
            'timestamp': datetime.utcnow().isoformat() + 'Z',
            'service': self.service_name
        }
        
        if success:
            self.logger.info(f"Authentication successful: {json.dumps(event_data)}")
        else:
            self.logger.warning(f"Authentication failed: {json.dumps(event_data)}")
    
    def log_security_violation(self, violation_type: str, details: Dict[str, Any]):
        """Log security violation"""
        event_data = {
            'event_type': 'security_violation',
            'violation_type': violation_type,
            'details': details,
            'timestamp': datetime.utcnow().isoformat() + 'Z',
            'service': self.service_name
        }
        
        self.logger.error(f"Security violation: {json.dumps(event_data)}")

class SecurityMonitor:
    """Monitor for security threats"""
    
    def __init__(self):
        self.failed_login_attempts = {}
        self.security_logger = SecurityLogger('security-monitor')
    
    def check_brute_force_attempt(self, ip_address: str, username: str) -> bool:
        """Check for brute force login attempts"""
        if ip_address not in self.failed_login_attempts:
            self.failed_login_attempts[ip_address] = 0
        
        self.failed_login_attempts[ip_address] += 1
        
        if self.failed_login_attempts[ip_address] >= 5:
            self.security_logger.log_security_violation(
                'brute_force_attempt',
                {
                    'ip_address': ip_address,
                    'username': username,
                    'attempt_count': self.failed_login_attempts[ip_address]
                }
            )
            return True
        
        return False
```

---

**💡 Key Takeaway**: Security in DevOps requires multiple layers - secure secret management, input validation, proper authentication/authorization, and comprehensive security monitoring!
