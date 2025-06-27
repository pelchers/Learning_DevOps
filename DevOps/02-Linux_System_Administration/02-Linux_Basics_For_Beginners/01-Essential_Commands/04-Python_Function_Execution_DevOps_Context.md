# 🔧 Python Function Execution in DevOps Context

## 📖 What This File Does
Comprehensive guide to executing Python functions for DevOps automation, infrastructure management, deployment orchestration, monitoring, and system administration tasks.

## 🎯 Learning Objectives
- Master Python function execution for infrastructure automation
- Learn deployment and orchestration function patterns
- Understand monitoring and alerting function design
- Implement error handling for production systems
- Master system administration automation

## 📋 Prerequisites
- Understanding of Python functions and DevOps concepts
- Familiarity with shell commands and system administration
- Basic knowledge of containers, CI/CD, and cloud platforms

---

## 🏗️ **Infrastructure Automation Functions**

### **Cloud Resource Management**
```python
import boto3
import subprocess
import logging
from typing import Dict, List, Optional
from datetime import datetime
import json

# Configure logging for infrastructure operations
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(levelname)s - %(message)s'
)
logger = logging.getLogger(__name__)

def create_aws_ec2_instance(instance_config: Dict) -> Dict:
    """Create AWS EC2 instance with specified configuration"""
    
    try:
        ec2 = boto3.client('ec2')
        
        # Validate required configuration
        required_fields = ['ImageId', 'InstanceType', 'KeyName', 'SecurityGroupIds']
        for field in required_fields:
            if field not in instance_config:
                raise ValueError(f"Missing required field: {field}")
        
        logger.info(f"Creating EC2 instance with config: {instance_config}")
        
        # Create instance
        response = ec2.run_instances(
            ImageId=instance_config['ImageId'],
            MinCount=1,
            MaxCount=1,
            InstanceType=instance_config['InstanceType'],
            KeyName=instance_config['KeyName'],
            SecurityGroupIds=instance_config['SecurityGroupIds'],
            SubnetId=instance_config.get('SubnetId'),
            UserData=instance_config.get('UserData', ''),
            TagSpecifications=[
                {
                    'ResourceType': 'instance',
                    'Tags': instance_config.get('Tags', [])
                }
            ]
        )
        
        instance_id = response['Instances'][0]['InstanceId']
        
        # Wait for instance to be running
        logger.info(f"Waiting for instance {instance_id} to be running...")
        waiter = ec2.get_waiter('instance_running')
        waiter.wait(InstanceIds=[instance_id])
        
        # Get instance details
        instance_details = get_instance_details(instance_id)
        
        logger.info(f"Instance {instance_id} created successfully")
        
        return {
            'instance_id': instance_id,
            'public_ip': instance_details.get('PublicIpAddress'),
            'private_ip': instance_details.get('PrivateIpAddress'),
            'state': instance_details.get('State', {}).get('Name'),
            'created_at': datetime.now().isoformat()
        }
        
    except Exception as e:
        logger.error(f"Failed to create EC2 instance: {str(e)}")
        raise

def get_instance_details(instance_id: str) -> Dict:
    """Get detailed information about an EC2 instance"""
    
    try:
        ec2 = boto3.client('ec2')
        
        response = ec2.describe_instances(InstanceIds=[instance_id])
        
        if not response['Reservations']:
            raise ValueError(f"Instance {instance_id} not found")
        
        instance = response['Reservations'][0]['Instances'][0]
        
        return {
            'InstanceId': instance['InstanceId'],
            'InstanceType': instance['InstanceType'],
            'State': instance['State'],
            'PublicIpAddress': instance.get('PublicIpAddress'),
            'PrivateIpAddress': instance.get('PrivateIpAddress'),
            'LaunchTime': instance['LaunchTime'].isoformat(),
            'Tags': instance.get('Tags', [])
        }
        
    except Exception as e:
        logger.error(f"Failed to get instance details for {instance_id}: {str(e)}")
        raise

def terminate_instance(instance_id: str) -> bool:
    """Terminate an EC2 instance"""
    
    try:
        ec2 = boto3.client('ec2')
        
        logger.info(f"Terminating instance {instance_id}")
        
        response = ec2.terminate_instances(InstanceIds=[instance_id])
        
        # Wait for termination
        waiter = ec2.get_waiter('instance_terminated')
        waiter.wait(InstanceIds=[instance_id])
        
        logger.info(f"Instance {instance_id} terminated successfully")
        return True
        
    except Exception as e:
        logger.error(f"Failed to terminate instance {instance_id}: {str(e)}")
        return False

def provision_infrastructure_stack(stack_config: Dict) -> Dict:
    """Provision complete infrastructure stack"""
    
    logger.info("Starting infrastructure stack provisioning")
    
    provisioned_resources = {
        'instances': [],
        'load_balancers': [],
        'databases': [],
        'errors': []
    }
    
    try:
        # Provision web servers
        for i, web_config in enumerate(stack_config.get('web_servers', [])):
            try:
                instance = create_aws_ec2_instance(web_config)
                provisioned_resources['instances'].append(instance)
                logger.info(f"Web server {i+1} provisioned: {instance['instance_id']}")
            except Exception as e:
                error_msg = f"Failed to provision web server {i+1}: {str(e)}"
                logger.error(error_msg)
                provisioned_resources['errors'].append(error_msg)
        
        # Provision database servers
        for i, db_config in enumerate(stack_config.get('database_servers', [])):
            try:
                instance = create_aws_ec2_instance(db_config)
                provisioned_resources['instances'].append(instance)
                logger.info(f"Database server {i+1} provisioned: {instance['instance_id']}")
            except Exception as e:
                error_msg = f"Failed to provision database server {i+1}: {str(e)}"
                logger.error(error_msg)
                provisioned_resources['errors'].append(error_msg)
        
        # Configure security groups
        if stack_config.get('security_groups'):
            configure_security_groups(stack_config['security_groups'])
        
        logger.info("Infrastructure stack provisioning completed")
        
        return provisioned_resources
        
    except Exception as e:
        logger.error(f"Infrastructure stack provisioning failed: {str(e)}")
        provisioned_resources['errors'].append(str(e))
        return provisioned_resources

def configure_security_groups(security_groups: List[Dict]):
    """Configure security groups for infrastructure"""
    
    ec2 = boto3.client('ec2')
    
    for sg_config in security_groups:
        try:
            # Create security group
            response = ec2.create_security_group(
                GroupName=sg_config['name'],
                Description=sg_config['description'],
                VpcId=sg_config.get('vpc_id')
            )
            
            sg_id = response['GroupId']
            
            # Add ingress rules
            if sg_config.get('ingress_rules'):
                ec2.authorize_security_group_ingress(
                    GroupId=sg_id,
                    IpPermissions=sg_config['ingress_rules']
                )
            
            logger.info(f"Security group {sg_config['name']} created: {sg_id}")
            
        except Exception as e:
            logger.error(f"Failed to create security group {sg_config['name']}: {str(e)}")
```

**📖 What This Does**  
Infrastructure automation functions manage cloud resources programmatically. They handle resource provisioning, configuration, and lifecycle management with proper error handling and logging.

**🔧 DevOps Applications**
- Automated infrastructure provisioning
- Dynamic scaling based on load
- Infrastructure-as-Code implementations
- Disaster recovery automation
- Cost optimization through resource management

**💡 Example Explanation**
```python
# Infrastructure automation flow:
# 1. validate_configuration() - Check all required parameters
# 2. create_aws_ec2_instance() - Provision compute resources
# 3. configure_security_groups() - Set up network security
# 4. wait_for_ready_state() - Ensure resources are operational
# 5. register_with_load_balancer() - Add to traffic distribution
# 6. update_dns_records() - Make resources discoverable
# 7. run_health_checks() - Verify everything works
```

### **Terraform Integration Functions**
```python
import subprocess
import json
from pathlib import Path
from typing import Dict, List, Optional

def execute_terraform_command(command: str, working_dir: str, 
                            env_vars: Optional[Dict] = None) -> Dict:
    """Execute Terraform command with proper error handling"""
    
    try:
        # Prepare environment
        import os
        env = os.environ.copy()
        if env_vars:
            env.update(env_vars)
        
        logger.info(f"Executing Terraform command: {command}")
        logger.info(f"Working directory: {working_dir}")
        
        # Execute command
        result = subprocess.run(
            command,
            shell=True,
            cwd=working_dir,
            capture_output=True,
            text=True,
            env=env,
            timeout=1800  # 30 minute timeout
        )
        
        return {
            'success': result.returncode == 0,
            'return_code': result.returncode,
            'stdout': result.stdout,
            'stderr': result.stderr,
            'command': command
        }
        
    except subprocess.TimeoutExpired:
        logger.error(f"Terraform command timed out: {command}")
        return {
            'success': False,
            'error': 'Command timed out',
            'command': command
        }
    except Exception as e:
        logger.error(f"Failed to execute Terraform command: {str(e)}")
        return {
            'success': False,
            'error': str(e),
            'command': command
        }

def terraform_init(project_dir: str) -> bool:
    """Initialize Terraform project"""
    
    result = execute_terraform_command(
        "terraform init",
        project_dir
    )
    
    if result['success']:
        logger.info("Terraform initialization successful")
        return True
    else:
        logger.error(f"Terraform init failed: {result.get('stderr', result.get('error'))}")
        return False

def terraform_plan(project_dir: str, var_file: Optional[str] = None) -> Dict:
    """Generate Terraform execution plan"""
    
    command = "terraform plan -out=tfplan"
    if var_file:
        command += f" -var-file={var_file}"
    
    result = execute_terraform_command(command, project_dir)
    
    if result['success']:
        logger.info("Terraform plan generated successfully")
        
        # Parse plan output for resource changes
        plan_summary = parse_terraform_plan_output(result['stdout'])
        
        return {
            'success': True,
            'plan_summary': plan_summary,
            'full_output': result['stdout']
        }
    else:
        logger.error(f"Terraform plan failed: {result.get('stderr', result.get('error'))}")
        return {
            'success': False,
            'error': result.get('stderr', result.get('error'))
        }

def terraform_apply(project_dir: str, auto_approve: bool = False) -> Dict:
    """Apply Terraform execution plan"""
    
    command = "terraform apply tfplan"
    if auto_approve:
        command = "terraform apply -auto-approve"
    
    result = execute_terraform_command(command, project_dir)
    
    if result['success']:
        logger.info("Terraform apply completed successfully")
        
        # Get state information
        state_info = get_terraform_state_info(project_dir)
        
        return {
            'success': True,
            'apply_output': result['stdout'],
            'resources': state_info.get('resources', [])
        }
    else:
        logger.error(f"Terraform apply failed: {result.get('stderr', result.get('error'))}")
        return {
            'success': False,
            'error': result.get('stderr', result.get('error'))
        }

def terraform_destroy(project_dir: str, auto_approve: bool = False) -> bool:
    """Destroy Terraform-managed infrastructure"""
    
    command = "terraform destroy"
    if auto_approve:
        command += " -auto-approve"
    
    result = execute_terraform_command(command, project_dir)
    
    if result['success']:
        logger.info("Terraform destroy completed successfully")
        return True
    else:
        logger.error(f"Terraform destroy failed: {result.get('stderr', result.get('error'))}")
        return False

def get_terraform_state_info(project_dir: str) -> Dict:
    """Get information from Terraform state"""
    
    result = execute_terraform_command("terraform show -json", project_dir)
    
    if result['success']:
        try:
            state_data = json.loads(result['stdout'])
            
            resources = []
            if 'values' in state_data and 'root_module' in state_data['values']:
                resources = state_data['values']['root_module'].get('resources', [])
            
            return {
                'success': True,
                'resources': resources,
                'resource_count': len(resources)
            }
        except json.JSONDecodeError:
            logger.error("Failed to parse Terraform state JSON")
            return {'success': False, 'error': 'Invalid JSON output'}
    else:
        return {'success': False, 'error': result.get('error')}

def parse_terraform_plan_output(output: str) -> Dict:
    """Parse Terraform plan output for resource changes"""
    
    changes = {
        'to_add': 0,
        'to_change': 0,
        'to_destroy': 0,
        'resources': []
    }
    
    lines = output.split('\n')
    for line in lines:
        if 'Plan:' in line:
            # Extract numbers from plan summary
            import re
            numbers = re.findall(r'\d+', line)
            if len(numbers) >= 3:
                changes['to_add'] = int(numbers[0])
                changes['to_change'] = int(numbers[1])
                changes['to_destroy'] = int(numbers[2])
            break
    
    return changes

def automated_infrastructure_deployment(config_file: str) -> Dict:
    """Complete automated infrastructure deployment"""
    
    logger.info("Starting automated infrastructure deployment")
    
    try:
        # Load configuration
        with open(config_file) as f:
            config = json.load(f)
        
        project_dir = config['terraform_project_dir']
        var_file = config.get('variables_file')
        auto_approve = config.get('auto_approve', False)
        
        deployment_result = {
            'steps_completed': [],
            'steps_failed': [],
            'resources_created': [],
            'deployment_time': datetime.now().isoformat()
        }
        
        # Step 1: Initialize Terraform
        if terraform_init(project_dir):
            deployment_result['steps_completed'].append('terraform_init')
        else:
            deployment_result['steps_failed'].append('terraform_init')
            return deployment_result
        
        # Step 2: Generate plan
        plan_result = terraform_plan(project_dir, var_file)
        if plan_result['success']:
            deployment_result['steps_completed'].append('terraform_plan')
            deployment_result['planned_changes'] = plan_result['plan_summary']
        else:
            deployment_result['steps_failed'].append('terraform_plan')
            return deployment_result
        
        # Step 3: Apply changes
        apply_result = terraform_apply(project_dir, auto_approve)
        if apply_result['success']:
            deployment_result['steps_completed'].append('terraform_apply')
            deployment_result['resources_created'] = apply_result['resources']
        else:
            deployment_result['steps_failed'].append('terraform_apply')
            return deployment_result
        
        # Step 4: Post-deployment validation
        if validate_deployed_infrastructure(config):
            deployment_result['steps_completed'].append('validation')
            deployment_result['validation_status'] = 'passed'
        else:
            deployment_result['steps_failed'].append('validation')
            deployment_result['validation_status'] = 'failed'
        
        logger.info("Automated infrastructure deployment completed")
        return deployment_result
        
    except Exception as e:
        logger.error(f"Automated deployment failed: {str(e)}")
        return {
            'error': str(e),
            'deployment_time': datetime.now().isoformat()
        }

def validate_deployed_infrastructure(config: Dict) -> bool:
    """Validate that deployed infrastructure is working correctly"""
    
    validation_checks = config.get('validation_checks', [])
    
    for check in validation_checks:
        check_type = check.get('type')
        
        if check_type == 'http_endpoint':
            if not validate_http_endpoint(check['url'], check.get('expected_status', 200)):
                return False
        elif check_type == 'ssh_connectivity':
            if not validate_ssh_connectivity(check['host'], check['username'], check['key_path']):
                return False
        elif check_type == 'database_connection':
            if not validate_database_connection(check['connection_string']):
                return False
    
    return True

def validate_http_endpoint(url: str, expected_status: int = 200) -> bool:
    """Validate HTTP endpoint is responding"""
    
    try:
        import requests
        response = requests.get(url, timeout=10)
        return response.status_code == expected_status
    except Exception as e:
        logger.error(f"HTTP endpoint validation failed for {url}: {str(e)}")
        return False

def validate_ssh_connectivity(host: str, username: str, key_path: str) -> bool:
    """Validate SSH connectivity to host"""
    
    try:
        command = f"ssh -i {key_path} -o ConnectTimeout=10 -o StrictHostKeyChecking=no {username}@{host} 'echo connection_test'"
        
        result = subprocess.run(
            command,
            shell=True,
            capture_output=True,
            text=True,
            timeout=30
        )
        
        return result.returncode == 0 and 'connection_test' in result.stdout
        
    except Exception as e:
        logger.error(f"SSH connectivity validation failed for {host}: {str(e)}")
        return False
```

**📖 What This Does**  
Terraform integration functions provide Infrastructure-as-Code automation with proper error handling, state management, and validation. They enable repeatable, version-controlled infrastructure deployments.

---

## 📦 **Container and Deployment Functions**

### **Docker Operations**
```python
import docker
import subprocess
from typing import Dict, List, Optional
import time

def build_docker_image(dockerfile_path: str, image_tag: str, 
                      build_args: Optional[Dict] = None) -> Dict:
    """Build Docker image with detailed logging"""
    
    try:
        client = docker.from_env()
        
        logger.info(f"Building Docker image: {image_tag}")
        logger.info(f"Dockerfile path: {dockerfile_path}")
        
        # Build image
        build_logs = []
        
        image, build_generator = client.images.build(
            path=dockerfile_path,
            tag=image_tag,
            buildargs=build_args or {},
            rm=True,  # Remove intermediate containers
            forcerm=True  # Always remove intermediate containers
        )
        
        # Collect build logs
        for log in build_generator:
            if 'stream' in log:
                log_line = log['stream'].strip()
                if log_line:
                    logger.info(f"Build: {log_line}")
                    build_logs.append(log_line)
        
        # Get image details
        image_info = {
            'id': image.id,
            'tags': image.tags,
            'size': image.attrs.get('Size', 0),
            'created': image.attrs.get('Created'),
            'build_logs': build_logs
        }
        
        logger.info(f"Image built successfully: {image_tag}")
        return {
            'success': True,
            'image': image_info
        }
        
    except docker.errors.BuildError as e:
        logger.error(f"Docker build failed: {str(e)}")
        return {
            'success': False,
            'error': 'Build failed',
            'build_logs': [str(log) for log in e.build_log]
        }
    except Exception as e:
        logger.error(f"Docker build error: {str(e)}")
        return {
            'success': False,
            'error': str(e)
        }

def push_docker_image(image_tag: str, registry_url: Optional[str] = None) -> bool:
    """Push Docker image to registry"""
    
    try:
        client = docker.from_env()
        
        # Tag for registry if specified
        if registry_url:
            full_tag = f"{registry_url}/{image_tag}"
            image = client.images.get(image_tag)
            image.tag(full_tag)
            push_tag = full_tag
        else:
            push_tag = image_tag
        
        logger.info(f"Pushing image: {push_tag}")
        
        # Push image
        push_logs = []
        for log in client.images.push(push_tag, stream=True, decode=True):
            if 'status' in log:
                status_line = f"{log.get('id', '')}: {log['status']}"
                if 'progress' in log:
                    status_line += f" {log['progress']}"
                
                logger.info(f"Push: {status_line}")
                push_logs.append(status_line)
        
        logger.info(f"Image pushed successfully: {push_tag}")
        return True
        
    except Exception as e:
        logger.error(f"Failed to push image {image_tag}: {str(e)}")
        return False

def run_docker_container(image_tag: str, container_config: Dict) -> Dict:
    """Run Docker container with specified configuration"""
    
    try:
        client = docker.from_env()
        
        logger.info(f"Starting container from image: {image_tag}")
        
        # Run container
        container = client.containers.run(
            image_tag,
            name=container_config.get('name'),
            ports=container_config.get('ports', {}),
            environment=container_config.get('environment', {}),
            volumes=container_config.get('volumes', {}),
            command=container_config.get('command'),
            detach=True,
            restart_policy=container_config.get('restart_policy', {'Name': 'unless-stopped'})
        )
        
        # Wait for container to be running
        time.sleep(2)
        container.reload()
        
        container_info = {
            'id': container.id,
            'name': container.name,
            'status': container.status,
            'ports': container.ports,
            'created': container.attrs.get('Created')
        }
        
        logger.info(f"Container started successfully: {container.name}")
        return {
            'success': True,
            'container': container_info
        }
        
    except Exception as e:
        logger.error(f"Failed to run container: {str(e)}")
        return {
            'success': False,
            'error': str(e)
        }

def get_container_logs(container_name: str, tail_lines: int = 100) -> str:
    """Get logs from running container"""
    
    try:
        client = docker.from_env()
        container = client.containers.get(container_name)
        
        logs = container.logs(tail=tail_lines, timestamps=True).decode('utf-8')
        return logs
        
    except Exception as e:
        logger.error(f"Failed to get logs for container {container_name}: {str(e)}")
        return ""

def stop_and_remove_container(container_name: str) -> bool:
    """Stop and remove container"""
    
    try:
        client = docker.from_env()
        container = client.containers.get(container_name)
        
        logger.info(f"Stopping container: {container_name}")
        container.stop(timeout=30)
        
        logger.info(f"Removing container: {container_name}")
        container.remove()
        
        logger.info(f"Container {container_name} stopped and removed")
        return True
        
    except Exception as e:
        logger.error(f"Failed to stop/remove container {container_name}: {str(e)}")
        return False

def automated_docker_deployment(deployment_config: Dict) -> Dict:
    """Complete automated Docker deployment"""
    
    logger.info("Starting automated Docker deployment")
    
    deployment_result = {
        'steps_completed': [],
        'steps_failed': [],
        'containers_deployed': [],
        'deployment_time': datetime.now().isoformat()
    }
    
    try:
        # Step 1: Build images
        for build_config in deployment_config.get('builds', []):
            build_result = build_docker_image(
                build_config['dockerfile_path'],
                build_config['image_tag'],
                build_config.get('build_args')
            )
            
            if build_result['success']:
                deployment_result['steps_completed'].append(f"build_{build_config['image_tag']}")
            else:
                deployment_result['steps_failed'].append(f"build_{build_config['image_tag']}")
                return deployment_result
        
        # Step 2: Push images to registry
        if deployment_config.get('push_to_registry'):
            registry_url = deployment_config.get('registry_url')
            
            for build_config in deployment_config.get('builds', []):
                if push_docker_image(build_config['image_tag'], registry_url):
                    deployment_result['steps_completed'].append(f"push_{build_config['image_tag']}")
                else:
                    deployment_result['steps_failed'].append(f"push_{build_config['image_tag']}")
        
        # Step 3: Deploy containers
        for container_config in deployment_config.get('containers', []):
            # Stop existing container if it exists
            existing_container = container_config.get('name')
            if existing_container:
                stop_and_remove_container(existing_container)
            
            # Start new container
            run_result = run_docker_container(
                container_config['image_tag'],
                container_config
            )
            
            if run_result['success']:
                deployment_result['steps_completed'].append(f"deploy_{container_config['name']}")
                deployment_result['containers_deployed'].append(run_result['container'])
            else:
                deployment_result['steps_failed'].append(f"deploy_{container_config['name']}")
        
        # Step 4: Health checks
        if deployment_config.get('health_checks'):
            if perform_container_health_checks(deployment_config['health_checks']):
                deployment_result['steps_completed'].append('health_checks')
            else:
                deployment_result['steps_failed'].append('health_checks')
        
        logger.info("Automated Docker deployment completed")
        return deployment_result
        
    except Exception as e:
        logger.error(f"Automated Docker deployment failed: {str(e)}")
        deployment_result['error'] = str(e)
        return deployment_result

def perform_container_health_checks(health_checks: List[Dict]) -> bool:
    """Perform health checks on deployed containers"""
    
    for check in health_checks:
        check_type = check.get('type')
        
        if check_type == 'http':
            url = check['url']
            expected_status = check.get('expected_status', 200)
            
            try:
                import requests
                response = requests.get(url, timeout=10)
                if response.status_code != expected_status:
                    logger.error(f"Health check failed for {url}: status {response.status_code}")
                    return False
            except Exception as e:
                logger.error(f"Health check failed for {url}: {str(e)}")
                return False
        
        elif check_type == 'container_running':
            container_name = check['container_name']
            
            try:
                client = docker.from_env()
                container = client.containers.get(container_name)
                if container.status != 'running':
                    logger.error(f"Container {container_name} is not running: {container.status}")
                    return False
            except Exception as e:
                logger.error(f"Failed to check container {container_name}: {str(e)}")
                return False
    
    logger.info("All health checks passed")
    return True
```

**📖 What This Does**  
Docker operations functions automate container lifecycle management including building, pushing, deploying, and monitoring containers. They provide comprehensive error handling and logging for production deployments.

---

## 📊 **Monitoring and Alerting Functions**

### **System Monitoring**
```python
import psutil
import requests
import smtplib
from email.mime.text import MIMEText
from email.mime.multipart import MIMEMultipart
from typing import Dict, List, Optional
import time

def collect_system_metrics() -> Dict:
    """Collect comprehensive system metrics"""
    
    try:
        # CPU metrics
        cpu_percent = psutil.cpu_percent(interval=1)
        cpu_count = psutil.cpu_count()
        cpu_freq = psutil.cpu_freq()
        
        # Memory metrics
        memory = psutil.virtual_memory()
        swap = psutil.swap_memory()
        
        # Disk metrics
        disk_usage = psutil.disk_usage('/')
        disk_io = psutil.disk_io_counters()
        
        # Network metrics
        network_io = psutil.net_io_counters()
        
        # Process metrics
        process_count = len(psutil.pids())
        
        # Load average (Unix-like systems)
        try:
            load_avg = psutil.getloadavg()
        except AttributeError:
            load_avg = None
        
        metrics = {
            'timestamp': datetime.now().isoformat(),
            'cpu': {
                'percent': cpu_percent,
                'count': cpu_count,
                'frequency_mhz': cpu_freq.current if cpu_freq else None
            },
            'memory': {
                'total_gb': memory.total / (1024**3),
                'available_gb': memory.available / (1024**3),
                'used_gb': memory.used / (1024**3),
                'percent': memory.percent
            },
            'swap': {
                'total_gb': swap.total / (1024**3),
                'used_gb': swap.used / (1024**3),
                'percent': swap.percent
            },
            'disk': {
                'total_gb': disk_usage.total / (1024**3),
                'used_gb': disk_usage.used / (1024**3),
                'free_gb': disk_usage.free / (1024**3),
                'percent': (disk_usage.used / disk_usage.total) * 100,
                'read_mb': disk_io.read_bytes / (1024**2) if disk_io else None,
                'write_mb': disk_io.write_bytes / (1024**2) if disk_io else None
            },
            'network': {
                'bytes_sent_mb': network_io.bytes_sent / (1024**2),
                'bytes_recv_mb': network_io.bytes_recv / (1024**2),
                'packets_sent': network_io.packets_sent,
                'packets_recv': network_io.packets_recv
            },
            'system': {
                'process_count': process_count,
                'load_average': load_avg,
                'boot_time': psutil.boot_time()
            }
        }
        
        logger.info("System metrics collected successfully")
        return metrics
        
    except Exception as e:
        logger.error(f"Failed to collect system metrics: {str(e)}")
        return {}

def check_system_health(metrics: Dict, thresholds: Dict) -> Dict:
    """Check system health against defined thresholds"""
    
    health_status = {
        'overall_status': 'healthy',
        'alerts': [],
        'warnings': [],
        'checks_performed': []
    }
    
    # CPU check
    cpu_threshold = thresholds.get('cpu_percent', 80)
    if metrics.get('cpu', {}).get('percent', 0) > cpu_threshold:
        alert = f"High CPU usage: {metrics['cpu']['percent']:.1f}% (threshold: {cpu_threshold}%)"
        health_status['alerts'].append(alert)
        health_status['overall_status'] = 'critical'
    
    # Memory check
    memory_threshold = thresholds.get('memory_percent', 85)
    if metrics.get('memory', {}).get('percent', 0) > memory_threshold:
        alert = f"High memory usage: {metrics['memory']['percent']:.1f}% (threshold: {memory_threshold}%)"
        health_status['alerts'].append(alert)
        health_status['overall_status'] = 'critical'
    
    # Disk check
    disk_threshold = thresholds.get('disk_percent', 90)
    if metrics.get('disk', {}).get('percent', 0) > disk_threshold:
        alert = f"High disk usage: {metrics['disk']['percent']:.1f}% (threshold: {disk_threshold}%)"
        health_status['alerts'].append(alert)
        health_status['overall_status'] = 'critical'
    
    # Load average check (if available)
    load_threshold = thresholds.get('load_average', None)
    if load_threshold and metrics.get('system', {}).get('load_average'):
        load_1min = metrics['system']['load_average'][0]
        if load_1min > load_threshold:
            alert = f"High load average: {load_1min:.2f} (threshold: {load_threshold})"
            health_status['alerts'].append(alert)
            if health_status['overall_status'] == 'healthy':
                health_status['overall_status'] = 'warning'
    
    # Available memory warning
    available_memory_gb = metrics.get('memory', {}).get('available_gb', 0)
    if available_memory_gb < 1.0:  # Less than 1GB available
        warning = f"Low available memory: {available_memory_gb:.2f}GB"
        health_status['warnings'].append(warning)
        if health_status['overall_status'] == 'healthy':
            health_status['overall_status'] = 'warning'
    
    health_status['checks_performed'] = ['cpu', 'memory', 'disk', 'load_average']
    
    return health_status

def send_alert_notification(alert_message: str, notification_config: Dict) -> bool:
    """Send alert notification via email or webhook"""
    
    try:
        notification_type = notification_config.get('type', 'email')
        
        if notification_type == 'email':
            return send_email_alert(alert_message, notification_config)
        elif notification_type == 'webhook':
            return send_webhook_alert(alert_message, notification_config)
        elif notification_type == 'slack':
            return send_slack_alert(alert_message, notification_config)
        else:
            logger.error(f"Unknown notification type: {notification_type}")
            return False
            
    except Exception as e:
        logger.error(f"Failed to send alert notification: {str(e)}")
        return False

def send_email_alert(alert_message: str, email_config: Dict) -> bool:
    """Send alert via email"""
    
    try:
        smtp_server = email_config['smtp_server']
        smtp_port = email_config.get('smtp_port', 587)
        username = email_config['username']
        password = email_config['password']
        from_email = email_config.get('from_email', username)
        to_emails = email_config['to_emails']
        
        # Create message
        msg = MIMEMultipart()
        msg['From'] = from_email
        msg['To'] = ', '.join(to_emails)
        msg['Subject'] = f"System Alert - {datetime.now().strftime('%Y-%m-%d %H:%M:%S')}"
        
        body = f"""
System Alert Generated at {datetime.now().isoformat()}

Alert Details:
{alert_message}

This is an automated alert from your DevOps monitoring system.
"""
        
        msg.attach(MIMEText(body, 'plain'))
        
        # Send email
        server = smtplib.SMTP(smtp_server, smtp_port)
        server.starttls()
        server.login(username, password)
        server.send_message(msg)
        server.quit()
        
        logger.info(f"Email alert sent to {to_emails}")
        return True
        
    except Exception as e:
        logger.error(f"Failed to send email alert: {str(e)}")
        return False

def send_webhook_alert(alert_message: str, webhook_config: Dict) -> bool:
    """Send alert via webhook"""
    
    try:
        webhook_url = webhook_config['url']
        headers = webhook_config.get('headers', {'Content-Type': 'application/json'})
        
        payload = {
            'alert_type': 'system_monitoring',
            'message': alert_message,
            'timestamp': datetime.now().isoformat(),
            'severity': 'critical'
        }
        
        response = requests.post(
            webhook_url,
            json=payload,
            headers=headers,
            timeout=10
        )
        
        if response.status_code == 200:
            logger.info("Webhook alert sent successfully")
            return True
        else:
            logger.error(f"Webhook alert failed: {response.status_code}")
            return False
            
    except Exception as e:
        logger.error(f"Failed to send webhook alert: {str(e)}")
        return False

def send_slack_alert(alert_message: str, slack_config: Dict) -> bool:
    """Send alert to Slack channel"""
    
    try:
        webhook_url = slack_config['webhook_url']
        channel = slack_config.get('channel', '#alerts')
        username = slack_config.get('username', 'DevOps Monitor')
        
        payload = {
            'channel': channel,
            'username': username,
            'text': f"🚨 *System Alert*\n```{alert_message}```",
            'icon_emoji': ':warning:'
        }
        
        response = requests.post(webhook_url, json=payload, timeout=10)
        
        if response.status_code == 200:
            logger.info("Slack alert sent successfully")
            return True
        else:
            logger.error(f"Slack alert failed: {response.status_code}")
            return False
            
    except Exception as e:
        logger.error(f"Failed to send Slack alert: {str(e)}")
        return False

def continuous_monitoring(config: Dict):
    """Run continuous system monitoring with alerting"""
    
    monitoring_interval = config.get('interval_seconds', 60)
    thresholds = config.get('thresholds', {})
    notification_config = config.get('notifications', {})
    
    logger.info(f"Starting continuous monitoring (interval: {monitoring_interval}s)")
    
    consecutive_alerts = 0
    max_consecutive_alerts = config.get('max_consecutive_alerts', 3)
    
    while True:
        try:
            # Collect metrics
            metrics = collect_system_metrics()
            
            if not metrics:
                logger.error("Failed to collect metrics, skipping this cycle")
                time.sleep(monitoring_interval)
                continue
            
            # Check health
            health_status = check_system_health(metrics, thresholds)
            
            # Log current status
            logger.info(f"System status: {health_status['overall_status']}")
            
            # Handle alerts
            if health_status['alerts']:
                consecutive_alerts += 1
                
                # Send alert if configured and not too many consecutive alerts
                if notification_config and consecutive_alerts <= max_consecutive_alerts:
                    alert_message = '\n'.join(health_status['alerts'])
                    send_alert_notification(alert_message, notification_config)
                
                # Log all alerts
                for alert in health_status['alerts']:
                    logger.warning(f"ALERT: {alert}")
            else:
                consecutive_alerts = 0
            
            # Log warnings
            for warning in health_status['warnings']:
                logger.warning(f"WARNING: {warning}")
            
            # Store metrics (could be to database, file, etc.)
            store_metrics(metrics, health_status)
            
            # Wait for next cycle
            time.sleep(monitoring_interval)
            
        except KeyboardInterrupt:
            logger.info("Monitoring stopped by user")
            break
        except Exception as e:
            logger.error(f"Monitoring cycle failed: {str(e)}")
            time.sleep(monitoring_interval)

def store_metrics(metrics: Dict, health_status: Dict):
    """Store metrics for historical analysis"""
    
    try:
        # Store to JSON file (could be database in production)
        metrics_file = f"metrics_{datetime.now().strftime('%Y%m%d')}.json"
        
        import json
        from pathlib import Path
        
        # Create metrics entry
        metrics_entry = {
            'timestamp': metrics['timestamp'],
            'metrics': metrics,
            'health_status': health_status
        }
        
        # Append to daily file
        metrics_path = Path(metrics_file)
        
        if metrics_path.exists():
            with open(metrics_path, 'r') as f:
                existing_data = json.load(f)
        else:
            existing_data = []
        
        existing_data.append(metrics_entry)
        
        with open(metrics_path, 'w') as f:
            json.dump(existing_data, f, indent=2)
        
    except Exception as e:
        logger.error(f"Failed to store metrics: {str(e)}")
```

**📖 What This Does**  
Monitoring functions collect system metrics, analyze health status, and send alerts through multiple channels. They provide continuous monitoring with configurable thresholds and notification methods.

---

## 🎯 **Best Practices for DevOps Function Execution**

### **Function Design Principles**
1. **Idempotent Operations**: Functions should produce the same result when run multiple times
2. **Comprehensive Logging**: Log all operations for debugging and auditing
3. **Error Handling**: Handle expected failures gracefully
4. **Configuration-Driven**: Use external configuration for flexibility
5. **Modular Design**: Break complex operations into smaller, testable functions

### **Production Considerations**
- Implement retry logic for network operations
- Use timeouts for all external calls
- Monitor function execution times
- Implement circuit breakers for external services
- Use secrets management for sensitive data

### **Automation Best Practices**
- Validate inputs before processing
- Provide detailed execution logs
- Implement rollback mechanisms
- Test in non-production environments
- Use version control for all scripts

---

📄 **File Path:** `/DevOps/02-Linux_System_Administration/02-Linux_Basics_For_Beginners/01-Essential_Commands/04-Python_Function_Execution_DevOps_Context.md` 