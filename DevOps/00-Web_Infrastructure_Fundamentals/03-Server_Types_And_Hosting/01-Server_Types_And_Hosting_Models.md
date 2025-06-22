# Server Types and Hosting Models

## 📖 What This File Covers
Master different server types, hosting models, and infrastructure patterns. Learn when to use physical servers, virtual machines, containers, and serverless computing for various applications and workloads.

## 🎯 Learning Objectives
- Understand different server types and their use cases
- Compare hosting models (on-premise, cloud, hybrid)
- Learn container orchestration and serverless patterns
- Design cost-effective infrastructure solutions
- Implement monitoring and management strategies

---

## 🖥️ **Server Types and Architectures**

### **Physical vs Virtual vs Container vs Serverless**
```
PHYSICAL SERVERS (Bare Metal)
├── Dedicated hardware resources
├── Maximum performance
├── Full control over hardware
├── Higher costs and maintenance
└── Use cases: High-performance computing, databases

VIRTUAL MACHINES (VMs)
├── Shared hardware resources
├── Resource isolation
├── OS-level virtualization
├── Moderate overhead
└── Use cases: Traditional applications, legacy systems

CONTAINERS
├── Shared OS kernel
├── Application-level isolation
├── Lightweight and fast startup
├── Resource efficient
└── Use cases: Microservices, CI/CD, scalable apps

SERVERLESS (Functions)
├── Event-driven execution
├── No infrastructure management
├── Pay-per-execution model
├── Auto-scaling
└── Use cases: APIs, data processing, automation
```

### **Real-World Server Configurations**

#### **Web Server (NGINX)**
```
Server: nginx-web-01.company.com
IP: 203.0.113.10
Specs: 8GB RAM, 4 cores, 100GB SSD
OS: Ubuntu 22.04 LTS

Configuration:
server {
    listen 80;
    listen 443 ssl http2;
    server_name api.company.com;
    
    location / {
        proxy_pass http://app_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}

upstream app_backend {
    least_conn;
    server 10.0.20.10:8080;
    server 10.0.20.11:8080;
    server 10.0.20.12:8080;
}
```

#### **Application Server (Node.js)**
```
Server: app-01.company.com
IP: 10.0.20.10
Specs: 16GB RAM, 8 cores, 200GB SSD

PM2 Configuration:
module.exports = {
  apps: [{
    name: 'company-api',
    script: './dist/server.js',
    instances: 'max',
    exec_mode: 'cluster',
    env: {
      NODE_ENV: 'production',
      PORT: 8080,
      DB_HOST: '10.0.30.10',
      REDIS_HOST: '10.0.30.20'
    }
  }]
};
```

#### **Database Server (PostgreSQL)**
```
Server: db-primary.company.com
IP: 10.0.30.10
Specs: 32GB RAM, 16 cores, 1TB NVMe SSD

Configuration:
max_connections = 200
shared_buffers = 8GB
effective_cache_size = 24GB
maintenance_work_mem = 2GB
wal_level = replica
max_wal_senders = 3
```

---

## ☁️ **Cloud Hosting Models**

### **AWS Instance Types**
```
WEB TIER - Compute Optimized
├── Instance: c5.2xlarge
├── vCPUs: 8, RAM: 16GB
├── Use Case: Web servers, load balancers
└── Cost: ~$0.34/hour

APPLICATION TIER - General Purpose
├── Instance: m5.4xlarge
├── vCPUs: 16, RAM: 64GB
├── Use Case: Application servers
└── Cost: ~$0.77/hour

DATABASE TIER - Memory Optimized
├── Instance: r5.8xlarge
├── vCPUs: 32, RAM: 256GB
├── Use Case: Databases, caching
└── Cost: ~$2.02/hour
```

### **Multi-Cloud Strategy**
```
AWS (Primary - US East)
├── Production API: api.company.com
├── Instance: ALB + 6x c5.2xlarge
├── Database: RDS PostgreSQL (Multi-AZ)
└── Cost: $2,500/month

Google Cloud (Secondary - US West)
├── Disaster Recovery: dr.company.com
├── Instance: 4x n2-standard-8
├── Database: Cloud SQL
└── Cost: $1,200/month

Azure (Development/Staging)
├── Staging: staging.company.com
├── Instance: 2x Standard_D4s_v3
└── Cost: $800/month
```

---

## 🐳 **Container Orchestration**

### **Kubernetes Cluster**
```
CONTROL PLANE (3 nodes)
├── k8s-master-01: 10.0.40.10 (us-east-1a)
├── k8s-master-02: 10.0.40.11 (us-east-1b)
└── k8s-master-03: 10.0.40.12 (us-east-1c)

WORKER NODES (Auto Scaling)
├── Min: 3 nodes, Max: 20 nodes
├── Instance: m5.xlarge (4 vCPU, 16GB)
└── Node Groups: General, Memory, CPU intensive
```

### **Docker Deployment**
```dockerfile
FROM node:18-alpine

WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

COPY . .
EXPOSE 8080

HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl -f http://localhost:8080/health || exit 1

USER nodejs
CMD ["node", "server.js"]
```

```yaml
# Kubernetes Deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: company-api
spec:
  replicas: 6
  selector:
    matchLabels:
      app: company-api
  template:
    spec:
      containers:
      - name: api
        image: company/api:v1.2.3
        ports:
        - containerPort: 8080
        resources:
          requests:
            memory: "512Mi"
            cpu: "250m"
          limits:
            memory: "1Gi"
            cpu: "500m"
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
```

---

## ⚡ **Serverless Computing**

### **AWS Lambda Function**
```javascript
// API Gateway Lambda Handler
exports.handler = async (event) => {
    const { httpMethod, pathParameters, body } = event;
    
    try {
        let response;
        switch (httpMethod) {
            case 'GET':
                response = await getItem(pathParameters?.id);
                break;
            case 'POST':
                response = await createItem(JSON.parse(body));
                break;
            default:
                throw new Error(`Unsupported method: ${httpMethod}`);
        }
        
        return {
            statusCode: 200,
            headers: {
                'Content-Type': 'application/json',
                'Access-Control-Allow-Origin': '*'
            },
            body: JSON.stringify(response)
        };
    } catch (error) {
        return {
            statusCode: 500,
            body: JSON.stringify({ error: error.message })
        };
    }
};
```

### **Serverless Framework**
```yaml
# serverless.yml
service: company-api
provider:
  name: aws
  runtime: nodejs18.x
  region: us-east-1

functions:
  api:
    handler: handler.handler
    events:
      - http:
          path: /items
          method: get
      - http:
          path: /items
          method: post
    reservedConcurrency: 100
    timeout: 30

resources:
  Resources:
    ItemsTable:
      Type: AWS::DynamoDB::Table
      Properties:
        BillingMode: PAY_PER_REQUEST
        AttributeDefinitions:
          - AttributeName: id
            AttributeType: S
        KeySchema:
          - AttributeName: id
            KeyType: HASH
```

---

## 💰 **Cost Optimization**

### **Pricing Model Comparison**
```python
class AWSCostCalculator:
    def calculate_monthly_costs(self, instance_type):
        pricing_data = {
            't3.medium': {
                'on_demand': 0.0416, 
                'reserved_1yr': 0.025, 
                'spot_avg': 0.012
            },
            'm5.large': {
                'on_demand': 0.096, 
                'reserved_1yr': 0.058, 
                'spot_avg': 0.029
            }
        }
        
        prices = pricing_data[instance_type]
        hours_per_month = 730
        
        return {
            'on_demand': prices['on_demand'] * hours_per_month,
            'reserved_1yr': prices['reserved_1yr'] * hours_per_month,
            'spot_average': prices['spot_avg'] * hours_per_month
        }

# Example cost comparison:
# t3.medium monthly costs:
# On-Demand: $30.37/month
# Reserved 1-year: $18.25/month (40% savings)
# Spot average: $8.76/month (71% savings)
```

### **Cost Optimization Recommendations**
```
STEADY WORKLOADS (24/7)
├── Use Reserved Instances (1-3 year terms)
├── Savings: 30-60% vs On-Demand
└── Best for: Production databases, web servers

VARIABLE WORKLOADS
├── Use Spot Instances with Auto Scaling
├── Savings: 50-90% vs On-Demand
└── Best for: Batch processing, development

DEVELOPMENT ENVIRONMENTS
├── Use Spot + Scheduled scaling
├── Run only during work hours
└── Savings: 80-90% vs 24/7 On-Demand

SERVERLESS WORKLOADS
├── Use Lambda for event-driven tasks
├── Pay only for execution time
└── Best for: APIs, data processing
```

---

**💡 Key Takeaway**: Choose the right server type and hosting model based on your workload requirements, cost constraints, and operational capabilities!
