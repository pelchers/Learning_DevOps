# Environment-Specific Configuration Patterns

## 📖 What This File Covers
Master configuration management patterns for DevOps environments. Learn how to handle environment-specific settings, secrets management, and configuration deployment across development, staging, and production environments.

## 🎯 Learning Objectives
- Implement environment-specific configuration patterns
- Manage secrets and sensitive data securely
- Create scalable configuration deployment strategies
- Handle configuration validation and rollback
- Use configuration as code principles

---

## 🏗️ **Configuration Hierarchy Patterns**

### **Multi-Environment Configuration Structure**
```python
# config/base.py - Base configuration shared across all environments
class BaseConfig:
    """Base configuration with common settings"""
    
    # Application settings
    APP_NAME = "DevOps Application"
    APP_VERSION = "1.0.0"
    
    # Database settings (will be overridden per environment)
    DATABASE_POOL_SIZE = 10
    DATABASE_TIMEOUT = 30
    
    # Feature flags (can be overridden per environment)
    FEATURE_NEW_DASHBOARD = False
    FEATURE_ADVANCED_METRICS = False
    
    @classmethod
    def get_config_for_environment(cls, environment):
        """Get configuration class for specific environment"""
        config_map = {
            'development': DevelopmentConfig,
            'staging': StagingConfig,
            'production': ProductionConfig
        }
        
        if environment not in config_map:
            raise ValueError(f"Unknown environment: {environment}")
        
        return config_map[environment]

# config/development.py
class DevelopmentConfig(BaseConfig):
    """Development environment configuration"""
    
    ENVIRONMENT = "development"
    DEBUG = True
    
    # Development database (local)
    DATABASE_URL = "postgresql://dev_user:dev_pass@localhost:5432/myapp_dev"
    DATABASE_POOL_SIZE = 5
    
    # Development-specific features
    FEATURE_NEW_DASHBOARD = True
    FEATURE_DEBUG_TOOLBAR = True
    
    # Development logging (more verbose)
    LOG_LEVEL = "DEBUG"

# config/production.py
class ProductionConfig(BaseConfig):
    """Production environment configuration"""
    
    ENVIRONMENT = "production"
    DEBUG = False
    
    # Production database (high availability)
    DATABASE_URL = os.getenv('PRODUCTION_DATABASE_URL')
    DATABASE_POOL_SIZE = 20
    
    # Production features (stable only)
    FEATURE_NEW_DASHBOARD = False
    FEATURE_ADVANCED_METRICS = True
    
    # Production logging (optimized)
    LOG_LEVEL = "WARNING"
    
    # Production security
    SECURE_COOKIES = True
    CSRF_PROTECTION = True
```

---

## 🔐 **Secrets Management Patterns**

### **Environment Variable Configuration**
```python
import os
from dataclasses import dataclass, field

@dataclass
class SecureConfig:
    """Configuration with secure secret handling"""
    
    # Database credentials
    db_host: str = field(default_factory=lambda: os.getenv('DB_HOST', 'localhost'))
    db_user: str = field(default_factory=lambda: os.getenv('DB_USER', ''))
    db_password: str = field(default_factory=lambda: os.getenv('DB_PASSWORD', ''))
    
    # API keys
    api_key: str = field(default_factory=lambda: os.getenv('API_KEY', ''))
    jwt_secret: str = field(default_factory=lambda: os.getenv('JWT_SECRET', ''))
    
    def validate_secrets(self) -> None:
        """Validate that required secrets are present"""
        required_secrets = {
            'db_password': 'Database password is required',
            'api_key': 'API key is required',
            'jwt_secret': 'JWT secret is required'
        }
        
        missing_secrets = []
        
        for secret_name, error_message in required_secrets.items():
            secret_value = getattr(self, secret_name)
            if not secret_value or secret_value.strip() == '':
                missing_secrets.append(error_message)
        
        if missing_secrets:
            raise ValueError(f"Missing required secrets: {', '.join(missing_secrets)}")
    
    @property
    def database_url(self) -> str:
        """Construct database URL from components"""
        return f"postgresql://{self.db_user}:{self.db_password}@{self.db_host}/{self.db_name}"
```

---

## 📋 **Configuration Deployment Patterns**

### **Kubernetes ConfigMaps and Secrets**
```yaml
# kubernetes/configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: myapp-config
  namespace: production
data:
  APP_NAME: "MyApp Production"
  ENVIRONMENT: "production"
  LOG_LEVEL: "INFO"
  WORKER_PROCESSES: "8"
  FEATURE_NEW_DASHBOARD: "false"

---
# kubernetes/secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: myapp-secrets
  namespace: production
type: Opaque
data:
  DATABASE_URL: <base64-encoded-database-url>
  API_KEY: <base64-encoded-api-key>
  JWT_SECRET: <base64-encoded-jwt-secret>
```

### **Configuration Validation Script**
```python
import os
import yaml
import subprocess

class ConfigurationDeployer:
    """Deploy and validate configuration across environments"""
    
    def __init__(self, environment: str):
        self.environment = environment
    
    def validate_configuration(self) -> bool:
        """Validate configuration before deployment"""
        print(f"🔍 Validating configuration for {self.environment}...")
        
        # Validate environment-specific requirements
        if self.environment == 'production':
            return self._validate_production_config()
        
        return True
    
    def _validate_production_config(self) -> bool:
        """Additional validation for production environment"""
        config_file = f"config/{self.environment}/app.yaml"
        
        if os.path.exists(config_file):
            with open(config_file, 'r') as f:
                config = yaml.safe_load(f)
            
            # Validate production settings
            if config.get('DEBUG', False):
                print("❌ DEBUG mode must be disabled in production")
                return False
            
            if not config.get('SECURE_COOKIES', False):
                print("❌ SECURE_COOKIES must be enabled in production")
                return False
        
        print("✅ Production configuration validation passed")
        return True
    
    def deploy_configuration(self) -> bool:
        """Deploy configuration to target environment"""
        print(f"🚀 Deploying configuration to {self.environment}...")
        
        try:
            # Deploy ConfigMap
            subprocess.run([
                'kubectl', 'apply', '-f', f"kubernetes/{self.environment}/configmap.yaml"
            ], check=True)
            
            # Deploy Secret
            subprocess.run([
                'kubectl', 'apply', '-f', f"kubernetes/{self.environment}/secret.yaml"
            ], check=True)
            
            print("✅ Configuration deployed successfully")
            return True
            
        except subprocess.CalledProcessError as e:
            print(f"❌ Deployment failed: {e}")
            return False
```

---

**💡 Key Takeaway**: Great configuration management enables reliable, secure, and scalable deployments across all environments while maintaining proper secrets handling!
