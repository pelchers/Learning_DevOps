# Python Installation and Setup for DevOps

## 📖 What This File Does
This guide covers Python installation, environment setup, and configuration specifically for DevOps workflows. It establishes the foundation for automation, scripting, and infrastructure management with Python.

## 🎯 Learning Objectives
- Install Python and essential tools on different operating systems
- Set up virtual environments for project isolation
- Configure Python development environment for DevOps
- Understand package management with pip and pipenv
- Install and configure essential DevOps Python libraries

## 📋 Prerequisites
- Basic command-line knowledge
- Administrative access to your system
- Understanding of environment variables
- Familiarity with package managers

---

## Python Installation

### Python Version Strategy for DevOps

#### Recommended Versions
```bash
# Current recommended versions (as of 2024)
Python 3.11.x - Latest stable, excellent performance
Python 3.10.x - Long-term support, widely supported
Python 3.9.x  - Minimum for modern DevOps tools

# Check installed Python versions
python3 --version
python3.11 --version
which python3
```

#### Version Management
```bash
# Using pyenv for multiple Python versions
curl https://pyenv.run | bash

# Install multiple Python versions
pyenv install 3.11.7
pyenv install 3.10.13
pyenv install 3.9.18

# Set global and local versions
pyenv global 3.11.7
pyenv local 3.10.13  # For specific project
```

---

## Installation by Operating System

### Ubuntu/Debian Installation
```bash
# Update package index
sudo apt update

# Install Python and essential tools
sudo apt install -y python3 python3-pip python3-venv python3-dev

# Install additional development tools
sudo apt install -y build-essential libssl-dev libffi-dev

# Install Python version management
sudo apt install -y python3.11 python3.11-venv python3.11-dev

# Verify installation
python3 --version
pip3 --version
```

### CentOS/RHEL/Fedora Installation
```bash
# CentOS/RHEL 8+
sudo dnf install -y python3 python3-pip python3-devel

# CentOS/RHEL 7
sudo yum install -y python3 python3-pip python3-devel

# Fedora
sudo dnf install -y python3 python3-pip python3-devel python3-virtualenv

# Install development tools
sudo dnf groupinstall -y "Development Tools"
sudo dnf install -y openssl-devel libffi-devel
```

### Windows Installation
```powershell
# Using Windows Package Manager
winget install Python.Python.3.11

# Using Chocolatey
choco install python --version=3.11.7

# Using Scoop
scoop install python

# Verify installation
python --version
pip --version

# Add to PATH if needed
$env:PATH += ";C:\Python311;C:\Python311\Scripts"
```

### macOS Installation
```bash
# Using Homebrew (Recommended)
brew install python@3.11

# Using pyenv (for version management)
brew install pyenv
pyenv install 3.11.7
pyenv global 3.11.7

# Update shell profile
echo 'eval "$(pyenv init -)"' >> ~/.zshrc
source ~/.zshrc

# Verify installation
python3 --version
pip3 --version
```

---

## Virtual Environment Setup

### Why Virtual Environments?
- **Isolation**: Separate dependencies per project
- **Reproducibility**: Consistent environments across systems
- **Cleanup**: Easy to remove without affecting system Python
- **Security**: Avoid permission issues with system packages

### Using venv (Built-in)
```bash
# Create virtual environment
python3 -m venv devops-env

# Activate environment
# Linux/macOS:
source devops-env/bin/activate

# Windows:
devops-env\Scripts\activate

# Deactivate environment
deactivate

# Remove environment
rm -rf devops-env
```

### Using virtualenv
```bash
# Install virtualenv
pip3 install virtualenv

# Create environment
virtualenv -p python3.11 devops-env

# Create with specific Python version
virtualenv --python=/usr/bin/python3.11 devops-env

# Activate and use
source devops-env/bin/activate
python --version
```

### Using conda (Anaconda/Miniconda)
```bash
# Install Miniconda
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
bash Miniconda3-latest-Linux-x86_64.sh

# Create environment
conda create -n devops-env python=3.11

# Activate environment
conda activate devops-env

# Install packages
conda install requests pyyaml

# List environments
conda env list

# Export environment
conda env export > environment.yml

# Create from file
conda env create -f environment.yml
```

---

## Package Management

### pip (Package Installer for Python)

#### Basic pip Usage
```bash
# Upgrade pip
python -m pip install --upgrade pip

# Install packages
pip install requests
pip install boto3==1.28.0  # Specific version
pip install 'django>=4.0,<5.0'  # Version range

# Install from requirements file
pip install -r requirements.txt

# Install in development mode
pip install -e .

# Uninstall packages
pip uninstall requests
pip uninstall -r requirements.txt
```

#### Package Management Best Practices
```bash
# Generate requirements file
pip freeze > requirements.txt

# Better: Use pipreqs for actual imports
pip install pipreqs
pipreqs . --force

# Install only production dependencies
pip install -r requirements.txt --no-deps

# Check for security vulnerabilities
pip install safety
safety check
```

### pipenv (Advanced Package Management)
```bash
# Install pipenv
pip install pipenv

# Create new project with virtual environment
pipenv install

# Install packages
pipenv install requests
pipenv install pytest --dev  # Development dependency

# Install from Pipfile
pipenv install

# Activate shell
pipenv shell

# Run commands in environment
pipenv run python script.py

# Generate requirements.txt
pipenv requirements > requirements.txt
pipenv requirements --dev > requirements-dev.txt
```

#### Pipfile Example
```toml
[[source]]
url = "https://pypi.org/simple"
verify_ssl = true
name = "pypi"

[packages]
requests = "*"
boto3 = ">=1.28.0"
pyyaml = "*"
click = "*"
jinja2 = "*"

[dev-packages]
pytest = "*"
black = "*"
flake8 = "*"
mypy = "*"

[requires]
python_version = "3.11"

[scripts]
test = "pytest tests/"
lint = "flake8 src/"
format = "black src/"
```

### Poetry (Modern Dependency Management)
```bash
# Install Poetry
curl -sSL https://install.python-poetry.org | python3 -

# Create new project
poetry new devops-project
cd devops-project

# Initialize existing project
poetry init

# Add dependencies
poetry add requests
poetry add pytest --group dev

# Install dependencies
poetry install

# Run commands
poetry run python main.py
poetry run pytest

# Build package
poetry build

# Publish package
poetry publish
```

---

## Essential DevOps Python Libraries

### Core Libraries Installation
```bash
# Activate your virtual environment first
source devops-env/bin/activate

# System and OS operations
pip install psutil          # System monitoring
pip install paramiko         # SSH connections
pip install fabric           # Remote execution

# Cloud platforms
pip install boto3            # AWS SDK
pip install azure-mgmt-core  # Azure SDK
pip install google-cloud    # Google Cloud SDK

# Configuration and data
pip install pyyaml           # YAML parsing
pip install toml             # TOML parsing
pip install python-dotenv   # Environment variables
pip install configparser    # Configuration files

# HTTP clients and APIs
pip install requests         # HTTP requests
pip install httpx           # Async HTTP client
pip install urllib3         # HTTP library

# Command-line interfaces
pip install click            # CLI framework
pip install typer            # Modern CLI framework
pip install argparse         # Built-in argument parsing

# Template engines
pip install jinja2           # Template engine
pip install chevron          # Mustache templates

# Monitoring and logging
pip install prometheus-client # Prometheus metrics
pip install structlog        # Structured logging
pip install loguru           # Enhanced logging

# DevOps tools
pip install ansible          # Configuration management
pip install docker           # Docker client
pip install kubernetes       # Kubernetes client
pip install terraform-compliance # Terraform testing
```

### Requirements File for DevOps
```txt
# requirements-devops.txt

# Core system libraries
psutil>=5.9.0
paramiko>=3.0.0
fabric>=3.0.0

# Cloud SDKs
boto3>=1.28.0
azure-mgmt-core>=1.4.0
google-cloud>=0.34.0

# Configuration and data parsing
PyYAML>=6.0
toml>=0.10.2
python-dotenv>=1.0.0

# HTTP and networking
requests>=2.31.0
httpx>=0.24.0

# CLI development
click>=8.1.0
typer>=0.9.0

# Templates and text processing
Jinja2>=3.1.0

# Monitoring and observability
prometheus-client>=0.17.0
structlog>=23.0.0

# Container and orchestration
docker>=6.1.0
kubernetes>=27.0.0

# Testing and quality
pytest>=7.4.0
pytest-cov>=4.1.0
black>=23.7.0
flake8>=6.0.0
mypy>=1.5.0
safety>=2.3.0

# DevOps tools
ansible>=8.0.0
terraform-compliance>=1.3.0
```

---

## Development Environment Configuration

### IDE and Editor Setup

#### Visual Studio Code
```json
// .vscode/settings.json
{
    "python.defaultInterpreterPath": "./devops-env/bin/python",
    "python.linting.enabled": true,
    "python.linting.flake8Enabled": true,
    "python.linting.mypyEnabled": true,
    "python.formatting.provider": "black",
    "python.formatting.blackPath": "./devops-env/bin/black",
    "python.testing.pytestEnabled": true,
    "python.testing.pytestPath": "./devops-env/bin/pytest",
    "files.associations": {
        "*.yml": "yaml",
        "*.yaml": "yaml"
    }
}
```

#### Extensions for DevOps
```bash
# Install VS Code extensions
code --install-extension ms-python.python
code --install-extension ms-python.flake8
code --install-extension ms-python.black-formatter
code --install-extension redhat.vscode-yaml
code --install-extension hashicorp.terraform
code --install-extension ms-vscode.docker
code --install-extension ms-kubernetes-tools.vscode-kubernetes-tools
```

### Git Configuration for Python
```bash
# .gitignore for Python projects
cat > .gitignore << 'EOF'
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
env/
venv/
ENV/
env.bak/
venv.bak/

# Distribution / packaging
.Python
build/
develop-eggs/
dist/
downloads/
eggs/
.eggs/
lib/
lib64/
parts/
sdist/
var/
wheels/
*.egg-info/
.installed.cfg
*.egg

# PyInstaller
*.manifest
*.spec

# Unit test / coverage reports
htmlcov/
.tox/
.coverage
.coverage.*
.cache
nosetests.xml
coverage.xml
*.cover
.hypothesis/
.pytest_cache/

# Virtual environments
venv/
ENV/
env/
.venv/

# IDEs
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
.DS_Store?
._*
.Spotlight-V100
.Trashes
ehthumbs.db
Thumbs.db

# DevOps specific
secrets.yml
.env
*.pem
*.key
terraform.tfstate*
.terraform/
EOF
```

---

## Python Configuration for DevOps

### Environment Variables Management
```python
# config.py
import os
from dotenv import load_dotenv

# Load environment variables from .env file
load_dotenv()

class Config:
    # AWS Configuration
    AWS_ACCESS_KEY_ID = os.getenv('AWS_ACCESS_KEY_ID')
    AWS_SECRET_ACCESS_KEY = os.getenv('AWS_SECRET_ACCESS_KEY')
    AWS_DEFAULT_REGION = os.getenv('AWS_DEFAULT_REGION', 'us-east-1')
    
    # Database Configuration
    DATABASE_URL = os.getenv('DATABASE_URL')
    
    # API Configuration
    API_BASE_URL = os.getenv('API_BASE_URL', 'https://api.example.com')
    API_TOKEN = os.getenv('API_TOKEN')
    
    # Logging Configuration
    LOG_LEVEL = os.getenv('LOG_LEVEL', 'INFO')
    LOG_FORMAT = os.getenv('LOG_FORMAT', 'json')
    
    @classmethod
    def validate(cls):
        """Validate required environment variables"""
        required = ['AWS_ACCESS_KEY_ID', 'AWS_SECRET_ACCESS_KEY', 'API_TOKEN']
        missing = [var for var in required if not getattr(cls, var)]
        if missing:
            raise ValueError(f"Missing required environment variables: {missing}")
```

### Logging Configuration
```python
# logging_config.py
import logging
import structlog
from pathlib import Path

def setup_logging(log_level='INFO', log_format='json'):
    """Setup structured logging for DevOps applications"""
    
    # Create logs directory
    log_dir = Path('logs')
    log_dir.mkdir(exist_ok=True)
    
    if log_format == 'json':
        # JSON structured logging
        structlog.configure(
            processors=[
                structlog.stdlib.filter_by_level,
                structlog.stdlib.add_logger_name,
                structlog.stdlib.add_log_level,
                structlog.stdlib.PositionalArgumentsFormatter(),
                structlog.processors.TimeStamper(fmt="iso"),
                structlog.processors.StackInfoRenderer(),
                structlog.processors.format_exc_info,
                structlog.processors.UnicodeDecoder(),
                structlog.processors.JSONRenderer()
            ],
            context_class=dict,
            logger_factory=structlog.stdlib.LoggerFactory(),
            wrapper_class=structlog.stdlib.BoundLogger,
            cache_logger_on_first_use=True,
        )
    
    # Configure standard logging
    logging.basicConfig(
        format="%(message)s",
        stream=sys.stdout,
        level=getattr(logging, log_level.upper()),
        handlers=[
            logging.FileHandler(log_dir / 'devops.log'),
            logging.StreamHandler()
        ]
    )
    
    return structlog.get_logger()
```

---

## Testing Environment Setup

### pytest Configuration
```ini
# pytest.ini
[tool:pytest]
testpaths = tests
python_files = test_*.py
python_classes = Test*
python_functions = test_*
addopts = 
    -v
    --cov=src
    --cov-report=html
    --cov-report=term-missing
    --cov-fail-under=80
    --strict-markers
    --disable-warnings
markers =
    slow: marks tests as slow
    integration: marks tests as integration tests
    unit: marks tests as unit tests
    aws: marks tests that require AWS credentials
```

### Test Structure
```bash
# Create test directory structure
mkdir -p tests/{unit,integration,fixtures}
touch tests/__init__.py
touch tests/conftest.py
touch tests/unit/__init__.py
touch tests/integration/__init__.py

# tests/conftest.py
import pytest
import os
from unittest.mock import Mock

@pytest.fixture
def aws_credentials():
    """Mock AWS credentials for testing"""
    os.environ['AWS_ACCESS_KEY_ID'] = 'testing'
    os.environ['AWS_SECRET_ACCESS_KEY'] = 'testing'
    os.environ['AWS_SECURITY_TOKEN'] = 'testing'
    os.environ['AWS_SESSION_TOKEN'] = 'testing'

@pytest.fixture
def mock_boto3_client():
    """Mock boto3 client"""
    return Mock()
```

---

## Performance and Security

### Python Security Tools
```bash
# Install security tools
pip install bandit safety semgrep

# Run security checks
bandit -r src/
safety check
semgrep --config=auto src/

# Create security check script
cat > security_check.sh << 'EOF'
#!/bin/bash
echo "Running Python security checks..."

echo "1. Bandit (Security linter)"
bandit -r src/ -f json -o bandit-report.json

echo "2. Safety (Dependency vulnerabilities)"
safety check --json --output safety-report.json

echo "3. Semgrep (Static analysis)"
semgrep --config=auto --json -o semgrep-report.json src/

echo "Security checks completed. Check report files for details."
EOF

chmod +x security_check.sh
```

### Performance Monitoring
```python
# performance_monitor.py
import time
import psutil
import functools
from typing import Callable, Any

def monitor_performance(func: Callable) -> Callable:
    """Decorator to monitor function performance"""
    @functools.wraps(func)
    def wrapper(*args, **kwargs) -> Any:
        # Record start metrics
        start_time = time.time()
        start_memory = psutil.Process().memory_info().rss / 1024 / 1024  # MB
        
        try:
            result = func(*args, **kwargs)
            return result
        finally:
            # Record end metrics
            end_time = time.time()
            end_memory = psutil.Process().memory_info().rss / 1024 / 1024  # MB
            
            duration = end_time - start_time
            memory_diff = end_memory - start_memory
            
            print(f"Function: {func.__name__}")
            print(f"Duration: {duration:.2f} seconds")
            print(f"Memory change: {memory_diff:.2f} MB")
    
    return wrapper

# Usage example
@monitor_performance
def process_large_dataset(data):
    # Your DevOps processing logic here
    pass
```

---

## Automation Scripts

### Environment Setup Script
```bash
#!/bin/bash
# setup_python_devops.sh

set -e  # Exit on any error

echo "Setting up Python DevOps environment..."

# Check Python version
python3 --version || { echo "Python 3 not found. Please install Python 3.9+"; exit 1; }

# Create virtual environment
if [ ! -d "devops-env" ]; then
    echo "Creating virtual environment..."
    python3 -m venv devops-env
fi

# Activate virtual environment
source devops-env/bin/activate

# Upgrade pip
echo "Upgrading pip..."
pip install --upgrade pip

# Install requirements
if [ -f "requirements-devops.txt" ]; then
    echo "Installing DevOps requirements..."
    pip install -r requirements-devops.txt
else
    echo "Installing basic DevOps packages..."
    pip install boto3 pyyaml requests click jinja2 paramiko psutil
fi

# Install development tools
echo "Installing development tools..."
pip install pytest black flake8 mypy safety bandit

# Create project structure
mkdir -p {src,tests,scripts,configs,logs}
touch src/__init__.py tests/__init__.py

echo "Python DevOps environment setup completed!"
echo "Activate with: source devops-env/bin/activate"
```

### Validation Script
```python
#!/usr/bin/env python3
# validate_environment.py

import sys
import subprocess
import importlib
from pathlib import Path

def check_python_version():
    """Check Python version compatibility"""
    version = sys.version_info
    if version.major != 3 or version.minor < 9:
        print(f"❌ Python {version.major}.{version.minor} is not supported. Use Python 3.9+")
        return False
    print(f"✅ Python {version.major}.{version.minor}.{version.micro}")
    return True

def check_required_packages():
    """Check if required packages are installed"""
    required_packages = [
        'boto3', 'requests', 'pyyaml', 'click', 
        'jinja2', 'paramiko', 'psutil', 'pytest'
    ]
    
    missing_packages = []
    for package in required_packages:
        try:
            importlib.import_module(package)
            print(f"✅ {package}")
        except ImportError:
            print(f"❌ {package} - not installed")
            missing_packages.append(package)
    
    return len(missing_packages) == 0, missing_packages

def check_environment_setup():
    """Check if development environment is properly set up"""
    checks = []
    
    # Check virtual environment
    if hasattr(sys, 'real_prefix') or (hasattr(sys, 'base_prefix') and sys.base_prefix != sys.prefix):
        print("✅ Virtual environment active")
        checks.append(True)
    else:
        print("❌ No virtual environment detected")
        checks.append(False)
    
    # Check project structure
    required_dirs = ['src', 'tests', 'scripts']
    for dir_name in required_dirs:
        if Path(dir_name).exists():
            print(f"✅ {dir_name}/ directory exists")
            checks.append(True)
        else:
            print(f"❌ {dir_name}/ directory missing")
            checks.append(False)
    
    return all(checks)

def main():
    """Main validation function"""
    print("Python DevOps Environment Validation")
    print("=" * 40)
    
    all_checks = []
    
    # Check Python version
    all_checks.append(check_python_version())
    
    # Check required packages
    packages_ok, missing = check_required_packages()
    all_checks.append(packages_ok)
    
    if missing:
        print(f"\nInstall missing packages with:")
        print(f"pip install {' '.join(missing)}")
    
    # Check environment setup
    all_checks.append(check_environment_setup())
    
    print("\n" + "=" * 40)
    if all(all_checks):
        print("✅ All checks passed! Environment is ready for DevOps development.")
        sys.exit(0)
    else:
        print("❌ Some checks failed. Please address the issues above.")
        sys.exit(1)

if __name__ == "__main__":
    main()
```

---

## Next Steps

After completing Python installation and setup:

1. **Learn Python fundamentals** → See `02-Python_Fundamentals_For_DevOps.md`
2. **Practice automation scripting** → See `../Automation_Scripting/`
3. **Explore DevOps libraries** → See `../DevOps_Specific_Libraries/`
4. **Build infrastructure tools** → See later modules

---

## 🔧 Configuration Notes

- **Virtual Environments**: Always use virtual environments for project isolation
- **Package Management**: Choose pipenv or poetry for advanced dependency management
- **Security**: Regularly audit dependencies with safety and bandit
- **Performance**: Monitor resource usage in production automation scripts

## 📚 Additional Resources

### Essential Reading
- ["The Lean Startup" by Eric Ries](https://www.amazon.com/Lean-Startup-Entrepreneurs-Continuous-Innovation/dp/0307887898) - Rapid prototyping and automation principles
- ["Team of Teams" by General Stanley McChrystal](https://www.amazon.com/Team-Teams-Rules-Engagement-Complex/dp/1591847486) - Scaling Python automation across organizations
- ["The Five Dysfunctions of a Team" by Patrick Lencioni](https://www.amazon.com/Five-Dysfunctions-Team-Leadership-Fable/dp/0787960756) - Collaborative development practices
- ["Fearless Change" by Linda Rising and Mary Lynn Manns](https://www.amazon.com/Fearless-Change-Patterns-Introducing-Ideas/dp/0201755920) - Introducing Python in DevOps environments

### Videos
- [Python for DevOps (TechWorld with Nana)](https://www.youtube.com/watch?v=Hsn5doE6jZ4) - Complete Python DevOps course
- [Python Virtual Environments (Corey Schafer)](https://www.youtube.com/watch?v=Kg1Yvry_Ydk) - Environment management mastery
- [Python Package Management (Real Python)](https://www.youtube.com/watch?v=Kg1Yvry_Ydk) - pip, pipenv, and poetry
- [Python Automation (freeCodeCamp)](https://www.youtube.com/watch?v=PXMJ6FS7llk) - Automation fundamentals

### Guides
- [Python Official Documentation](https://docs.python.org/3/)
- [Real Python DevOps Tutorials](https://realpython.com/learning-paths/python-devops/)
- [Python Package Index (PyPI)](https://pypi.org/)
- [Python DevOps Libraries](https://github.com/mahmoud/awesome-python-applications#devops)
- [Python Virtual Environments Guide](https://realpython.com/python-virtual-environments-a-primer/)
- [Python Installation Guide](https://realpython.com/installing-python/)

### Articles
- [Python for DevOps: Getting Started](https://www.digitalocean.com/community/tutorials/python-for-devops)
- [Managing Python Dependencies](https://realpython.com/pipenv-guide/)
- [Python Security Best Practices](https://python-security.readthedocs.io/)
- [Python Performance Optimization](https://realpython.com/python-performance/)

### Cultural Assessment Tools
- [Python Development Maturity Assessment](https://www.devops-culture.com/assessment/)
- [Automation Readiness Evaluation](https://www.python.org/dev/peps/pep-0008/)

### Communities and Events
- [Python Software Foundation](https://www.python.org/psf/)
- [Python DevOps Community](https://www.reddit.com/r/devops/)
- [Local Python Meetups](https://www.meetup.com/topics/python/)
- [PyCon Events](https://pycon.org/)
- [Python Security Best Practices](https://python-security.readthedocs.io/) 