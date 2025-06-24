# 🔧 API Development with Node.js & Express for DevOps

## 📖 What This File Covers
Build production-ready APIs using Node.js and Express specifically for DevOps workflows. Learn to create APIs that handle deployments, monitor infrastructure, and integrate with CI/CD pipelines.

## 🎯 Learning Objectives
- Build RESTful APIs with Express.js for DevOps operations
- Implement proper error handling and logging for production systems  
- Create APIs that integrate with Docker, Kubernetes, and cloud services
- Handle asynchronous operations and real-time updates
- Structure APIs for scalability and maintainability

## 📋 Prerequisites
- JavaScript and Node.js basics
- Understanding of REST API principles (see `00-API_Fundamentals_For_DevOps.md`)
- Familiarity with Express.js framework
- Basic DevOps concepts (deployments, containerization)

---

## 🚀 **Project Setup: DevOps API Server**

### **🔧 Initial Project Structure**

```bash
# Create project structure
mkdir devops-api
cd devops-api

# Initialize Node.js project
npm init -y

# Install dependencies
npm install express cors helmet morgan dotenv
npm install express-rate-limit express-validator
npm install winston axios uuid

# Install development dependencies  
npm install --save-dev nodemon jest supertest
```

📄 **File Path**: `/devops-api/package.json`
```json
{
  "name": "devops-api",
  "version": "1.0.0",
  "description": "DevOps API for deployment and infrastructure management",
  "main": "server.js",
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js",
    "test": "jest",
    "test:watch": "jest --watch"
  },
  "dependencies": {
    "express": "^4.18.0",
    "cors": "^2.8.5",
    "helmet": "^6.0.0",
    "morgan": "^1.10.0",
    "dotenv": "^16.0.0",
    "express-rate-limit": "^6.7.0",
    "express-validator": "^6.14.0",
    "winston": "^3.8.0",
    "axios": "^1.3.0",
    "uuid": "^9.0.0"
  }
}
```

📄 **File Path**: `/devops-api/.env`
```bash
# Server Configuration
PORT=3000
NODE_ENV=development

# API Configuration
API_VERSION=v1
RATE_LIMIT_WINDOW=900000
RATE_LIMIT_MAX=100

# External Service URLs
DOCKER_API_URL=unix:///var/run/docker.sock
KUBERNETES_API_URL=https://kubernetes.default.svc
AWS_REGION=us-west-2

# Authentication
JWT_SECRET=your-super-secret-key-change-in-production
API_KEY_SALT=your-api-key-salt
```

---

## 🏗️ **Core Server Setup**

📄 **File Path**: `/devops-api/server.js`
```javascript
const express = require('express');
const cors = require('cors');
const helmet = require('helmet');
const morgan = require('morgan');
const rateLimit = require('express-rate-limit');
require('dotenv').config();

// Import routes
const deploymentRoutes = require('./routes/deployments');
const serviceRoutes = require('./routes/services');  
const healthRoutes = require('./routes/health');

// Import middleware
const errorHandler = require('./middleware/errorHandler');
const logger = require('./utils/logger');

const app = express();
const PORT = process.env.PORT || 3000;

// Security middleware
app.use(helmet());
app.use(cors({
    origin: process.env.ALLOWED_ORIGINS?.split(',') || ['http://localhost:3000'],
    credentials: true
}));

// Rate limiting
const limiter = rateLimit({
    windowMs: parseInt(process.env.RATE_LIMIT_WINDOW) || 15 * 60 * 1000,
    max: parseInt(process.env.RATE_LIMIT_MAX) || 100,
    message: {
        error: 'Too many requests',
        message: 'Rate limit exceeded, please try again later.'
    },
    standardHeaders: true,
    legacyHeaders: false
});

app.use(limiter);

// Logging middleware
app.use(morgan('combined', {
    stream: { write: (message) => logger.info(message.trim()) }
}));

// Body parsing middleware
app.use(express.json({ limit: '10mb' }));
app.use(express.urlencoded({ extended: true }));

// API routes
app.use('/api/v1/health', healthRoutes);
app.use('/api/v1/deployments', deploymentRoutes);
app.use('/api/v1/services', serviceRoutes);

// 404 handler
app.use('*', (req, res) => {
    res.status(404).json({
        error: 'endpoint_not_found',
        message: `Endpoint ${req.method} ${req.originalUrl} not found`,
        available_endpoints: [
            'GET /api/v1/health',
            'GET /api/v1/deployments', 
            'POST /api/v1/deployments',
            'GET /api/v1/services'
        ]
    });
});

// Global error handler
app.use(errorHandler);

// Graceful shutdown
process.on('SIGTERM', () => {
    logger.info('SIGTERM received, shutting down gracefully');
    process.exit(0);
});

process.on('SIGINT', () => {
    logger.info('SIGINT received, shutting down gracefully');
    process.exit(0);
});

app.listen(PORT, () => {
    logger.info(`DevOps API server running on port ${PORT}`);
    logger.info(`Environment: ${process.env.NODE_ENV}`);
    logger.info(`API Version: ${process.env.API_VERSION}`);
});

module.exports = app;
```

---

## 📊 **Health Check and Monitoring Endpoints**

📄 **File Path**: `/devops-api/routes/health.js`
```javascript
const express = require('express');
const router = express.Router();
const { exec } = require('child_process');
const { promisify } = require('util');
const execAsync = promisify(exec);

// Basic health check
router.get('/', (req, res) => {
    const healthData = {
        status: 'healthy',
        timestamp: new Date().toISOString(),
        uptime: process.uptime(),
        memory: process.memoryUsage(),
        version: process.env.npm_package_version || '1.0.0'
    };
    
    res.json(healthData);
});

// Detailed system health check
router.get('/detailed', async (req, res) => {
    try {
        const healthChecks = await Promise.allSettled([
            checkDiskSpace(),
            checkDockerStatus(),
            checkNetworkConnectivity(),
            checkDatabaseConnection()
        ]);
        
        const results = {
            status: 'healthy',
            timestamp: new Date().toISOString(),
            checks: {
                disk: healthChecks[0].status === 'fulfilled' ? healthChecks[0].value : { status: 'error', error: healthChecks[0].reason },
                docker: healthChecks[1].status === 'fulfilled' ? healthChecks[1].value : { status: 'error', error: healthChecks[1].reason },
                network: healthChecks[2].status === 'fulfilled' ? healthChecks[2].value : { status: 'error', error: healthChecks[2].reason },
                database: healthChecks[3].status === 'fulfilled' ? healthChecks[3].value : { status: 'error', error: healthChecks[3].reason }
            }
        };
        
        // Determine overall status
        const hasErrors = Object.values(results.checks).some(check => check.status === 'error');
        results.status = hasErrors ? 'degraded' : 'healthy';
        
        res.status(hasErrors ? 503 : 200).json(results);
    } catch (error) {
        res.status(500).json({
            status: 'error',
            message: 'Health check failed',
            error: error.message
        });
    }
});

// Helper functions for health checks
async function checkDiskSpace() {
    try {
        const { stdout } = await execAsync("df -h / | tail -1 | awk '{print $5}' | sed 's/%//'");
        const usagePercent = parseInt(stdout.trim());
        
        return {
            status: usagePercent > 90 ? 'warning' : 'ok',
            usage_percent: usagePercent,
            message: usagePercent > 90 ? 'Disk usage high' : 'Disk usage normal'
        };
    } catch (error) {
        throw new Error(`Disk check failed: ${error.message}`);
    }
}

async function checkDockerStatus() {
    try {
        await execAsync('docker info');
        return {
            status: 'ok',
            message: 'Docker daemon is running'
        };
    } catch (error) {
        return {
            status: 'warning',
            message: 'Docker daemon not accessible'
        };
    }
}

async function checkNetworkConnectivity() {
    try {
        const axios = require('axios');
        await axios.get('https://api.github.com', { timeout: 5000 });
        return {
            status: 'ok',
            message: 'External network connectivity OK'
        };
    } catch (error) {
        return {
            status: 'warning',
            message: 'External network connectivity issues'
        };
    }
}

async function checkDatabaseConnection() {
    // Placeholder for database connection check
    // In real implementation, test your actual database
    return {
        status: 'ok',
        message: 'Database connection OK'
    };
}

module.exports = router;
```

---

## 🚀 **Deployment Management API**

📄 **File Path**: `/devops-api/routes/deployments.js`
```javascript
const express = require('express');
const router = express.Router();
const { body, validationResult, param, query } = require('express-validator');
const { v4: uuidv4 } = require('uuid');
const logger = require('../utils/logger');

// In-memory storage (use database in production)
let deployments = [];

// Input validation schemas
const createDeploymentValidation = [
    body('service_name')
        .isLength({ min: 3, max: 50 })
        .matches(/^[a-zA-Z0-9-_]+$/)
        .withMessage('Service name must be 3-50 characters, alphanumeric with hyphens/underscores'),
    body('version')
        .matches(/^v?\d+\.\d+\.\d+(-[\w\d]+)?$/)
        .withMessage('Version must follow semantic versioning (e.g., v1.2.3)'),
    body('environment')
        .isIn(['development', 'staging', 'production'])
        .withMessage('Environment must be development, staging, or production'),
    body('strategy')
        .optional()
        .isIn(['rolling', 'blue-green', 'canary'])
        .withMessage('Strategy must be rolling, blue-green, or canary'),
    body('replicas')
        .optional()
        .isInt({ min: 1, max: 100 })
        .withMessage('Replicas must be between 1 and 100')
];

// GET /deployments - List all deployments with filtering
router.get('/', [
    query('environment').optional().isIn(['development', 'staging', 'production']),
    query('status').optional().isIn(['pending', 'running', 'successful', 'failed']),
    query('limit').optional().isInt({ min: 1, max: 100 }),
    query('offset').optional().isInt({ min: 0 })
], (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
        return res.status(400).json({
            error: 'validation_error',
            message: 'Invalid query parameters',
            details: errors.array()
        });
    }
    
    let filteredDeployments = [...deployments];
    
    // Apply filters
    if (req.query.environment) {
        filteredDeployments = filteredDeployments.filter(d => d.environment === req.query.environment);
    }
    
    if (req.query.status) {
        filteredDeployments = filteredDeployments.filter(d => d.status === req.query.status);
    }
    
    // Pagination
    const limit = parseInt(req.query.limit) || 50;
    const offset = parseInt(req.query.offset) || 0;
    const paginatedDeployments = filteredDeployments.slice(offset, offset + limit);
    
    res.json({
        deployments: paginatedDeployments,
        pagination: {
            total: filteredDeployments.length,
            limit,
            offset,
            has_more: offset + limit < filteredDeployments.length
        }
    });
});

// GET /deployments/:id - Get specific deployment
router.get('/:id', [
    param('id').isUUID().withMessage('Invalid deployment ID format')
], (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
        return res.status(400).json({
            error: 'validation_error',
            details: errors.array()
        });
    }
    
    const deployment = deployments.find(d => d.id === req.params.id);
    
    if (!deployment) {
        return res.status(404).json({
            error: 'deployment_not_found',
            message: `Deployment ${req.params.id} not found`
        });
    }
    
    res.json({ deployment });
});

// POST /deployments - Create new deployment
router.post('/', createDeploymentValidation, async (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
        return res.status(400).json({
            error: 'validation_error',
            message: 'Invalid deployment data',
            details: errors.array()
        });
    }
    
    try {
        const deployment = {
            id: uuidv4(),
            service_name: req.body.service_name,
            version: req.body.version,
            environment: req.body.environment,
            strategy: req.body.strategy || 'rolling',
            replicas: req.body.replicas || 1,
            status: 'pending',
            created_at: new Date().toISOString(),
            updated_at: new Date().toISOString(),
            created_by: req.user?.username || 'api-user',
            logs: []
        };
        
        deployments.push(deployment);
        
        // Log deployment creation
        logger.info('Deployment created', {
            deployment_id: deployment.id,
            service: deployment.service_name,
            version: deployment.version,
            environment: deployment.environment
        });
        
        // Simulate deployment process (async)
        processDeployment(deployment.id);
        
        res.status(201).json({ deployment });
    } catch (error) {
        logger.error('Deployment creation failed', { error: error.message });
        res.status(500).json({
            error: 'deployment_failed',
            message: 'Failed to create deployment'
        });
    }
});

// POST /deployments/:id/rollback - Rollback deployment
router.post('/:id/rollback', [
    param('id').isUUID().withMessage('Invalid deployment ID'),
    body('reason').optional().isLength({ max: 200 })
], async (req, res) => {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
        return res.status(400).json({
            error: 'validation_error',
            details: errors.array()
        });
    }
    
    const deployment = deployments.find(d => d.id === req.params.id);
    
    if (!deployment) {
        return res.status(404).json({
            error: 'deployment_not_found',
            message: `Deployment ${req.params.id} not found`
        });
    }
    
    if (deployment.status !== 'successful') {
        return res.status(409).json({
            error: 'invalid_deployment_status',
            message: 'Can only rollback successful deployments'
        });
    }
    
    try {
        const rollbackDeployment = {
            id: uuidv4(),
            service_name: deployment.service_name,
            version: 'rollback-' + deployment.version,
            environment: deployment.environment,
            strategy: 'rolling',
            replicas: deployment.replicas,
            status: 'pending',
            created_at: new Date().toISOString(),
            updated_at: new Date().toISOString(),
            created_by: req.user?.username || 'api-user',
            rollback_from: deployment.id,
            rollback_reason: req.body.reason || 'Manual rollback',
            logs: []
        };
        
        deployments.push(rollbackDeployment);
        
        logger.info('Rollback initiated', {
            rollback_id: rollbackDeployment.id,
            original_deployment: deployment.id,
            reason: rollbackDeployment.rollback_reason
        });
        
        processDeployment(rollbackDeployment.id);
        
        res.status(202).json({ 
            rollback: rollbackDeployment,
            message: 'Rollback initiated successfully'
        });
    } catch (error) {
        logger.error('Rollback failed', { error: error.message });
        res.status(500).json({
            error: 'rollback_failed',
            message: 'Failed to initiate rollback'
        });
    }
});

// Simulate deployment process
async function processDeployment(deploymentId) {
    const deployment = deployments.find(d => d.id === deploymentId);
    if (!deployment) return;
    
    try {
        // Update status to running
        deployment.status = 'running';
        deployment.updated_at = new Date().toISOString();
        deployment.logs.push({
            timestamp: new Date().toISOString(),
            level: 'info',
            message: 'Deployment started'
        });
        
        // Simulate deployment steps
        await new Promise(resolve => setTimeout(resolve, 2000));
        
        deployment.logs.push({
            timestamp: new Date().toISOString(),
            level: 'info',
            message: 'Pulling container image'
        });
        
        await new Promise(resolve => setTimeout(resolve, 3000));
        
        deployment.logs.push({
            timestamp: new Date().toISOString(),
            level: 'info',
            message: 'Starting new containers'
        });
        
        await new Promise(resolve => setTimeout(resolve, 2000));
        
        deployment.logs.push({
            timestamp: new Date().toISOString(),
            level: 'info',
            message: 'Health checks passing'
        });
        
        // Randomly succeed or fail (90% success rate)
        const success = Math.random() > 0.1;
        
        if (success) {
            deployment.status = 'successful';
            deployment.logs.push({
                timestamp: new Date().toISOString(),
                level: 'info',
                message: 'Deployment completed successfully'
            });
        } else {
            deployment.status = 'failed';
            deployment.logs.push({
                timestamp: new Date().toISOString(),
                level: 'error',
                message: 'Deployment failed: Health checks failed'
            });
        }
        
        deployment.updated_at = new Date().toISOString();
        
        logger.info('Deployment process completed', {
            deployment_id: deploymentId,
            status: deployment.status
        });
        
    } catch (error) {
        deployment.status = 'failed';
        deployment.logs.push({
            timestamp: new Date().toISOString(),
            level: 'error',
            message: `Deployment error: ${error.message}`
        });
        
        logger.error('Deployment process failed', {
            deployment_id: deploymentId,
            error: error.message
        });
    }
}

module.exports = router;
```

---

## 🛠️ **Utility Functions and Middleware**

📄 **File Path**: `/devops-api/middleware/errorHandler.js`
```javascript
const logger = require('../utils/logger');

function errorHandler(err, req, res, next) {
    // Log error details
    logger.error('API Error', {
        error: err.message,
        stack: err.stack,
        url: req.url,
        method: req.method,
        ip: req.ip,
        userAgent: req.get('User-Agent')
    });
    
    // Handle different error types
    if (err.name === 'ValidationError') {
        return res.status(400).json({
            error: 'validation_error',
            message: 'Request validation failed',
            details: err.details
        });
    }
    
    if (err.name === 'UnauthorizedError') {
        return res.status(401).json({
            error: 'unauthorized',
            message: 'Authentication required'
        });
    }
    
    if (err.code === 'ECONNREFUSED') {
        return res.status(503).json({
            error: 'service_unavailable',
            message: 'External service unavailable'
        });
    }
    
    // Default error response
    const isDevelopment = process.env.NODE_ENV === 'development';
    
    res.status(err.status || 500).json({
        error: 'internal_server_error',
        message: isDevelopment ? err.message : 'An internal error occurred',
        ...(isDevelopment && { stack: err.stack })
    });
}

module.exports = errorHandler;
```

📄 **File Path**: `/devops-api/utils/logger.js`
```javascript
const winston = require('winston');

const logger = winston.createLogger({
    level: process.env.LOG_LEVEL || 'info',
    format: winston.format.combine(
        winston.format.timestamp(),
        winston.format.errors({ stack: true }),
        winston.format.json()
    ),
    defaultMeta: {
        service: 'devops-api',
        version: process.env.npm_package_version || '1.0.0'
    },
    transports: [
        new winston.transports.File({ 
            filename: 'logs/error.log', 
            level: 'error' 
        }),
        new winston.transports.File({ 
            filename: 'logs/combined.log' 
        })
    ]
});

// Add console logging in development
if (process.env.NODE_ENV !== 'production') {
    logger.add(new winston.transports.Console({
        format: winston.format.combine(
            winston.format.colorize(),
            winston.format.simple()
        )
    }));
}

module.exports = logger;
```

---

## 🧪 **Testing Your DevOps API**

📄 **File Path**: `/devops-api/tests/deployments.test.js`
```javascript
const request = require('supertest');
const app = require('../server');

describe('Deployment API', () => {
    describe('GET /api/v1/deployments', () => {
        it('should return empty deployments list', async () => {
            const response = await request(app)
                .get('/api/v1/deployments')
                .expect(200);
            
            expect(response.body.deployments).toEqual([]);
            expect(response.body.pagination.total).toBe(0);
        });
    });
    
    describe('POST /api/v1/deployments', () => {
        it('should create a new deployment', async () => {
            const deploymentData = {
                service_name: 'test-api',
                version: 'v1.0.0',
                environment: 'staging',
                replicas: 2
            };
            
            const response = await request(app)
                .post('/api/v1/deployments')
                .send(deploymentData)
                .expect(201);
            
            expect(response.body.deployment).toMatchObject(deploymentData);
            expect(response.body.deployment.id).toBeDefined();
            expect(response.body.deployment.status).toBe('pending');
        });
        
        it('should validate deployment data', async () => {
            const invalidData = {
                service_name: 'a', // Too short
                version: 'invalid-version',
                environment: 'invalid-env'
            };
            
            const response = await request(app)
                .post('/api/v1/deployments')
                .send(invalidData)
                .expect(400);
            
            expect(response.body.error).toBe('validation_error');
            expect(response.body.details).toHaveLength(3);
        });
    });
});
```

---

## 🚀 **Running and Testing Your API**

### **🔧 Development Commands**

```bash
# Install dependencies
npm install

# Start development server with auto-reload
npm run dev

# Run tests
npm test

# Run tests in watch mode
npm run test:watch

# Start production server
npm start
```

### **🧪 Manual Testing with curl**

```bash
# Test health check
curl http://localhost:3000/api/v1/health

# Create a deployment
curl -X POST http://localhost:3000/api/v1/deployments \
  -H "Content-Type: application/json" \
  -d '{
    "service_name": "my-api",
    "version": "v1.2.3",
    "environment": "staging",
    "replicas": 3
  }'

# List deployments
curl http://localhost:3000/api/v1/deployments

# Get specific deployment
curl http://localhost:3000/api/v1/deployments/{deployment-id}

# Rollback deployment
curl -X POST http://localhost:3000/api/v1/deployments/{deployment-id}/rollback \
  -H "Content-Type: application/json" \
  -d '{"reason": "Critical bug found"}'
```

---

## 🚀 **Next Steps: Production Deployment**

### **🎯 Immediate Actions:**
1. **Set up the project** and run locally
2. **Test all endpoints** with curl or Postman
3. **Write additional tests** for edge cases
4. **Add authentication** (see `02-API_Authentication_Security.md`)

### **🔧 Production Enhancements:**
- **Database integration** (PostgreSQL, MongoDB)
- **Container deployment** with Docker
- **CI/CD pipeline** integration
- **Monitoring and alerting** setup

### **📚 Related Files:**
- **Next**: `04-API_Testing_Validation.md` - Comprehensive testing strategies
- **Security**: `02-API_Authentication_Security.md` - Add authentication
- **Deployment**: `06-APIs_In_CI_CD_Pipelines.md` - Automate deployment

---

**🎯 You've Built a Production-Ready Foundation**: This API structure handles real DevOps scenarios with proper validation, logging, error handling, and testing. It's ready to be extended with authentication, database integration, and deployment to production environments. 