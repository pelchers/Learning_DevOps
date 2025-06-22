# Automated Testing in CI/CD Pipelines

## 📖 What This File Covers
Master automated testing patterns within CI/CD pipelines. Learn how to implement comprehensive testing strategies that ensure code quality, security, and reliability throughout the deployment process.

## 🎯 Learning Objectives
- Design automated testing strategies for CI/CD pipelines
- Implement unit, integration, and end-to-end testing automation
- Create performance and security testing in pipelines
- Handle test data management and environment provisioning
- Optimize test execution for speed and reliability

---

## 🧪 **Testing Pipeline Architecture**

### **Multi-Stage Testing Strategy**
```yaml
# .github/workflows/comprehensive-testing.yml
name: Comprehensive Testing Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

jobs:
  # Stage 1: Fast feedback tests
  unit-tests:
    name: Unit Tests
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4
      
      - name: Setup Python
        uses: actions/setup-python@v4
        with:
          python-version: '3.9'
      
      - name: Install Dependencies
        run: |
          pip install -r requirements.txt
          pip install -r requirements-test.txt
      
      - name: Run Unit Tests
        run: |
          python -m pytest tests/unit/ \
            --junitxml=test-results/unit-tests.xml \
            --cov=src/ \
            --cov-report=xml
      
      - name: Upload Coverage
        uses: codecov/codecov-action@v3

  # Stage 2: Integration tests
  integration-tests:
    name: Integration Tests
    runs-on: ubuntu-latest
    needs: unit-tests
    
    services:
      postgres:
        image: postgres:13
        env:
          POSTGRES_PASSWORD: test_password
          POSTGRES_DB: test_db
    
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4
      
      - name: Run Integration Tests
        env:
          DATABASE_URL: postgresql://postgres:test_password@localhost:5432/test_db
        run: |
          python -m pytest tests/integration/ \
            --junitxml=test-results/integration-tests.xml

  # Stage 3: End-to-end tests
  e2e-tests:
    name: End-to-End Tests
    runs-on: ubuntu-latest
    needs: integration-tests
    
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4
      
      - name: Start Application Stack
        run: |
          docker-compose -f docker-compose.test.yml up -d
      
      - name: Wait for Services
        run: |
          ./scripts/wait-for-services.sh
      
      - name: Run E2E Tests
        run: |
          python -m pytest tests/e2e/ \
            --junitxml=test-results/e2e-tests.xml
      
      - name: Cleanup
        if: always()
        run: |
          docker-compose -f docker-compose.test.yml down -v

  # Stage 4: Performance tests
  performance-tests:
    name: Performance Tests
    runs-on: ubuntu-latest
    needs: e2e-tests
    if: github.ref == 'refs/heads/main'
    
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4
      
      - name: Setup K6
        run: |
          sudo apt-get update
          sudo apt-get install k6
      
      - name: Run Load Tests
        run: |
          k6 run tests/performance/load-test.js
```

---

## 🎯 **Test Automation Patterns**

### **Parallel Test Execution**
```python
# test_runner.py
import subprocess
import multiprocessing
from concurrent.futures import ThreadPoolExecutor
from typing import List, Dict, Any

class ParallelTestRunner:
    """Run tests in parallel to reduce execution time"""
    
    def __init__(self, max_workers: int = None):
        self.max_workers = max_workers or multiprocessing.cpu_count()
    
    def run_test_suites_parallel(self, test_suites: List[Dict[str, Any]]) -> Dict[str, Any]:
        """Run multiple test suites in parallel"""
        results = {}
        
        with ThreadPoolExecutor(max_workers=self.max_workers) as executor:
            future_to_suite = {
                executor.submit(self._run_single_suite, suite): suite['name']
                for suite in test_suites
            }
            
            for future in future_to_suite:
                suite_name = future_to_suite[future]
                try:
                    result = future.result()
                    results[suite_name] = result
                    print(f"✅ {suite_name} completed: {result['status']}")
                except Exception as e:
                    results[suite_name] = {
                        'status': 'failed',
                        'error': str(e)
                    }
                    print(f"❌ {suite_name} failed: {e}")
        
        return results
    
    def _run_single_suite(self, suite: Dict[str, Any]) -> Dict[str, Any]:
        """Run a single test suite"""
        import time
        start_time = time.time()
        
        cmd = [
            'python', '-m', 'pytest',
            suite['path'],
            '--junitxml=' + suite.get('output_file', f"results/{suite['name']}.xml")
        ]
        
        result = subprocess.run(cmd, capture_output=True, text=True)
        
        return {
            'status': 'passed' if result.returncode == 0 else 'failed',
            'duration': time.time() - start_time,
            'output': result.stdout
        }
```

---

## 📊 **Test Data Management**

### **Dynamic Test Data Generation**
```python
# test_data_factory.py
import random
from faker import Faker
from typing import Dict, Any

class TestDataFactory:
    """Generate realistic test data for automated tests"""
    
    def __init__(self, seed: int = None):
        self.fake = Faker()
        if seed:
            Faker.seed(seed)
    
    def create_user_data(self, role: str = 'user') -> Dict[str, Any]:
        """Create test user data"""
        return {
            'id': self.fake.uuid4(),
            'username': self.fake.user_name(),
            'email': self.fake.email(),
            'role': role,
            'created_at': self.fake.date_time_between(start_date='-1y').isoformat()
        }
    
    def create_deployment_data(self, environment: str = None) -> Dict[str, Any]:
        """Create test deployment data"""
        environments = ['development', 'staging', 'production']
        env = environment or self.fake.random_element(environments)
        
        return {
            'id': f"deploy-{self.fake.random_int(1000, 9999)}",
            'service_name': self.fake.random_element(['api-service', 'web-app']),
            'version': f"v{self.fake.random_int(1, 5)}.{self.fake.random_int(0, 10)}.0",
            'environment': env,
            'status': self.fake.random_element(['pending', 'successful', 'failed']),
            'created_by': self.fake.user_name()
        }
```

---

## 🚀 **Performance Testing Automation**

### **Load Testing with K6**
```javascript
// tests/performance/load-test.js
import http from 'k6/http';
import { check, sleep } from 'k6';
import { Rate } from 'k6/metrics';

const errorRate = new Rate('errors');

export const options = {
  stages: [
    { duration: '2m', target: 10 },
    { duration: '5m', target: 10 },
    { duration: '2m', target: 0 },
  ],
  thresholds: {
    http_req_duration: ['p(95)<500'],
    errors: ['rate<0.05'],
  },
};

const API_BASE_URL = __ENV.API_BASE_URL || 'http://localhost:8080';

export default function() {
  const response = http.get(`${API_BASE_URL}/api/v1/health`);
  
  check(response, {
    'status is 200': (r) => r.status === 200,
    'response time < 100ms': (r) => r.timings.duration < 100,
  });
  
  errorRate.add(response.status !== 200);
  sleep(1);
}
```

---

**💡 Key Takeaway**: Automated testing in CI/CD pipelines ensures consistent quality and provides confidence for rapid deployment cycles!
