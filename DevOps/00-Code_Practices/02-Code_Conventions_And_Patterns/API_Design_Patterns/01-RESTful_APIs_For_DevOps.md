# RESTful APIs for DevOps

## 📖 What This File Covers
Master RESTful API design patterns for DevOps services and infrastructure management. Learn how to design APIs that are reliable, scalable, and follow best practices for automation and monitoring systems.

## 🎯 Learning Objectives
- Design RESTful APIs for DevOps services
- Implement proper HTTP status codes and error handling
- Create APIs for deployment, monitoring, and infrastructure management
- Handle authentication and authorization for DevOps workflows
- Design APIs that integrate well with CI/CD pipelines

---

## 🏗️ **DevOps API Design Fundamentals**

### **Resource-Based URL Design**
```python
# Flask example for DevOps API design
from flask import Flask, jsonify, request
from datetime import datetime
import uuid

app = Flask(__name__)

# ✅ Good: Resource-based URLs for DevOps operations
@app.route('/api/v1/deployments', methods=['GET'])
def list_deployments():
    """List all deployments with filtering"""
    environment = request.args.get('environment')
    status = request.args.get('status')
    
    deployments = [
        {
            'id': 'deploy-001',
            'service': 'api-gateway',
            'version': 'v1.2.3',
            'environment': 'production',
            'status': 'successful',
            'created_at': '2023-11-20T10:30:00Z',
            'duration_seconds': 120
        }
    ]
    
    return jsonify({
        'deployments': deployments,
        'total': len(deployments),
        'page': 1,
        'per_page': 50
    })

@app.route('/api/v1/deployments', methods=['POST'])
def create_deployment():
    """Create a new deployment"""
    data = request.get_json()
    
    # Validation
    required_fields = ['service', 'version', 'environment']
    for field in required_fields:
        if field not in data:
            return jsonify({
                'error': 'validation_error',
                'message': f'Missing required field: {field}',
                'field': field
            }), 400
    
    deployment = {
        'id': str(uuid.uuid4()),
        'service': data['service'],
        'version': data['version'],
        'environment': data['environment'],
        'status': 'pending',
        'created_at': datetime.utcnow().isoformat() + 'Z'
    }
    
    return jsonify(deployment), 201

@app.route('/api/v1/deployments/<deployment_id>/rollback', methods=['POST'])
def rollback_deployment(deployment_id):
    """Rollback a deployment"""
    data = request.get_json() or {}
    
    rollback_request = {
        'deployment_id': deployment_id,
        'rollback_id': str(uuid.uuid4()),
        'status': 'initiated',
        'requested_by': data.get('user', 'system'),
        'reason': data.get('reason', 'Manual rollback'),
        'initiated_at': datetime.utcnow().isoformat() + 'Z'
    }
    
    return jsonify(rollback_request), 202
```

---

## 🔒 **Authentication and Authorization Patterns**

### **API Key Authentication**
```python
from functools import wraps
import hashlib

class DevOpsAuthManager:
    """Authentication manager for DevOps APIs"""
    
    def __init__(self):
        self.api_keys = {
            'ci_cd_pipeline': {
                'key_hash': hashlib.sha256('pipeline_key_123'.encode()).hexdigest(),
                'permissions': ['deployments:create', 'deployments:read'],
                'environment': 'all'
            }
        }
    
    def validate_api_key(self, api_key: str) -> dict:
        """Validate API key and return permissions"""
        key_hash = hashlib.sha256(api_key.encode()).hexdigest()
        
        for service, config in self.api_keys.items():
            if config['key_hash'] == key_hash:
                return {
                    'service': service,
                    'permissions': config['permissions'],
                    'environment': config['environment']
                }
        
        return None

def require_api_key(auth_manager):
    """Decorator to require API key authentication"""
    def decorator(f):
        @wraps(f)
        def decorated_function(*args, **kwargs):
            auth_header = request.headers.get('Authorization')
            
            if not auth_header or not auth_header.startswith('Bearer '):
                return jsonify({'error': 'Missing authorization header'}), 401
            
            api_key = auth_header.split(' ')[1]
            auth_info = auth_manager.validate_api_key(api_key)
            
            if not auth_info:
                return jsonify({'error': 'Invalid API key'}), 401
            
            request.auth_info = auth_info
            return f(*args, **kwargs)
        
        return decorated_function
    return decorator
```

---

## 📊 **Error Handling Patterns**

### **Structured Error Responses**
```python
from enum import Enum

class ErrorCode(Enum):
    VALIDATION_ERROR = "validation_error"
    AUTHENTICATION_ERROR = "authentication_error"
    RESOURCE_NOT_FOUND = "resource_not_found"
    DEPLOYMENT_FAILED = "deployment_failed"

class APIError(Exception):
    """Custom API exception"""
    
    def __init__(self, error_code: ErrorCode, message: str, details: dict = None):
        self.error_code = error_code
        self.message = message
        self.details = details or {}
    
    def to_dict(self):
        return {
            'error': self.error_code.value,
            'message': self.message,
            'details': self.details,
            'timestamp': datetime.utcnow().isoformat() + 'Z'
        }

@app.errorhandler(APIError)
def handle_api_error(error: APIError):
    """Handle custom API errors"""
    status_codes = {
        ErrorCode.VALIDATION_ERROR: 400,
        ErrorCode.AUTHENTICATION_ERROR: 401,
        ErrorCode.RESOURCE_NOT_FOUND: 404,
        ErrorCode.DEPLOYMENT_FAILED: 409
    }
    
    status_code = status_codes.get(error.error_code, 500)
    return jsonify(error.to_dict()), status_code
```

---

## ⚡ **Async Operations Pattern**

### **Long-Running Operations**
```python
class AsyncOperationManager:
    """Manage long-running asynchronous operations"""
    
    def __init__(self):
        self.operations = {}
    
    def start_operation(self, operation_id: str, operation_func, *args):
        """Start an asynchronous operation"""
        operation_status = {
            'id': operation_id,
            'status': 'pending',
            'started_at': datetime.utcnow().isoformat() + 'Z',
            'progress': 0
        }
        
        self.operations[operation_id] = operation_status
        return operation_status
    
    def get_operation_status(self, operation_id: str):
        """Get status of an operation"""
        return self.operations.get(operation_id)

@app.route('/api/v1/deployments/async', methods=['POST'])
def create_async_deployment():
    """Create an asynchronous deployment"""
    data = request.get_json()
    operation_id = str(uuid.uuid4())
    
    operation_status = operation_manager.start_operation(
        operation_id,
        perform_deployment,
        data['service'], data['version']
    )
    
    return jsonify({
        'operation_id': operation_id,
        'status_url': f"/api/v1/operations/{operation_id}",
        **operation_status
    }), 202

@app.route('/api/v1/operations/<operation_id>', methods=['GET'])
def get_operation_status(operation_id):
    """Get status of an async operation"""
    status = operation_manager.get_operation_status(operation_id)
    
    if not status:
        raise APIError(
            ErrorCode.RESOURCE_NOT_FOUND,
            f"Operation {operation_id} not found"
        )
    
    return jsonify(status)
```

---

**💡 Key Takeaway**: Well-designed DevOps APIs provide reliable, secure interfaces for automation and infrastructure management!
