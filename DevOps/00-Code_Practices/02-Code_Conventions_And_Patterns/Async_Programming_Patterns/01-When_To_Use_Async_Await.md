# When to Use Async/Await in DevOps

## 📖 What This File Covers
Master async/await patterns for DevOps automation. Learn when asynchronous programming improves performance and reliability in deployment scripts, infrastructure management, and monitoring systems.

## 🎯 Learning Objectives
- Understand when async programming benefits DevOps workflows
- Implement async patterns for parallel infrastructure operations
- Handle concurrent API calls and external service interactions
- Avoid common async/await pitfalls in automation scripts
- Choose between sync and async approaches based on use case

---

## ⚡ **When Async Programming Helps DevOps**

### **Scenario 1: Multi-Region Deployments**
```javascript
// ❌ Slow: Sequential deployment (90 seconds total)
async function deploySequential() {
    console.log('🚀 Starting sequential deployment...');
    
    const startTime = Date.now();
    
    // Deploy to each region one by one
    await deployToRegion('us-east-1');      // 30 seconds
    await deployToRegion('us-west-2');      // 30 seconds
    await deployToRegion('eu-west-1');      // 30 seconds
    
    const totalTime = (Date.now() - startTime) / 1000;
    console.log(`⏰ Sequential deployment took ${totalTime}s`);
}

// ✅ Fast: Parallel deployment (30 seconds total)
async function deployParallel() {
    console.log('🚀 Starting parallel deployment...');
    
    const startTime = Date.now();
    
    // Deploy to all regions simultaneously
    const deploymentPromises = [
        deployToRegion('us-east-1'),
        deployToRegion('us-west-2'),
        deployToRegion('eu-west-1')
    ];
    
    try {
        const results = await Promise.all(deploymentPromises);
        
        const totalTime = (Date.now() - startTime) / 1000;
        console.log(`⚡ Parallel deployment completed in ${totalTime}s`);
        
        return results;
        
    } catch (error) {
        console.error(`💥 Deployment failed: ${error.message}`);
        throw error;
    }
}

async function deployToRegion(region) {
    console.log(`📍 Deploying to ${region}...`);
    
    // Simulate deployment time
    await new Promise(resolve => setTimeout(resolve, 30000));
    
    return {
        success: true,
        region: region,
        deploymentId: `deploy-${region}-${Date.now()}`
    };
}
```

### **Scenario 2: Health Check Monitoring**
```javascript
// ✅ Async health checking for multiple services
async function checkSystemHealth() {
    console.log('🔍 Running system health checks...');
    
    const services = [
        { name: 'API Gateway', url: 'https://api.company.com/health' },
        { name: 'Database', url: 'https://db.company.com/health' },
        { name: 'Redis Cache', url: 'https://cache.company.com/health' }
    ];
    
    const healthCheckPromises = services.map(service => 
        checkServiceHealth(service)
    );
    
    try {
        // Run all health checks in parallel
        const results = await Promise.allSettled(healthCheckPromises);
        
        const healthReport = {
            timestamp: new Date().toISOString(),
            services: [],
            overallStatus: 'healthy'
        };
        
        results.forEach((result, index) => {
            const service = services[index];
            
            if (result.status === 'fulfilled') {
                healthReport.services.push({
                    name: service.name,
                    status: result.value.healthy ? 'healthy' : 'unhealthy',
                    responseTime: result.value.responseTime
                });
            } else {
                healthReport.services.push({
                    name: service.name,
                    status: 'error',
                    error: result.reason.message
                });
                healthReport.overallStatus = 'degraded';
            }
        });
        
        return healthReport;
        
    } catch (error) {
        console.error('💥 Health check system failed:', error);
        throw error;
    }
}

async function checkServiceHealth(service) {
    const startTime = Date.now();
    
    try {
        const response = await fetch(service.url, {
            method: 'GET',
            timeout: 10000
        });
        
        const responseTime = Date.now() - startTime;
        
        if (response.ok) {
            const healthData = await response.json();
            
            return {
                healthy: healthData.status === 'healthy',
                responseTime: responseTime
            };
        } else {
            return {
                healthy: false,
                responseTime: responseTime
            };
        }
        
    } catch (error) {
        throw new Error(`${service.name} health check failed: ${error.message}`);
    }
}
```

---

## 🎯 **When NOT to Use Async**

### **Sequential Operations That Depend on Each Other**
```javascript
// ❌ Don't use async for sequential dependent operations
async function badAsyncUsage() {
    // These operations must happen in order
    const buildResult = await buildApplication();
    const testResult = await runTests(buildResult);
    const packageResult = await packageApplication(testResult);
    const deployResult = await deployToProduction(packageResult);
    
    return deployResult;
}

// ✅ Better: Use regular sequential code
function goodSequentialApproach() {
    console.log('📦 Building application...');
    const buildResult = buildApplicationSync();
    
    if (!buildResult.success) {
        throw new Error(`Build failed: ${buildResult.error}`);
    }
    
    console.log('🧪 Running tests...');
    const testResult = runTestsSync(buildResult);
    
    return testResult;
}
```

---

## 📊 **Decision Matrix: Async vs Sync**

| Operation Type | Use Async? | Reason |
|---------------|------------|---------|
| **Multiple API calls** | ✅ Yes | Can run in parallel |
| **File uploads to different servers** | ✅ Yes | I/O operations can be parallel |
| **Health checks across services** | ✅ Yes | Independent checks |
| **Multi-region deployments** | ✅ Yes | Regions are independent |
| **Sequential build steps** | ❌ No | Each step depends on previous |
| **Data processing/calculations** | ❌ No | No I/O, just CPU work |
| **Configuration validation** | ❌ No | Simple logic, no external calls |

---

## 🛠️ **Best Practices for DevOps Async Code**

### **1. Timeout and Error Handling**
```javascript
async function robustAsyncOperation() {
    const TIMEOUT = 30000; // 30 seconds
    
    try {
        const result = await Promise.race([
            performOperation(),
            new Promise((_, reject) => 
                setTimeout(() => reject(new Error('Operation timeout')), TIMEOUT)
            )
        ]);
        
        return result;
        
    } catch (error) {
        console.error(`Operation failed: ${error.message}`);
        
        // Implement retry logic for transient failures
        if (error.message.includes('network') || error.message.includes('timeout')) {
            console.log('🔄 Retrying operation...');
            await new Promise(resolve => setTimeout(resolve, 2000));
            return robustAsyncOperation();
        }
        
        throw error;
    }
}
```

### **2. Concurrency Control**
```javascript
async function deployWithConcurrencyLimit(servers) {
    const BATCH_SIZE = 3; // Deploy to max 3 servers at once
    
    const results = [];
    
    for (let i = 0; i < servers.length; i += BATCH_SIZE) {
        const batch = servers.slice(i, i + BATCH_SIZE);
        
        console.log(`🚀 Deploying batch ${Math.floor(i / BATCH_SIZE) + 1}`);
        
        const batchResults = await Promise.all(
            batch.map(server => deployToServer(server))
        );
        
        results.push(...batchResults);
        
        // Brief pause between batches
        if (i + BATCH_SIZE < servers.length) {
            await new Promise(resolve => setTimeout(resolve, 1000));
        }
    }
    
    return results;
}
```

---

**💡 Key Takeaway**: Use async/await when you have **independent operations** that involve **I/O or external services**. Keep it simple and sequential when operations **depend on each other**!
