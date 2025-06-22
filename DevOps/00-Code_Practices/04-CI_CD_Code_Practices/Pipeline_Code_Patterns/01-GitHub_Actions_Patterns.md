# GitHub Actions Pipeline Patterns

## 📖 What This File Covers
Master GitHub Actions pipeline patterns specifically for DevOps workflows. Learn how to build robust, maintainable, and efficient CI/CD pipelines that follow industry best practices.

## 🎯 Learning Objectives
- Build production-ready GitHub Actions workflows
- Implement proper testing, security, and deployment patterns
- Create reusable workflow components
- Handle secrets and environment configurations
- Implement deployment strategies (rolling, blue-green, canary)

---

## 🚀 **Basic Pipeline Structure**

### **Standard CI/CD Workflow Pattern**
```yaml
# .github/workflows/ci-cd.yml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main]

env:
  NODE_VERSION: '18'
  DOCKER_REGISTRY: 'ghcr.io'

jobs:
  # Job 1: Code Quality and Testing
  test:
    name: Run Tests and Quality Checks
    runs-on: ubuntu-latest
    
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4
      
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
          cache: 'npm'
      
      - name: Install Dependencies
        run: |
          npm ci
          npm audit --audit-level high
      
      - name: Run Tests
        run: npm test
      
      - name: Upload Coverage
        uses: codecov/codecov-action@v3

  # Job 2: Build and Package
  build:
    name: Build Application
    runs-on: ubuntu-latest
    needs: [test]
    
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4
      
      - name: Build Docker Image
        run: |
          docker build -t myapp:latest .
          docker tag myapp:latest ${{ env.DOCKER_REGISTRY }}/myapp:${{ github.sha }}
      
      - name: Push to Registry
        run: |
          echo ${{ secrets.GITHUB_TOKEN }} | docker login ${{ env.DOCKER_REGISTRY }} -u ${{ github.actor }} --password-stdin
          docker push ${{ env.DOCKER_REGISTRY }}/myapp:${{ github.sha }}

  # Job 3: Deploy
  deploy:
    name: Deploy to Production
    runs-on: ubuntu-latest
    needs: [build]
    if: github.ref == 'refs/heads/main'
    environment: production
    
    steps:
      - name: Deploy Application
        run: |
          echo "🚀 Deploying to production..."
          # Deployment logic here
```

---

## 🎯 **Advanced Pipeline Patterns**

### **Matrix Strategy for Multi-Environment Testing**
```yaml
jobs:
  test-matrix:
    name: Test on Multiple Environments
    runs-on: ${{ matrix.os }}
    
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest]
        node-version: [16, 18, 20]
    
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4
      
      - name: Setup Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
      
      - name: Run Tests
        run: npm test
```

### **Conditional Deployment Based on Changes**
```yaml
jobs:
  detect-changes:
    name: Detect Changes
    runs-on: ubuntu-latest
    outputs:
      frontend-changed: ${{ steps.changes.outputs.frontend }}
      backend-changed: ${{ steps.changes.outputs.backend }}
    
    steps:
      - name: Checkout Code
        uses: actions/checkout@v4
      
      - name: Detect Changes
        id: changes
        uses: dorny/paths-filter@v2
        with:
          filters: |
            frontend:
              - 'frontend/**'
              - '*.html'
              - '*.css'
            backend:
              - 'backend/**'
              - '*.py'
              - 'requirements.txt'

  deploy-frontend:
    name: Deploy Frontend
    runs-on: ubuntu-latest
    needs: detect-changes
    if: needs.detect-changes.outputs.frontend-changed == 'true'
    
    steps:
      - name: Deploy Frontend
        run: echo "🎨 Deploying frontend changes..."

  deploy-backend:
    name: Deploy Backend
    runs-on: ubuntu-latest
    needs: detect-changes
    if: needs.detect-changes.outputs.backend-changed == 'true'
    
    steps:
      - name: Deploy Backend
        run: echo "🔧 Deploying backend changes..."
```

---

## 🔒 **Security and Secrets Management**

### **Secure Secrets Handling Pattern**
```yaml
jobs:
  deploy:
    name: Secure Deployment
    runs-on: ubuntu-latest
    environment: production
    
    steps:
      - name: Configure AWS Credentials
        uses: aws-actions/configure-aws-credentials@v4
        with:
          role-to-assume: ${{ secrets.AWS_ROLE_ARN }}
          aws-region: us-east-1
      
      - name: Validate Secrets
        run: |
          if [ -z "${{ secrets.DATABASE_URL }}" ]; then
            echo "❌ DATABASE_URL secret is missing"
            exit 1
          fi
          echo "✅ All required secrets are present"
      
      - name: Deploy with Secrets
        env:
          DATABASE_URL: ${{ secrets.DATABASE_URL }}
          API_KEY: ${{ secrets.API_KEY }}
        run: |
          # Deploy application with secure configuration
          kubectl create secret generic app-secrets \
            --from-literal=database-url="$DATABASE_URL" \
            --from-literal=api-key="$API_KEY"
```

---

## 🔄 **Reusable Workflow Components**

### **Reusable Workflow Definition**
```yaml
# .github/workflows/reusable-deployment.yml
name: Reusable Deployment

on:
  workflow_call:
    inputs:
      environment:
        required: true
        type: string
      image-tag:
        required: true
        type: string
    secrets:
      KUBE_CONFIG:
        required: true

jobs:
  deploy:
    name: Deploy to ${{ inputs.environment }}
    runs-on: ubuntu-latest
    
    steps:
      - name: Deploy Application
        env:
          IMAGE_TAG: ${{ inputs.image-tag }}
        run: |
          echo "🚀 Deploying ${IMAGE_TAG} to ${{ inputs.environment }}"
          # Deployment logic here
```

### **Using Reusable Workflows**
```yaml
# .github/workflows/main-pipeline.yml
jobs:
  deploy-staging:
    uses: ./.github/workflows/reusable-deployment.yml
    with:
      environment: staging
      image-tag: ${{ needs.build.outputs.image-tag }}
    secrets:
      KUBE_CONFIG: ${{ secrets.KUBE_CONFIG_STAGING }}
```

---

## 📊 **Monitoring and Notifications**

### **Pipeline Monitoring Pattern**
```yaml
jobs:
  deploy:
    name: Deploy with Monitoring
    runs-on: ubuntu-latest
    
    steps:
      - name: Deploy Application
        id: deploy
        run: |
          echo "🚀 Deploying application..."
          # Deployment logic here
      
      - name: Send Slack Notification
        if: always()
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          text: |
            Deployment ${{ job.status }} for ${{ github.repository }}
            Branch: ${{ github.ref_name }}
            Commit: ${{ github.sha }}
        env:
          SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK }}
```

---

## 🎯 **Best Practices Summary**

### **Pipeline Structure**
- ✅ Use clear job names and descriptions
- ✅ Implement proper job dependencies
- ✅ Use conditional execution wisely
- ✅ Separate concerns (test, build, deploy)

### **Security**
- ✅ Use least privilege principles
- ✅ Store secrets securely
- ✅ Validate all inputs
- ✅ Use environment protection rules

### **Efficiency**
- ✅ Use caching for dependencies
- ✅ Implement matrix strategies
- ✅ Use conditional deployment
- ✅ Optimize build context

---

**💡 Remember**: Great CI/CD pipelines are fast, reliable, secure, and provide clear feedback!
