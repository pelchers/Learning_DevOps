# GitHub Actions Basics

## 📖 What This File Does
This guide covers GitHub Actions fundamentals, workflow creation, and basic CI/CD automation. It introduces GitHub's built-in automation platform for DevOps workflows.

## 🎯 Learning Objectives
- Understand GitHub Actions concepts and terminology
- Create basic workflows and actions
- Implement automated testing and deployment
- Use GitHub marketplace actions
- Manage secrets and environment variables

## 📋 Prerequisites
- GitHub account and repository access
- Basic Git knowledge (see `../Git_Fundamentals/`)
- Understanding of YAML syntax
- Command-line experience

---

## GitHub Actions Overview

### What are GitHub Actions?
GitHub Actions is a CI/CD platform that automates software workflows directly in your GitHub repository.

**Key Benefits:**
- **Integrated**: Built into GitHub repositories
- **Event-driven**: Triggered by repository events
- **Flexible**: Supports any language and platform
- **Community**: Extensive marketplace of actions
- **Free tier**: Generous usage limits for public repos

### Core Concepts

#### Workflows
YAML files that define automated processes:
```yaml
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: npm test
```

#### Events
Triggers that start workflows:
- `push`: Code pushed to repository
- `pull_request`: PR opened/updated
- `schedule`: Time-based triggers
- `workflow_dispatch`: Manual triggers

#### Jobs
Groups of steps that run on the same runner:
- Run in parallel by default
- Can have dependencies
- Each job runs in fresh environment

#### Steps
Individual tasks within a job:
- Can run commands or use actions
- Share environment within job
- Execute sequentially

#### Runners
Virtual machines that execute jobs:
- GitHub-hosted (Ubuntu, Windows, macOS)
- Self-hosted (your own infrastructure)

---

## Creating Your First Workflow

### Basic Workflow Structure
```yaml
name: My First Workflow
on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  hello-world:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
      
      - name: Say hello
        run: echo "Hello, GitHub Actions!"
      
      - name: Show environment
        run: |
          echo "Runner OS: ${{ runner.os }}"
          echo "GitHub Actor: ${{ github.actor }}"
          echo "Repository: ${{ github.repository }}"
```

### File Location
Save workflows in: `.github/workflows/filename.yml`

### Example: Node.js CI Workflow
```yaml
name: Node.js CI

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  test:
    runs-on: ubuntu-latest
    
    strategy:
      matrix:
        node-version: [14, 16, 18]
    
    steps:
    - name: Checkout repository
      uses: actions/checkout@v3
    
    - name: Setup Node.js ${{ matrix.node-version }}
      uses: actions/setup-node@v3
      with:
        node-version: ${{ matrix.node-version }}
        cache: 'npm'
    
    - name: Install dependencies
      run: npm ci
    
    - name: Run linter
      run: npm run lint
    
    - name: Run tests
      run: npm test
    
    - name: Generate coverage report
      run: npm run coverage
    
    - name: Upload coverage to Codecov
      uses: codecov/codecov-action@v3
      with:
        file: ./coverage/lcov.info
```

---

## Common Workflow Patterns

### 1. Continuous Integration (CI)
```yaml
name: CI
on: [push, pull_request]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      - run: npm ci
      - run: npm run lint

  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        node-version: [16, 18, 20]
    steps:
      - uses: actions/checkout@v3
      - name: Setup Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'
      - run: npm ci
      - run: npm test

  build:
    runs-on: ubuntu-latest
    needs: [lint, test]
    steps:
      - uses: actions/checkout@v3
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'npm'
      - run: npm ci
      - run: npm run build
      - name: Upload build artifacts
        uses: actions/upload-artifact@v3
        with:
          name: build-files
          path: dist/
```

### 2. Continuous Deployment (CD)
```yaml
name: Deploy to Production

on:
  push:
    branches: [ main ]
    tags: [ 'v*' ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    environment: production
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v3
    
    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'
        cache: 'npm'
    
    - name: Install dependencies
      run: npm ci
    
    - name: Build application
      run: npm run build
    
    - name: Deploy to server
      env:
        DEPLOY_HOST: ${{ secrets.DEPLOY_HOST }}
        DEPLOY_USER: ${{ secrets.DEPLOY_USER }}
        DEPLOY_KEY: ${{ secrets.DEPLOY_KEY }}
      run: |
        # Install SSH key
        mkdir -p ~/.ssh
        echo "$DEPLOY_KEY" > ~/.ssh/deploy_key
        chmod 600 ~/.ssh/deploy_key
        
        # Deploy files
        rsync -avz -e "ssh -i ~/.ssh/deploy_key -o StrictHostKeyChecking=no" \
          dist/ $DEPLOY_USER@$DEPLOY_HOST:/var/www/html/
```

### 3. Scheduled Workflows
```yaml
name: Daily Health Check

on:
  schedule:
    # Run at 6 AM UTC every day
    - cron: '0 6 * * *'
  workflow_dispatch: # Allow manual trigger

jobs:
  health-check:
    runs-on: ubuntu-latest
    steps:
    - name: Check website health
      run: |
        response=$(curl -s -o /dev/null -w "%{http_code}" https://mywebsite.com)
        if [ $response -ne 200 ]; then
          echo "Website is down! Status code: $response"
          exit 1
        fi
        echo "Website is healthy (Status: $response)"
    
    - name: Notify on failure
      if: failure()
      uses: actions/github-script@v6
      with:
        script: |
          github.rest.issues.create({
            owner: context.repo.owner,
            repo: context.repo.repo,
            title: 'Website Health Check Failed',
            body: 'The daily health check failed. Please investigate.'
          })
```

---

## Working with Actions

### Using Marketplace Actions
GitHub Marketplace offers thousands of pre-built actions:

```yaml
steps:
  # Checkout code
  - uses: actions/checkout@v3
  
  # Setup programming languages
  - uses: actions/setup-node@v3
  - uses: actions/setup-python@v4
  - uses: actions/setup-java@v3
  
  # Deploy to cloud platforms
  - uses: azure/webapps-deploy@v2
  - uses: aws-actions/configure-aws-credentials@v2
  
  # Notify services
  - uses: 8398a7/action-slack@v3
  - uses: technote-space/slack-webhook@v1
```

### Custom Actions
Create reusable actions for your organization:

#### Composite Action Example
```yaml
# .github/actions/setup-project/action.yml
name: 'Setup Project'
description: 'Setup Node.js project with caching'
inputs:
  node-version:
    description: 'Node.js version'
    required: false
    default: '18'
  
runs:
  using: 'composite'
  steps:
    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: ${{ inputs.node-version }}
        cache: 'npm'
    
    - name: Install dependencies
      run: npm ci
      shell: bash
    
    - name: Cache node_modules
      uses: actions/cache@v3
      with:
        path: node_modules
        key: ${{ runner.os }}-node-${{ hashFiles('package-lock.json') }}
```

#### Using Custom Action
```yaml
steps:
  - uses: actions/checkout@v3
  - uses: ./.github/actions/setup-project
    with:
      node-version: '18'
```

---

## Managing Secrets and Variables

### Repository Secrets
Store sensitive information securely:

1. Go to repository → Settings → Secrets and variables → Actions
2. Add secrets like API keys, passwords, tokens
3. Use in workflows:

```yaml
steps:
  - name: Deploy to staging
    env:
      API_KEY: ${{ secrets.API_KEY }}
      DATABASE_URL: ${{ secrets.DATABASE_URL }}
    run: ./deploy.sh
```

### Environment Variables
```yaml
env:
  NODE_ENV: production
  API_URL: https://api.example.com

jobs:
  deploy:
    runs-on: ubuntu-latest
    env:
      DEPLOY_ENV: staging
    steps:
      - name: Show environment
        run: |
          echo "Node environment: $NODE_ENV"
          echo "API URL: $API_URL"
          echo "Deploy environment: $DEPLOY_ENV"
```

### Environment Protection
Create protected environments:

```yaml
jobs:
  deploy-production:
    runs-on: ubuntu-latest
    environment: production  # Requires approval
    steps:
      - name: Deploy to production
        run: echo "Deploying to production..."
```

---

## Advanced Workflow Features

### Matrix Builds
Test across multiple configurations:

```yaml
jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        node-version: [16, 18, 20]
        include:
          - os: ubuntu-latest
            node-version: 20
            experimental: true
        exclude:
          - os: windows-latest
            node-version: 16
    
    steps:
      - uses: actions/checkout@v3
      - name: Setup Node.js ${{ matrix.node-version }}
        uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node-version }}
      - run: npm test
```

### Conditional Execution
```yaml
steps:
  - name: Run only on main branch
    if: github.ref == 'refs/heads/main'
    run: echo "This runs only on main branch"
  
  - name: Run only on pull requests
    if: github.event_name == 'pull_request'
    run: echo "This runs only on PRs"
  
  - name: Run only on tags
    if: startsWith(github.ref, 'refs/tags/')
    run: echo "This runs only on tags"
  
  - name: Deploy only on success
    if: success()
    run: ./deploy.sh
  
  - name: Notify on failure
    if: failure()
    run: ./notify-failure.sh
```

### Artifacts and Outputs
```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    outputs:
      version: ${{ steps.get-version.outputs.version }}
    steps:
      - uses: actions/checkout@v3
      - name: Build application
        run: npm run build
      
      - name: Get version
        id: get-version
        run: echo "version=$(npm run version --silent)" >> $GITHUB_OUTPUT
      
      - name: Upload build artifacts
        uses: actions/upload-artifact@v3
        with:
          name: build-${{ steps.get-version.outputs.version }}
          path: dist/
          retention-days: 30

  deploy:
    needs: build
    runs-on: ubuntu-latest
    steps:
      - name: Download artifacts
        uses: actions/download-artifact@v3
        with:
          name: build-${{ needs.build.outputs.version }}
          path: dist/
      
      - name: Deploy version ${{ needs.build.outputs.version }}
        run: ./deploy.sh dist/
```

---

## Debugging and Monitoring

### Workflow Status Badges
Add status badges to README:
```markdown
![CI](https://github.com/username/repo/workflows/CI/badge.svg)
![Deploy](https://github.com/username/repo/workflows/Deploy/badge.svg?branch=main)
```

### Debug Logging
```yaml
steps:
  - name: Enable debug logging
    run: echo "ACTIONS_STEP_DEBUG=true" >> $GITHUB_ENV
  
  - name: Debug information
    run: |
      echo "::debug::This is a debug message"
      echo "::notice::This is a notice"
      echo "::warning::This is a warning"
      echo "::error::This is an error"
```

### Job Summaries
```yaml
steps:
  - name: Generate job summary
    run: |
      echo "# Test Results" >> $GITHUB_STEP_SUMMARY
      echo "✅ All tests passed" >> $GITHUB_STEP_SUMMARY
      echo "📊 Coverage: 95%" >> $GITHUB_STEP_SUMMARY
      echo "" >> $GITHUB_STEP_SUMMARY
      echo "| Test Suite | Status |" >> $GITHUB_STEP_SUMMARY
      echo "|------------|--------|" >> $GITHUB_STEP_SUMMARY
      echo "| Unit Tests | ✅ Pass |" >> $GITHUB_STEP_SUMMARY
      echo "| Integration | ✅ Pass |" >> $GITHUB_STEP_SUMMARY
```

---

## Security Best Practices

### Secure Workflows
```yaml
name: Secure CI

on:
  pull_request_target: # Be careful with this trigger
    types: [opened, synchronize]

jobs:
  security-check:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      security-events: write
    
    steps:
      - name: Checkout code
        uses: actions/checkout@v3
        with:
          ref: ${{ github.event.pull_request.head.sha }}
      
      - name: Run security scan
        uses: github/super-linter@v4
        env:
          DEFAULT_BRANCH: main
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
          VALIDATE_ALL_CODEBASE: false
```

### Permission Management
```yaml
permissions:
  contents: read
  issues: write
  pull-requests: write
  security-events: write
```

### Secret Scanning
```yaml
- name: Secret scan
  uses: trufflesecurity/trufflehog@v3
  with:
    path: ./
    base: main
    head: HEAD
```

---

## Troubleshooting Common Issues

### Issue 1: Workflow Not Triggering
```yaml
# Check trigger conditions
on:
  push:
    branches: [ main ]    # Specify correct branch
    paths:               # Trigger only on specific paths
      - 'src/**'
      - 'package.json'
```

### Issue 2: Permission Denied
```yaml
# Add necessary permissions
permissions:
  contents: write
  pages: write
  id-token: write
```

### Issue 3: Timeout Issues
```yaml
jobs:
  test:
    runs-on: ubuntu-latest
    timeout-minutes: 30    # Set appropriate timeout
    steps:
      - name: Long running task
        timeout-minutes: 10  # Per-step timeout
        run: ./long-task.sh
```

---

## Next Steps

After mastering GitHub Actions basics:

1. **Learn advanced workflows** → See `02-Pull_Request_Workflows.md`
2. **Implement CI/CD pipelines** → See `03-CICD_With_GitHub.md`
3. **Practice collaboration** → See `../Collaboration_Strategies/`
4. **Explore container workflows** → See Docker modules

---

## 🔧 Configuration Notes

- **Rate Limits**: Be aware of GitHub Actions usage limits
- **Runner Selection**: Choose appropriate runners for your needs
- **Workflow Organization**: Keep workflows modular and reusable
- **Security**: Always use least privilege principle

## 📚 Additional Resources

### Essential Reading
- ["The Lean Startup" by Eric Ries](https://www.amazon.com/Lean-Startup-Entrepreneurs-Continuous-Innovation/dp/0307887898) - Continuous integration and automation principles
- ["Team of Teams" by General Stanley McChrystal](https://www.amazon.com/Team-Teams-Rules-Engagement-Complex/dp/1591847486) - Scaling automation across organizations
- ["The Five Dysfunctions of a Team" by Patrick Lencioni](https://www.amazon.com/Five-Dysfunctions-Team-Leadership-Fable/dp/0787960756) - Building trust through automated processes
- ["Fearless Change" by Linda Rising and Mary Lynn Manns](https://www.amazon.com/Fearless-Change-Patterns-Introducing-Ideas/dp/0201755920) - Adopting CI/CD practices

### Videos
- [GitHub Actions Tutorial (TechWorld with Nana)](https://www.youtube.com/watch?v=R8_veQiYBjI) - Complete GitHub Actions course
- [CI/CD with GitHub Actions (freeCodeCamp)](https://www.youtube.com/watch?v=scEDHsr3APg) - Full CI/CD pipeline tutorial
- [GitHub Actions Secrets Management (DevOps Journey)](https://www.youtube.com/watch?v=bTQpANJWJ0g) - Security best practices
- [Advanced GitHub Actions (Coderjourney)](https://www.youtube.com/watch?v=TLB5MY9BBa4) - Professional automation techniques

### Guides
- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [GitHub Actions Marketplace](https://github.com/marketplace?type=actions)
- [Workflow Syntax Reference](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)
- [GitHub Actions Examples](https://github.com/actions/starter-workflows)
- [Awesome GitHub Actions](https://github.com/sdras/awesome-actions)
- [GitHub Learning Lab](https://lab.github.com/)

### Articles
- [Getting Started with GitHub Actions](https://github.blog/2019-08-08-github-actions-now-supports-ci-cd/)
- [GitHub Actions Security Best Practices](https://securitylab.github.com/research/github-actions-preventing-pwn-requests/)
- [Building and Testing with GitHub Actions](https://docs.github.com/en/actions/automating-builds-and-tests)
- [Deploying with GitHub Actions](https://docs.github.com/en/actions/deployment)

### Cultural Assessment Tools
- [CI/CD Maturity Assessment](https://www.devops-culture.com/assessment/)
- [Automation Readiness Evaluation](https://continuousdelivery.com/implementing/patterns/)

### Communities and Events
- [GitHub Community](https://github.community/)
- [GitHub Actions Community](https://github.com/actions/community)
- [DevOps Automation Meetups](https://www.meetup.com/topics/devops-automation/)
- [GitHub Universe Events](https://githubuniverse.com/) 