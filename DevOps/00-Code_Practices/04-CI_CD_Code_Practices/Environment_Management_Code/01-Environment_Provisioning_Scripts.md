# Environment Provisioning Scripts

## 📖 What This File Covers
Master environment provisioning patterns for CI/CD pipelines. Learn how to automate environment creation, configuration, and teardown for consistent development, testing, and production environments.

## 🎯 Learning Objectives
- Automate environment provisioning with Infrastructure as Code
- Create dynamic environments for feature branches
- Implement environment cleanup and resource management
- Design environment configuration management
- Handle environment dependencies and data seeding

---

## 🏗️ **Dynamic Environment Provisioning**

### **Feature Branch Environment Creator**
```python
#!/usr/bin/env python3
"""
Dynamic Environment Provisioning Script
Creates isolated environments for feature branches
"""

import os
import subprocess
import yaml
import time
from typing import Dict, Any, List
from datetime import datetime, timedelta

class EnvironmentProvisioner:
    """Manage dynamic environment provisioning"""
    
    def __init__(self, config_file: str = "environments.yaml"):
        self.config = self._load_config(config_file)
        self.environments = {}
        
    def _load_config(self, config_file: str) -> Dict[str, Any]:
        """Load environment configuration"""
        with open(config_file, 'r') as f:
            return yaml.safe_load(f)
    
    def create_environment(self, branch_name: str, environment_type: str = "feature") -> Dict[str, Any]:
        """Create a new environment for the specified branch"""
        
        env_name = self._sanitize_name(branch_name)
        
        print(f"🚀 Creating {environment_type} environment for branch: {branch_name}")
        print(f"   Environment name: {env_name}")
        
        env_config = {
            'name': env_name,
            'branch': branch_name,
            'type': environment_type,
            'created_at': datetime.now().isoformat(),
            'namespace': f"{env_name}-{environment_type}",
            'domain': f"{env_name}.{self.config['base_domain']}",
            'status': 'creating'
        }
        
        try:
            # Create Kubernetes namespace
            self._create_namespace(env_config['namespace'])
            
            # Deploy database
            db_config = self._deploy_database(env_config)
            env_config['database'] = db_config
            
            # Deploy application
            app_config = self._deploy_application(env_config)
            env_config['application'] = app_config
            
            # Configure ingress
            ingress_config = self._configure_ingress(env_config)
            env_config['ingress'] = ingress_config
            
            env_config['status'] = 'ready'
            self.environments[env_name] = env_config
            
            print(f"✅ Environment ready!")
            print(f"   URL: https://{env_config['domain']}")
            
            return env_config
            
        except Exception as e:
            print(f"❌ Environment creation failed: {e}")
            self._cleanup_environment(env_name)
            raise
    
    def _sanitize_name(self, name: str) -> str:
        """Sanitize name for Kubernetes resources"""
        sanitized = name.lower().replace('/', '-').replace('_', '-')
        sanitized = ''.join(c for c in sanitized if c.isalnum() or c == '-')
        return sanitized[:50]
    
    def _create_namespace(self, namespace: str):
        """Create Kubernetes namespace"""
        print(f"📁 Creating namespace: {namespace}")
        
        namespace_yaml = f"""
apiVersion: v1
kind: Namespace
metadata:
  name: {namespace}
  labels:
    type: dynamic-environment
"""
        
        process = subprocess.Popen(['kubectl', 'apply', '-f', '-'], 
                                 stdin=subprocess.PIPE)
        process.communicate(input=namespace_yaml.encode())
        
        if process.returncode != 0:
            raise Exception(f"Failed to create namespace {namespace}")
    
    def _deploy_database(self, env_config: Dict[str, Any]) -> Dict[str, Any]:
        """Deploy database for the environment"""
        print("🗄️  Deploying database...")
        
        namespace = env_config['namespace']
        
        db_yaml = f"""
apiVersion: apps/v1
kind: Deployment
metadata:
  name: postgres
  namespace: {namespace}
spec:
  replicas: 1
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - name: postgres
        image: postgres:13
        env:
        - name: POSTGRES_DB
          value: testdb
        - name: POSTGRES_USER
          value: app_user
        - name: POSTGRES_PASSWORD
          value: temp_password
        ports:
        - containerPort: 5432
---
apiVersion: v1
kind: Service
metadata:
  name: postgres
  namespace: {namespace}
spec:
  selector:
    app: postgres
  ports:
  - port: 5432
"""
        
        process = subprocess.Popen(['kubectl', 'apply', '-f', '-'], 
                                 stdin=subprocess.PIPE)
        process.communicate(input=db_yaml.encode())
        
        return {
            'type': 'postgresql',
            'host': f"postgres.{namespace}.svc.cluster.local",
            'port': 5432
        }
    
    def _deploy_application(self, env_config: Dict[str, Any]) -> Dict[str, Any]:
        """Deploy application to the environment"""
        print("🚀 Deploying application...")
        
        namespace = env_config['namespace']
        branch = env_config['branch']
        
        app_yaml = f"""
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app
  namespace: {namespace}
spec:
  replicas: 1
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: app
        image: myapp:latest
        ports:
        - containerPort: 8080
        env:
        - name: ENVIRONMENT
          value: {env_config['type']}
        - name: BRANCH
          value: {branch}
---
apiVersion: v1
kind: Service
metadata:
  name: app
  namespace: {namespace}
spec:
  selector:
    app: myapp
  ports:
  - port: 80
    targetPort: 8080
"""
        
        process = subprocess.Popen(['kubectl', 'apply', '-f', '-'], 
                                 stdin=subprocess.PIPE)
        process.communicate(input=app_yaml.encode())
        
        return {
            'replicas': 1,
            'service': f"app.{namespace}.svc.cluster.local"
        }
    
    def _configure_ingress(self, env_config: Dict[str, Any]) -> Dict[str, Any]:
        """Configure ingress for external access"""
        print("🌐 Configuring ingress...")
        
        namespace = env_config['namespace']
        domain = env_config['domain']
        
        ingress_yaml = f"""
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
  namespace: {namespace}
spec:
  rules:
  - host: {domain}
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: app
            port:
              number: 80
"""
        
        process = subprocess.Popen(['kubectl', 'apply', '-f', '-'], 
                                 stdin=subprocess.PIPE)
        process.communicate(input=ingress_yaml.encode())
        
        return {
            'domain': domain,
            'url': f"https://{domain}"
        }
    
    def cleanup_environment(self, env_name: str) -> bool:
        """Clean up an environment"""
        print(f"🧹 Cleaning up environment: {env_name}")
        
        if env_name not in self.environments:
            print(f"⚠️  Environment {env_name} not found")
            return False
        
        env_config = self.environments[env_name]
        
        try:
            cmd = ['kubectl', 'delete', 'namespace', env_config['namespace']]
            subprocess.run(cmd, check=True)
            
            del self.environments[env_name]
            
            print(f"✅ Environment {env_name} cleaned up")
            return True
            
        except Exception as e:
            print(f"❌ Failed to cleanup environment {env_name}: {e}")
            return False
    
    def _cleanup_environment(self, env_name: str):
        """Internal cleanup method"""
        try:
            self.cleanup_environment(env_name)
        except:
            pass
    
    def list_environments(self) -> List[Dict[str, Any]]:
        """List all managed environments"""
        return list(self.environments.values())
```

---

## ⚙️ **Configuration Management**

### **Environment Configuration Template**
```yaml
# environments.yaml
base_domain: "dev.company.com"

templates:
  feature:
    replicas: 1
    resources:
      requests:
        memory: "256Mi"
        cpu: "250m"
      limits:
        memory: "512Mi"
        cpu: "500m"
    database:
      size: "1Gi"
      type: "postgresql"

  staging:
    replicas: 2
    resources:
      requests:
        memory: "512Mi" 
        cpu: "500m"
      limits:
        memory: "1Gi"
        cpu: "1000m"
    database:
      size: "5Gi"
      type: "postgresql"

  production:
    replicas: 3
    resources:
      requests:
        memory: "1Gi"
        cpu: "1000m"
      limits:
        memory: "2Gi"
        cpu: "2000m"
    database:
      size: "20Gi"
      type: "postgresql"
      high_availability: true
```

---

## 🔄 **GitHub Actions Integration**

### **Automated Environment Management**
```yaml
# .github/workflows/environment-management.yml
name: Environment Management

on:
  pull_request:
    types: [opened, synchronize, closed]

jobs:
  create-feature-environment:
    name: Create Feature Environment
    runs-on: ubuntu-latest
    if: github.event_name == 'pull_request' && github.event.action != 'closed'
    
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4
      
      - name: Create Environment
        env:
          KUBE_CONFIG: ${{ secrets.KUBE_CONFIG }}
        run: |
          echo "$KUBE_CONFIG" | base64 -d > ~/.kube/config
          python scripts/provision-environment.py create \
            --branch "${{ github.head_ref }}" \
            --type feature

  cleanup-feature-environment:
    name: Cleanup Feature Environment
    runs-on: ubuntu-latest
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    
    steps:
      - name: Cleanup Environment
        env:
          KUBE_CONFIG: ${{ secrets.KUBE_CONFIG }}
        run: |
          echo "$KUBE_CONFIG" | base64 -d > ~/.kube/config
          python scripts/provision-environment.py cleanup \
            --environment "${{ github.head_ref }}"
```

---

**💡 Key Takeaway**: Automated environment provisioning enables fast feedback loops, isolated testing, and efficient resource utilization in CI/CD pipelines!
