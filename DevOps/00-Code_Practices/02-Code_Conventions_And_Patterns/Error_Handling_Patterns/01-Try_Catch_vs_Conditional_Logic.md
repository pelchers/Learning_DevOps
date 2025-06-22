# Try/Catch vs Conditional Logic in DevOps

## 📖 What This File Covers
Master the art of choosing between try/catch error handling and conditional logic in DevOps automation. Learn when each approach is appropriate and how to implement them effectively for resilient infrastructure code.

## 🎯 Learning Objectives
- Understand the fundamental difference between try/catch and conditional logic
- Learn when to use each approach in DevOps scenarios
- Implement robust error handling patterns for automation
- Create resilient deployment and infrastructure code
- Handle external dependencies and system failures gracefully

---

## 🔧 **Fundamental Differences**

### **Try/Catch: Handling Unexpected Failures**
```python
# Use try/catch for operations that might fail due to external factors
import requests
import subprocess
import boto3

def deploy_to_aws_with_error_handling():
    """Deploy application with comprehensive error handling"""
    
    try:
        # Network operations - can fail due to connectivity issues
        ec2 = boto3.client('ec2', region_name='us-east-1')
        
        # Create instance - can fail due to quotas, permissions, etc.
        response = ec2.run_instances(
            ImageId='ami-0c02fb55956c7d316',
            MinCount=1,
            MaxCount=1,
            InstanceType='t3.micro'
        )
        
        instance_id = response['Instances'][0]['InstanceId']
        print(f"✅ Created instance: {instance_id}")
        
        return {'success': True, 'instance_id': instance_id}
        
    except boto3.exceptions.Boto3Error as e:
        print(f"❌ AWS API Error: {e}")
        return {'success': False, 'error': f'AWS API failed: {e}'}
        
    except Exception as e:
        print(f"💥 Unexpected Error: {e}")
        return {'success': False, 'error': f'Unexpected error: {e}'}
```

### **Conditional Logic: Making Decisions Based on Data**
```python
# Use if/else for business logic and expected conditions
def validate_deployment_config(config):
    """Validate deployment configuration using conditional logic"""
    
    errors = []
    
    # Environment validation (expected business rule)
    if config.get('environment') not in ['dev', 'staging', 'prod']:
        errors.append(f"Invalid environment: {config.get('environment')}")
    
    # Resource allocation validation (business logic)
    replicas = config.get('replicas', 1)
    if replicas < 1:
        errors.append("Replicas must be at least 1")
    
    # Environment-specific rules (business logic)
    if config.get('environment') == 'prod':
        if replicas < 2:
            errors.append("Production requires at least 2 replicas for HA")
    
    return {
        'valid': len(errors) == 0,
        'errors': errors
    }
```

---

## 🎯 **When to Use Each Pattern**

### **Use Try/Catch When:**

**1. External API Calls**
```python
def check_service_health(service_url):
    """Check if external service is healthy"""
    try:
        response = requests.get(f"{service_url}/health", timeout=10)
        response.raise_for_status()
        
        health_data = response.json()
        return {
            'healthy': health_data.get('status') == 'healthy',
            'response_time': response.elapsed.total_seconds()
        }
        
    except requests.exceptions.Timeout:
        return {'healthy': False, 'error': 'Service timeout'}
    except requests.exceptions.ConnectionError:
        return {'healthy': False, 'error': 'Service unreachable'}
    except Exception as e:
        return {'healthy': False, 'error': f'Unexpected error: {e}'}
```

**2. File System Operations**
```python
def backup_configuration(config_dir, backup_dir):
    """Backup configuration files with error handling"""
    import shutil
    from datetime import datetime
    
    try:
        timestamp = datetime.now().strftime('%Y%m%d_%H%M%S')
        backup_path = f"{backup_dir}/config_backup_{timestamp}"
        
        # Copy configuration directory
        shutil.copytree(config_dir, backup_path)
        
        print(f"✅ Configuration backed up to: {backup_path}")
        return {'success': True, 'backup_path': backup_path}
        
    except FileNotFoundError:
        return {'success': False, 'error': 'Configuration directory not found'}
    except PermissionError:
        return {'success': False, 'error': 'Permission denied - check file permissions'}
    except Exception as e:
        return {'success': False, 'error': f'Backup failed: {e}'}
```

### **Use Conditional Logic When:**

**1. Configuration Validation**
```python
def validate_kubernetes_deployment(deployment_config):
    """Validate Kubernetes deployment configuration"""
    
    # Required fields validation
    if 'name' not in deployment_config:
        return {'valid': False, 'error': 'Deployment name is required'}
    
    if 'image' not in deployment_config:
        return {'valid': False, 'error': 'Container image is required'}
    
    # Environment-specific validation
    namespace = deployment_config.get('namespace', 'default')
    if namespace.startswith('prod-') and deployment_config.get('replicas', 1) < 2:
        return {'valid': False, 'error': 'Production deployments require at least 2 replicas'}
    
    return {'valid': True}
```

---

## 📊 **Decision Matrix**

| Scenario | Pattern | Reason |
|----------|---------|---------|
| **API calls to external services** | Try/Catch | Network can fail unpredictably |
| **File operations** | Try/Catch | Disk space, permissions can fail |
| **Environment validation** | Conditional Logic | Business rule validation |
| **Version comparison** | Conditional Logic | Expected data comparison |
| **Database connections** | Try/Catch | External dependency |
| **Deployment strategy selection** | Conditional Logic | Business rule based on data |

---

**💡 Key Takeaway**: Use **conditional logic** for decisions you control and **try/catch** for operations that external factors can break!
