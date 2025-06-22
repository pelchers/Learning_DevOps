# Blue-Green Deployment Scripts

## 📖 What This File Covers
Master blue-green deployment patterns using scripts and automation. Learn how to implement zero-downtime deployments, automated rollbacks, and traffic switching mechanisms for reliable application updates.

## 🎯 Learning Objectives
- Implement blue-green deployment automation scripts
- Create traffic switching and health check mechanisms
- Design automated rollback strategies
- Handle database migrations in blue-green deployments
- Monitor deployment health and performance

---

## 🔄 **Blue-Green Deployment Architecture**

### **Core Deployment Script**
```python
#!/usr/bin/env python3
"""
Blue-Green Deployment Script for Kubernetes
"""

import subprocess
import time
import requests
from typing import Dict, Any
from datetime import datetime

class BlueGreenDeployer:
    """Blue-Green deployment manager"""
    
    def __init__(self, namespace: str, service_name: str):
        self.namespace = namespace
        self.service_name = service_name
        self.colors = ['blue', 'green']
        self.health_check_timeout = 300
    
    def get_current_active_color(self) -> str:
        """Get currently active deployment color"""
        try:
            cmd = [
                'kubectl', 'get', 'service', self.service_name,
                '-n', self.namespace,
                '-o', 'jsonpath={.spec.selector.version}'
            ]
            
            result = subprocess.run(cmd, capture_output=True, text=True, check=True)
            current_color = result.stdout.strip()
            
            return current_color if current_color in self.colors else 'blue'
                
        except subprocess.CalledProcessError:
            print("⚠️  Could not determine current active color, defaulting to blue")
            return 'blue'
    
    def get_target_color(self) -> str:
        """Get target deployment color (opposite of current)"""
        current = self.get_current_active_color()
        return 'green' if current == 'blue' else 'blue'
    
    def deploy_to_target_environment(self, image_tag: str, target_color: str) -> bool:
        """Deploy new version to target environment"""
        print(f"🚀 Deploying {image_tag} to {target_color} environment...")
        
        deployment_name = f"{self.service_name}-{target_color}"
        
        try:
            # Update deployment image
            cmd = [
                'kubectl', 'set', 'image',
                f"deployment/{deployment_name}",
                f"{self.service_name}={image_tag}",
                '-n', self.namespace
            ]
            
            subprocess.run(cmd, check=True)
            
            # Wait for rollout to complete
            cmd = [
                'kubectl', 'rollout', 'status',
                f"deployment/{deployment_name}",
                '-n', self.namespace,
                '--timeout=600s'
            ]
            
            subprocess.run(cmd, check=True)
            
            print(f"✅ Successfully deployed to {target_color} environment")
            return True
            
        except subprocess.CalledProcessError as e:
            print(f"❌ Deployment to {target_color} failed: {e}")
            return False
    
    def perform_health_checks(self, target_color: str) -> bool:
        """Perform health checks on target environment"""
        print(f"🔍 Performing health checks on {target_color} environment...")
        
        target_service_name = f"{self.service_name}-{target_color}"
        endpoint = self._get_service_endpoint(target_service_name)
        
        if not endpoint:
            print(f"❌ Could not get endpoint for {target_service_name}")
            return False
        
        start_time = time.time()
        
        while time.time() - start_time < self.health_check_timeout:
            try:
                response = requests.get(f"http://{endpoint}/health", timeout=10)
                
                if response.status_code == 200:
                    health_data = response.json()
                    
                    if health_data.get('status') == 'healthy':
                        print(f"✅ Health check passed for {target_color}")
                        return True
                
            except requests.RequestException as e:
                print(f"⚠️  Health check failed: {e}")
            
            time.sleep(10)
        
        print(f"❌ Health checks failed for {target_color}")
        return False
    
    def switch_traffic(self, target_color: str) -> bool:
        """Switch traffic to target environment"""
        print(f"🔀 Switching traffic to {target_color} environment...")
        
        try:
            cmd = [
                'kubectl', 'patch', 'service', self.service_name,
                '-n', self.namespace,
                '-p', f'{{"spec":{{"selector":{{"version":"{target_color}"}}}}}}'
            ]
            
            subprocess.run(cmd, check=True)
            
            # Verify traffic switch
            time.sleep(5)
            current_color = self.get_current_active_color()
            
            if current_color == target_color:
                print(f"✅ Traffic successfully switched to {target_color}")
                return True
            else:
                print(f"❌ Traffic switch verification failed")
                return False
                
        except subprocess.CalledProcessError as e:
            print(f"❌ Traffic switch failed: {e}")
            return False
    
    def rollback_deployment(self, previous_color: str) -> bool:
        """Rollback to previous deployment"""
        print(f"🔄 Rolling back to {previous_color} environment...")
        return self.switch_traffic(previous_color)
    
    def _get_service_endpoint(self, service_name: str) -> str:
        """Get service endpoint for health checks"""
        try:
            cmd = [
                'kubectl', 'get', 'service', service_name,
                '-n', self.namespace,
                '-o', 'jsonpath={.spec.clusterIP}:{.spec.ports[0].port}'
            ]
            
            result = subprocess.run(cmd, capture_output=True, text=True, check=True)
            return result.stdout.strip()
            
        except subprocess.CalledProcessError:
            return None
    
    def execute_deployment(self, image_tag: str) -> bool:
        """Execute complete blue-green deployment"""
        print(f"\n🎯 Starting Blue-Green Deployment")
        print(f"   Service: {self.service_name}")
        print(f"   Image: {image_tag}")
        
        current_color = self.get_current_active_color()
        target_color = self.get_target_color()
        
        print(f"\n📊 Deployment Plan:")
        print(f"   Current Active: {current_color}")
        print(f"   Target: {target_color}")
        
        # Deploy to target environment
        if not self.deploy_to_target_environment(image_tag, target_color):
            return False
        
        # Health checks
        if not self.perform_health_checks(target_color):
            print("❌ Health checks failed, aborting deployment")
            return False
        
        # Switch traffic
        if not self.switch_traffic(target_color):
            print("❌ Traffic switch failed, attempting rollback...")
            self.rollback_deployment(current_color)
            return False
        
        print(f"\n🎉 Blue-Green deployment completed successfully!")
        return True
```

---

## 🐳 **Kubernetes Blue-Green Setup**

### **Blue-Green Deployment Manifests**
```yaml
# blue-green-setup.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-blue
  namespace: production
  labels:
    app: myapp
    version: blue
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
      version: blue
  template:
    metadata:
      labels:
        app: myapp
        version: blue
    spec:
      containers:
      - name: myapp
        image: myapp:v1.0.0
        ports:
        - containerPort: 8080
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 30
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 5

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-green
  namespace: production
  labels:
    app: myapp
    version: green
spec:
  replicas: 0  # Initially scaled to 0
  selector:
    matchLabels:
      app: myapp
      version: green
  template:
    metadata:
      labels:
        app: myapp
        version: green
    spec:
      containers:
      - name: myapp
        image: myapp:v1.0.0
        ports:
        - containerPort: 8080

---
# Main service - points to active color
apiVersion: v1
kind: Service
metadata:
  name: myapp
  namespace: production
spec:
  selector:
    app: myapp
    version: blue  # Initially points to blue
  ports:
  - port: 80
    targetPort: 8080
```

---

## 🔄 **Automated Rollback Strategy**

### **Health Monitoring and Rollback**
```python
# rollback_manager.py
import time
from typing import Dict

class RollbackManager:
    """Manage automated rollbacks based on metrics"""
    
    def __init__(self, deployer):
        self.deployer = deployer
        self.monitoring_duration = 300  # 5 minutes
        self.error_rate_threshold = 0.05  # 5%
    
    def monitor_deployment_health(self, target_color: str) -> bool:
        """Monitor deployment health and trigger rollback if needed"""
        print(f"📊 Monitoring {target_color} deployment health...")
        
        start_time = time.time()
        
        while time.time() - start_time < self.monitoring_duration:
            metrics = self._collect_metrics(target_color)
            
            if self._should_rollback(metrics):
                print("🚨 Health metrics indicate problems")
                return False
            
            time.sleep(30)
        
        print("✅ Deployment health monitoring completed")
        return True
    
    def _collect_metrics(self, color: str) -> Dict[str, float]:
        """Collect metrics for the specified environment"""
        # Simulated metrics collection
        return {
            'error_rate': 0.02,
            'avg_response_time': 250
        }
    
    def _should_rollback(self, metrics: Dict[str, float]) -> bool:
        """Determine if rollback should be triggered"""
        if metrics.get('error_rate', 0) > self.error_rate_threshold:
            return True
        return False
```

---

**💡 Key Takeaway**: Blue-green deployments provide zero-downtime updates with instant rollback capabilities!
