# Python AWS Automation for DevOps

## 📖 What This File Covers
Master Python automation for AWS cloud services in DevOps workflows. Learn how to use boto3 and other AWS tools to automate infrastructure management, deployments, and monitoring.

## 🎯 Learning Objectives
- Use boto3 for AWS service automation
- Implement infrastructure provisioning with Python
- Automate deployment processes using AWS services
- Monitor and manage AWS resources programmatically
- Handle AWS credentials and security best practices

---

## 🐍 **AWS SDK Setup and Configuration**

### **Installing and Configuring Boto3**
```python
# requirements.txt
boto3==1.34.0
botocore==1.34.0
python-dotenv==1.0.0
pyyaml==6.0

# Install dependencies
# pip install -r requirements.txt
```

### **AWS Credentials Configuration**
```python
import boto3
import os
from dotenv import load_dotenv

# Load environment variables
load_dotenv()

def get_aws_session(profile_name=None, region_name='us-east-1'):
    """
    Create AWS session with proper credential handling
    
    Args:
        profile_name: AWS profile to use (optional)
        region_name: AWS region to use
    
    Returns:
        boto3.Session: Configured AWS session
    """
    
    # Method 1: Using environment variables
    if os.getenv('AWS_ACCESS_KEY_ID') and os.getenv('AWS_SECRET_ACCESS_KEY'):
        session = boto3.Session(
            aws_access_key_id=os.getenv('AWS_ACCESS_KEY_ID'),
            aws_secret_access_key=os.getenv('AWS_SECRET_ACCESS_KEY'),
            region_name=region_name
        )
        print("✅ Using environment variables for AWS credentials")
        return session
    
    # Method 2: Using AWS profile
    if profile_name:
        session = boto3.Session(profile_name=profile_name, region_name=region_name)
        print(f"✅ Using AWS profile: {profile_name}")
        return session
    
    # Method 3: Default credentials (IAM role, default profile, etc.)
    session = boto3.Session(region_name=region_name)
    print("✅ Using default AWS credentials")
    return session

def validate_aws_credentials(session):
    """Validate AWS credentials are working"""
    try:
        sts = session.client('sts')
        response = sts.get_caller_identity()
        
        print(f"✅ AWS credentials valid")
        print(f"   Account: {response.get('Account')}")
        print(f"   User: {response.get('Arn')}")
        return True
        
    except Exception as e:
        print(f"❌ AWS credentials invalid: {e}")
        return False

# Usage example
session = get_aws_session()
if validate_aws_credentials(session):
    print("Ready to use AWS services!")
```

---

## 🖥️ **EC2 Instance Management**

### **EC2 Instance Automation**
```python
import boto3
import time
from typing import List, Dict, Any

class EC2Manager:
    """Manage EC2 instances for DevOps automation"""
    
    def __init__(self, session):
        self.ec2_client = session.client('ec2')
        self.ec2_resource = session.resource('ec2')
    
    def create_instance(self, config: Dict[str, Any]) -> Dict[str, Any]:
        """Create EC2 instance with specified configuration"""
        try:
            response = self.ec2_client.run_instances(
                ImageId=config['ami_id'],
                MinCount=config.get('min_count', 1),
                MaxCount=config.get('max_count', 1),
                InstanceType=config['instance_type'],
                KeyName=config.get('key_name'),
                SecurityGroupIds=config.get('security_groups', []),
                SubnetId=config.get('subnet_id'),
                UserData=config.get('user_data', ''),
                TagSpecifications=[
                    {
                        'ResourceType': 'instance',
                        'Tags': config.get('tags', [])
                    }
                ]
            )
            
            instance_id = response['Instances'][0]['InstanceId']
            print(f"✅ Created EC2 instance: {instance_id}")
            
            return {
                'success': True,
                'instance_id': instance_id,
                'instance_info': response['Instances'][0]
            }
            
        except Exception as e:
            print(f"❌ Failed to create EC2 instance: {e}")
            return {'success': False, 'error': str(e)}
    
    def get_instance_info(self, instance_id: str) -> Dict[str, Any]:
        """Get detailed information about an instance"""
        try:
            response = self.ec2_client.describe_instances(InstanceIds=[instance_id])
            
            if not response['Reservations']:
                return {'success': False, 'error': 'Instance not found'}
            
            instance = response['Reservations'][0]['Instances'][0]
            
            return {
                'success': True,
                'instance_id': instance['InstanceId'],
                'state': instance['State']['Name'],
                'public_ip': instance.get('PublicIpAddress'),
                'private_ip': instance.get('PrivateIpAddress'),
                'instance_type': instance['InstanceType'],
                'tags': {tag['Key']: tag['Value'] for tag in instance.get('Tags', [])}
            }
            
        except Exception as e:
            print(f"❌ Failed to get instance info: {e}")
            return {'success': False, 'error': str(e)}

# Usage example
def deploy_web_server():
    """Deploy a web server instance"""
    session = get_aws_session()
    ec2_manager = EC2Manager(session)
    
    server_config = {
        'ami_id': 'ami-0c02fb55956c7d316',  # Amazon Linux 2
        'instance_type': 't3.micro',
        'tags': [
            {'Key': 'Name', 'Value': 'WebServer'},
            {'Key': 'Environment', 'Value': 'Production'}
        ]
    }
    
    result = ec2_manager.create_instance(server_config)
    return result['instance_id'] if result['success'] else None
```

---

## 🗄️ **S3 Storage Management**

### **S3 Operations for DevOps**
```python
class S3Manager:
    """Manage S3 operations for DevOps workflows"""
    
    def __init__(self, session):
        self.s3_client = session.client('s3')
    
    def create_bucket(self, bucket_name: str, region: str = 'us-east-1') -> bool:
        """Create S3 bucket with proper configuration"""
        try:
            if region == 'us-east-1':
                self.s3_client.create_bucket(Bucket=bucket_name)
            else:
                self.s3_client.create_bucket(
                    Bucket=bucket_name,
                    CreateBucketConfiguration={'LocationConstraint': region}
                )
            
            print(f"✅ Created S3 bucket: {bucket_name}")
            return True
            
        except Exception as e:
            print(f"❌ Failed to create bucket: {e}")
            return False
    
    def upload_file(self, file_path: str, bucket_name: str, object_key: str = None) -> bool:
        """Upload file to S3"""
        try:
            if not object_key:
                object_key = os.path.basename(file_path)
            
            self.s3_client.upload_file(file_path, bucket_name, object_key)
            print(f"✅ Uploaded {file_path} to s3://{bucket_name}/{object_key}")
            return True
            
        except Exception as e:
            print(f"❌ Failed to upload file: {e}")
            return False
```

---

## 🎯 **Complete Deployment Example**

### **End-to-End Application Deployment**
```python
def deploy_application_stack(app_name: str, environment: str):
    """Complete application deployment with AWS automation"""
    
    print(f"🚀 Deploying {app_name} to {environment} environment")
    
    session = get_aws_session()
    ec2_manager = EC2Manager(session)
    s3_manager = S3Manager(session)
    
    try:
        # 1. Create S3 bucket for artifacts
        bucket_name = f"{app_name}-{environment}-artifacts"
        s3_manager.create_bucket(bucket_name)
        
        # 2. Deploy EC2 instance
        instance_config = {
            'ami_id': 'ami-0c02fb55956c7d316',
            'instance_type': 't3.micro',
            'tags': [
                {'Key': 'Name', 'Value': f"{app_name}-{environment}"},
                {'Key': 'Environment', 'Value': environment}
            ]
        }
        
        result = ec2_manager.create_instance(instance_config)
        if not result['success']:
            raise Exception(f"Failed to create instance: {result['error']}")
        
        instance_id = result['instance_id']
        instance_info = ec2_manager.get_instance_info(instance_id)
        
        print(f"✅ Deployment completed successfully!")
        print(f"   Instance ID: {instance_id}")
        print(f"   Public IP: {instance_info.get('public_ip', 'N/A')}")
        
        return {'success': True, 'instance_id': instance_id}
        
    except Exception as e:
        print(f"❌ Deployment failed: {e}")
        return {'success': False, 'error': str(e)}

# Run deployment
if __name__ == "__main__":
    result = deploy_application_stack("myapp", "staging")
    
    if result['success']:
        print("🎉 Application deployed successfully!")
    else:
        print(f"😞 Deployment failed: {result['error']}")
```

---

**💡 Next Steps**: Practice these AWS automation patterns with real resources!
