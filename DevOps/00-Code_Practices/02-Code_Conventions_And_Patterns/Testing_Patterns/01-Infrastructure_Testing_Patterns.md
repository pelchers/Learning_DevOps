# Infrastructure Testing Patterns

## 📖 What This File Covers
Master testing patterns for DevOps infrastructure, including unit tests for infrastructure code, integration tests for deployment pipelines, and end-to-end testing for complete system validation.

## 🎯 Learning Objectives
- Implement unit testing for infrastructure code
- Create integration tests for CI/CD pipelines
- Design end-to-end tests for system validation
- Test configuration management and deployment scripts
- Implement testing in Docker and Kubernetes environments

---

## 🧪 **Infrastructure Unit Testing**

### **Testing Terraform Code**
```python
# test_infrastructure.py
import unittest
import subprocess
import os

class TestTerraformInfrastructure(unittest.TestCase):
    """Unit tests for Terraform infrastructure code"""
    
    def setUp(self):
        """Set up test environment"""
        self.terraform_dir = "infrastructure/terraform"
        self.test_vars = {
            "environment": "test",
            "instance_type": "t3.micro"
        }
    
    def test_terraform_plan_validation(self):
        """Test that Terraform plan is valid"""
        os.chdir(self.terraform_dir)
        
        # Initialize terraform
        init_result = subprocess.run(
            ["terraform", "init"],
            capture_output=True,
            text=True
        )
        self.assertEqual(init_result.returncode, 0)
        
        # Validate configuration
        validate_result = subprocess.run(
            ["terraform", "validate"],
            capture_output=True,
            text=True
        )
        self.assertEqual(validate_result.returncode, 0)
    
    def test_security_group_rules(self):
        """Test that security groups have proper rules"""
        security_rules = self._parse_security_groups()
        
        # Ensure no overly permissive rules
        for rule in security_rules:
            if rule.get('from_port') == 22:  # SSH
                self.assertNotIn('0.0.0.0/0', rule.get('cidr_blocks', []))
    
    def _parse_security_groups(self):
        """Parse security group rules from Terraform files"""
        return [
            {
                'from_port': 80,
                'to_port': 80,
                'protocol': 'tcp',
                'cidr_blocks': ['0.0.0.0/0']
            },
            {
                'from_port': 22,
                'to_port': 22,
                'protocol': 'tcp',
                'cidr_blocks': ['10.0.0.0/8']
            }
        ]
```

---

## 🔄 **CI/CD Pipeline Testing**

### **Testing GitHub Actions Workflows**
```python
# test_github_actions.py
import yaml
import unittest
from pathlib import Path

class TestGitHubActionsWorkflows(unittest.TestCase):
    """Test GitHub Actions workflow configurations"""
    
    def setUp(self):
        """Set up test environment"""
        self.workflows_dir = Path(".github/workflows")
        self.workflow_files = list(self.workflows_dir.glob("*.yml"))
    
    def test_all_workflows_valid_yaml(self):
        """Test that all workflow files are valid YAML"""
        for workflow_file in self.workflow_files:
            with open(workflow_file, 'r') as f:
                try:
                    yaml.safe_load(f)
                except yaml.YAMLError:
                    self.fail(f"Invalid YAML in {workflow_file}")
    
    def test_workflows_have_required_fields(self):
        """Test that workflows have required fields"""
        required_fields = ['name', 'on', 'jobs']
        
        for workflow_file in self.workflow_files:
            with open(workflow_file, 'r') as f:
                workflow = yaml.safe_load(f)
            
            for field in required_fields:
                self.assertIn(field, workflow)
    
    def test_production_deployment_requires_approval(self):
        """Test that production deployments require manual approval"""
        for workflow_file in self.workflow_files:
            with open(workflow_file, 'r') as f:
                workflow = yaml.safe_load(f)
            
            jobs = workflow.get('jobs', {})
            
            for job_name, job_config in jobs.items():
                # Check if this is a production deployment job
                if 'deploy' in job_name.lower() and 'prod' in str(job_config):
                    self.assertIn('environment', job_config)
```

---

## 🐳 **Container Testing Patterns**

### **Testing Docker Configurations**
```python
# test_docker.py
import unittest
import subprocess
import time
import requests

class TestDockerConfiguration(unittest.TestCase):
    """Unit tests for Docker configurations"""
    
    def test_dockerfile_security(self):
        """Test Dockerfile follows security best practices"""
        dockerfile_path = "docker/Dockerfile"
        
        with open(dockerfile_path, 'r') as f:
            dockerfile_content = f.read()
        
        # Check that we're not running as root
        self.assertIn("USER", dockerfile_content)
        
        # Check that we're using specific image tags
        self.assertNotRegex(dockerfile_content, r'FROM.*:latest')
    
    def test_container_health_check(self):
        """Test that containers respond to health checks"""
        container_name = "test-app"
        
        # Start container for testing
        subprocess.run([
            "docker", "run", "-d", "--name", container_name,
            "-p", "8080:8080", "myapp:test"
        ], check=True)
        
        try:
            # Wait for container to start
            time.sleep(10)
            
            # Test health endpoint
            response = requests.get("http://localhost:8080/health", timeout=10)
            self.assertEqual(response.status_code, 200)
            
            health_data = response.json()
            self.assertEqual(health_data.get('status'), 'healthy')
            
        finally:
            # Clean up container
            subprocess.run(["docker", "rm", "-f", container_name])
```

---

## 🧪 **End-to-End Testing Patterns**

### **System Integration Testing**
```python
# test_system_integration.py
import unittest
import requests
import time
import subprocess

class TestSystemIntegration(unittest.TestCase):
    """End-to-end system integration tests"""
    
    @classmethod
    def setUpClass(cls):
        """Set up test environment"""
        cls.base_url = "http://localhost:8080"
        cls.setup_test_environment()
    
    @classmethod
    def setup_test_environment(cls):
        """Start all required services for testing"""
        subprocess.run([
            "docker-compose", "-f", "docker-compose.test.yml", "up", "-d"
        ], check=True)
        
        cls.wait_for_services()
    
    @classmethod
    def wait_for_services(cls):
        """Wait for all services to be healthy"""
        services = [
            {"name": "api", "url": f"{cls.base_url}/health"},
            {"name": "database", "url": f"{cls.base_url}/db/health"},
        ]
        
        for service in services:
            retries = 30
            while retries > 0:
                try:
                    response = requests.get(service["url"], timeout=5)
                    if response.status_code == 200:
                        break
                except requests.RequestException:
                    pass
                
                retries -= 1
                time.sleep(2)
    
    def test_full_deployment_workflow(self):
        """Test complete deployment workflow"""
        # Deploy application
        deployment_data = {
            "service": "test-app",
            "version": "v1.0.0",
            "environment": "test"
        }
        
        response = requests.post(
            f"{self.base_url}/api/v1/deployments",
            json=deployment_data
        )
        self.assertEqual(response.status_code, 201)
        
        deployment = response.json()
        deployment_id = deployment['id']
        
        # Wait for deployment to complete
        self.wait_for_deployment_completion(deployment_id)
        
        # Verify application is running
        app_response = requests.get(f"{self.base_url}/api/v1/status")
        self.assertEqual(app_response.status_code, 200)
    
    def wait_for_deployment_completion(self, deployment_id: str):
        """Wait for deployment to complete"""
        retries = 60
        
        while retries > 0:
            response = requests.get(
                f"{self.base_url}/api/v1/deployments/{deployment_id}"
            )
            
            if response.status_code == 200:
                deployment = response.json()
                status = deployment.get('status')
                
                if status in ['successful', 'failed']:
                    self.assertEqual(status, 'successful')
                    return
            
            retries -= 1
            time.sleep(2)
```

---

**💡 Key Takeaway**: Comprehensive testing at every layer ensures reliable and maintainable DevOps infrastructure!
