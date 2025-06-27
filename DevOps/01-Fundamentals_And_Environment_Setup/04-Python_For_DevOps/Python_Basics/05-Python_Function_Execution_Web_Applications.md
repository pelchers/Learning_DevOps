# 🌐 Python Function Execution in Web Applications

## 📖 What This File Does
Comprehensive guide to executing Python functions in web application contexts. Covers synchronous and asynchronous execution, web frameworks, API development, and request/response handling patterns.

## 🎯 Learning Objectives
- Master function execution in web frameworks (Flask, FastAPI)
- Understand synchronous vs asynchronous function execution
- Learn request/response handling patterns
- Implement error handling for web applications
- Master API endpoint function design

## 📋 Prerequisites
- Understanding of Python functions and basic web concepts
- Familiarity with HTTP methods and status codes
- Basic knowledge of JSON and REST APIs

---

## 🚀 **Flask Web Application Function Execution**

### **Basic Flask Function Execution**
```python
from flask import Flask, request, jsonify
from datetime import datetime
import logging

app = Flask(__name__)

# Configure logging
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

def process_user_data(user_data: dict) -> dict:
    """Process user data - business logic function"""
    processed_data = {
        'user_id': user_data.get('id'),
        'username': user_data.get('username', '').lower().strip(),
        'email': user_data.get('email', '').lower().strip(),
        'created_at': datetime.now().isoformat(),
        'is_active': True
    }
    
    # Validation logic
    if not processed_data['username']:
        raise ValueError("Username is required")
    
    if '@' not in processed_data['email']:
        raise ValueError("Invalid email format")
    
    return processed_data

@app.route('/users', methods=['POST'])
def create_user():
    """Web endpoint that executes business logic function"""
    try:
        # Get data from request
        user_data = request.get_json()
        
        # Log the request
        logger.info(f"Creating user with data: {user_data}")
        
        # Execute business logic function
        processed_user = process_user_data(user_data)
        
        # Save to database (simulated)
        saved_user = save_user_to_database(processed_user)
        
        # Log success
        logger.info(f"User created successfully: {saved_user['user_id']}")
        
        # Return response
        return jsonify({
            'status': 'success',
            'message': 'User created successfully',
            'data': saved_user
        }), 201
        
    except ValueError as e:
        # Handle validation errors
        logger.warning(f"Validation error: {str(e)}")
        return jsonify({
            'status': 'error',
            'message': f'Validation error: {str(e)}'
        }), 400
        
    except Exception as e:
        # Handle unexpected errors
        logger.error(f"Unexpected error creating user: {str(e)}")
        return jsonify({
            'status': 'error',
            'message': 'Internal server error'
        }), 500

def save_user_to_database(user_data: dict) -> dict:
    """Database operation function"""
    # Simulate database save
    user_data['id'] = f"user_{datetime.now().timestamp()}"
    return user_data

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0', port=5000)
```

**📖 What This Does**  
Flask web functions execute in response to HTTP requests. The framework handles request routing, data parsing, and response formatting while your functions handle the business logic.

**🔧 Web Application Patterns**
- **Route Functions**: Handle HTTP requests and responses
- **Business Logic Functions**: Process data and implement core functionality
- **Database Functions**: Handle data persistence
- **Utility Functions**: Support operations like logging and validation

**💡 Example Explanation**
```python
# Request Flow:
# 1. HTTP POST request to /users
# 2. Flask routes to create_user() function
# 3. create_user() extracts JSON data from request
# 4. create_user() calls process_user_data() for business logic
# 5. create_user() calls save_user_to_database() for persistence
# 6. create_user() returns JSON response with appropriate HTTP status
```

### **Advanced Flask Function Patterns**
```python
from flask import Flask, request, jsonify, g
from functools import wraps
import time
import jwt
from typing import Callable, Any

app = Flask(__name__)
app.config['SECRET_KEY'] = 'your-secret-key'

# Decorator for function execution timing
def measure_execution_time(func: Callable) -> Callable:
    """Decorator to measure function execution time"""
    @wraps(func)
    def wrapper(*args, **kwargs):
        start_time = time.time()
        result = func(*args, **kwargs)
        execution_time = time.time() - start_time
        
        logger.info(f"Function {func.__name__} executed in {execution_time:.3f} seconds")
        return result
    return wrapper

# Decorator for authentication
def require_auth(func: Callable) -> Callable:
    """Decorator to require authentication for function execution"""
    @wraps(func)
    def wrapper(*args, **kwargs):
        token = request.headers.get('Authorization')
        
        if not token:
            return jsonify({'error': 'No token provided'}), 401
        
        try:
            # Remove 'Bearer ' prefix
            token = token.replace('Bearer ', '')
            payload = jwt.decode(token, app.config['SECRET_KEY'], algorithms=['HS256'])
            g.current_user = payload['user_id']
        except jwt.InvalidTokenError:
            return jsonify({'error': 'Invalid token'}), 401
        
        return func(*args, **kwargs)
    return wrapper

@measure_execution_time
def complex_data_processing(data: list) -> dict:
    """Complex business logic function with timing"""
    # Simulate heavy processing
    processed_items = []
    
    for item in data:
        # Complex processing logic
        processed_item = {
            'id': item.get('id'),
            'processed_at': datetime.now().isoformat(),
            'hash': hash(str(item)),
            'validation_score': calculate_validation_score(item)
        }
        processed_items.append(processed_item)
        time.sleep(0.01)  # Simulate processing time
    
    return {
        'total_processed': len(processed_items),
        'items': processed_items,
        'processing_summary': generate_processing_summary(processed_items)
    }

def calculate_validation_score(item: dict) -> float:
    """Calculate validation score for an item"""
    score = 0.0
    
    # Check required fields
    required_fields = ['id', 'name', 'type']
    for field in required_fields:
        if field in item and item[field]:
            score += 0.3
    
    # Check data quality
    if item.get('name', '').strip():
        score += 0.1
    
    return min(score, 1.0)  # Cap at 1.0

def generate_processing_summary(items: list) -> dict:
    """Generate summary of processed items"""
    total_items = len(items)
    valid_items = sum(1 for item in items if item['validation_score'] >= 0.7)
    
    return {
        'total_items': total_items,
        'valid_items': valid_items,
        'valid_percentage': (valid_items / total_items * 100) if total_items > 0 else 0,
        'average_score': sum(item['validation_score'] for item in items) / total_items if total_items > 0 else 0
    }

@app.route('/process-data', methods=['POST'])
@require_auth
@measure_execution_time
def process_data_endpoint():
    """Endpoint with authentication and timing decorators"""
    try:
        data = request.get_json()
        
        if not data or 'items' not in data:
            return jsonify({'error': 'No items provided'}), 400
        
        # Execute business logic function
        result = complex_data_processing(data['items'])
        
        # Log processing for current user
        logger.info(f"User {g.current_user} processed {result['total_processed']} items")
        
        return jsonify({
            'status': 'success',
            'user_id': g.current_user,
            'result': result
        }), 200
        
    except Exception as e:
        logger.error(f"Error processing data for user {g.current_user}: {str(e)}")
        return jsonify({'error': 'Processing failed'}), 500

# Error handler function
@app.errorhandler(404)
def not_found_handler(error):
    """Function executed for 404 errors"""
    return jsonify({
        'status': 'error',
        'message': 'Endpoint not found',
        'available_endpoints': [
            'POST /users',
            'POST /process-data'
        ]
    }), 404

@app.errorhandler(500)
def internal_error_handler(error):
    """Function executed for 500 errors"""
    logger.error(f"Internal server error: {str(error)}")
    return jsonify({
        'status': 'error',
        'message': 'Internal server error'
    }), 500
```

**📖 What This Does**  
Advanced Flask patterns use decorators to enhance function execution with cross-cutting concerns like authentication, timing, and error handling. Functions are composed to create robust web applications.

---

## ⚡ **FastAPI Asynchronous Function Execution**

### **Basic FastAPI Async Functions**
```python
from fastapi import FastAPI, HTTPException, Depends, BackgroundTasks
from pydantic import BaseModel, EmailStr
from typing import List, Optional
import asyncio
import aiohttp
import logging
from datetime import datetime

app = FastAPI(title="User Management API", version="1.0.0")

# Pydantic models for request/response
class UserCreate(BaseModel):
    username: str
    email: EmailStr
    full_name: Optional[str] = None

class UserResponse(BaseModel):
    id: str
    username: str
    email: str
    full_name: Optional[str]
    created_at: datetime
    is_active: bool

# Async business logic functions
async def validate_user_async(user_data: UserCreate) -> dict:
    """Async function to validate user data with external service"""
    
    # Simulate async validation with external API
    async with aiohttp.ClientSession() as session:
        validation_url = f"https://api.validation-service.com/users/validate"
        
        payload = {
            'username': user_data.username,
            'email': user_data.email
        }
        
        try:
            async with session.post(validation_url, json=payload, timeout=5) as response:
                if response.status == 200:
                    validation_result = await response.json()
                    return validation_result
                else:
                    raise HTTPException(status_code=400, detail="Validation service error")
                    
        except asyncio.TimeoutError:
            # Fallback to local validation
            return await local_validation_fallback(user_data)

async def local_validation_fallback(user_data: UserCreate) -> dict:
    """Local async validation fallback"""
    await asyncio.sleep(0.1)  # Simulate async processing
    
    validation_result = {
        'username_available': True,
        'email_valid': '@' in user_data.email,
        'score': 0.8
    }
    
    return validation_result

async def save_user_async(user_data: UserCreate) -> UserResponse:
    """Async function to save user to database"""
    
    # Simulate async database operation
    await asyncio.sleep(0.2)
    
    user_response = UserResponse(
        id=f"user_{datetime.now().timestamp()}",
        username=user_data.username,
        email=user_data.email,
        full_name=user_data.full_name,
        created_at=datetime.now(),
        is_active=True
    )
    
    return user_response

async def send_welcome_email_async(user_email: str, username: str):
    """Async background task function"""
    
    # Simulate sending email
    await asyncio.sleep(1.0)
    
    email_content = f"""
    Welcome {username}!
    
    Your account has been created successfully.
    Email: {user_email}
    """
    
    # Simulate email sending
    logging.info(f"Welcome email sent to {user_email}")

# FastAPI endpoints with async function execution
@app.post("/users", response_model=UserResponse)
async def create_user_async(
    user_data: UserCreate,
    background_tasks: BackgroundTasks
):
    """Async endpoint for creating users"""
    try:
        # Execute async validation
        validation_result = await validate_user_async(user_data)
        
        if not validation_result.get('username_available'):
            raise HTTPException(status_code=400, detail="Username not available")
        
        if not validation_result.get('email_valid'):
            raise HTTPException(status_code=400, detail="Invalid email")
        
        # Execute async save operation
        created_user = await save_user_async(user_data)
        
        # Add background task for welcome email
        background_tasks.add_task(
            send_welcome_email_async,
            created_user.email,
            created_user.username
        )
        
        return created_user
        
    except HTTPException:
        raise
    except Exception as e:
        logging.error(f"Error creating user: {str(e)}")
        raise HTTPException(status_code=500, detail="Internal server error")

@app.get("/users/{user_id}", response_model=UserResponse)
async def get_user_async(user_id: str):
    """Async endpoint for retrieving user"""
    
    # Simulate async database lookup
    await asyncio.sleep(0.1)
    
    # Mock user data
    user = UserResponse(
        id=user_id,
        username="example_user",
        email="user@example.com",
        full_name="Example User",
        created_at=datetime.now(),
        is_active=True
    )
    
    return user
```

**📖 What This Does**  
FastAPI async functions execute concurrently, allowing the server to handle multiple requests simultaneously. Async functions use `await` for I/O operations like database queries or API calls.

**🔧 Async Execution Benefits**
- **Concurrency**: Handle multiple requests simultaneously
- **I/O Efficiency**: Don't block on network/database operations
- **Scalability**: Better resource utilization
- **Background Tasks**: Execute non-blocking operations

### **Advanced FastAPI Async Patterns**
```python
from fastapi import FastAPI, Depends, HTTPException, status
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials
import asyncio
import aioredis
import aiomysql
from typing import List, Dict, Any
import time

app = FastAPI()
security = HTTPBearer()

# Dependency injection for async services
class AsyncDatabaseService:
    def __init__(self):
        self.pool = None
    
    async def connect(self):
        """Async database connection"""
        self.pool = await aiomysql.create_pool(
            host='localhost',
            port=3306,
            user='user',
            password='password',
            db='myapp',
            minsize=1,
            maxsize=10
        )
    
    async def execute_query(self, query: str, params: tuple = None) -> List[Dict]:
        """Execute async database query"""
        async with self.pool.acquire() as connection:
            async with connection.cursor(aiomysql.DictCursor) as cursor:
                await cursor.execute(query, params)
                result = await cursor.fetchall()
                return result
    
    async def close(self):
        """Close database connections"""
        if self.pool:
            self.pool.close()
            await self.pool.wait_closed()

class AsyncCacheService:
    def __init__(self):
        self.redis = None
    
    async def connect(self):
        """Async Redis connection"""
        self.redis = await aioredis.from_url("redis://localhost")
    
    async def get(self, key: str) -> Any:
        """Get value from cache"""
        value = await self.redis.get(key)
        return value.decode() if value else None
    
    async def set(self, key: str, value: str, expire: int = 3600):
        """Set value in cache"""
        await self.redis.setex(key, expire, value)
    
    async def close(self):
        """Close Redis connection"""
        await self.redis.close()

# Global async services
db_service = AsyncDatabaseService()
cache_service = AsyncCacheService()

# Startup event to initialize async services
@app.on_event("startup")
async def startup_event():
    """Initialize async services on startup"""
    await db_service.connect()
    await cache_service.connect()
    logging.info("Async services initialized")

@app.on_event("shutdown")
async def shutdown_event():
    """Cleanup async services on shutdown"""
    await db_service.close()
    await cache_service.close()
    logging.info("Async services closed")

# Dependency functions
async def get_current_user(credentials: HTTPAuthorizationCredentials = Depends(security)):
    """Async dependency to get current user"""
    token = credentials.credentials
    
    # Check cache first
    cached_user = await cache_service.get(f"user_token:{token}")
    if cached_user:
        return json.loads(cached_user)
    
    # Query database if not in cache
    user_query = "SELECT * FROM users WHERE auth_token = %s"
    users = await db_service.execute_query(user_query, (token,))
    
    if not users:
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="Invalid authentication token"
        )
    
    user = users[0]
    
    # Cache user for 1 hour
    await cache_service.set(f"user_token:{token}", json.dumps(user), 3600)
    
    return user

# Async function with multiple concurrent operations
async def process_user_analytics(user_id: str) -> Dict[str, Any]:
    """Process user analytics with concurrent async operations"""
    
    # Define multiple async operations
    async def get_user_orders():
        query = "SELECT * FROM orders WHERE user_id = %s ORDER BY created_at DESC LIMIT 10"
        return await db_service.execute_query(query, (user_id,))
    
    async def get_user_sessions():
        query = "SELECT * FROM user_sessions WHERE user_id = %s AND created_at > DATE_SUB(NOW(), INTERVAL 30 DAY)"
        return await db_service.execute_query(query, (user_id,))
    
    async def get_user_preferences():
        cached_prefs = await cache_service.get(f"user_prefs:{user_id}")
        if cached_prefs:
            return json.loads(cached_prefs)
        
        query = "SELECT * FROM user_preferences WHERE user_id = %s"
        prefs = await db_service.execute_query(query, (user_id,))
        
        if prefs:
            await cache_service.set(f"user_prefs:{user_id}", json.dumps(prefs[0]))
            return prefs[0]
        return {}
    
    # Execute all operations concurrently
    start_time = time.time()
    
    orders, sessions, preferences = await asyncio.gather(
        get_user_orders(),
        get_user_sessions(),
        get_user_preferences(),
        return_exceptions=True
    )
    
    execution_time = time.time() - start_time
    
    # Handle potential exceptions
    if isinstance(orders, Exception):
        orders = []
    if isinstance(sessions, Exception):
        sessions = []
    if isinstance(preferences, Exception):
        preferences = {}
    
    # Process analytics
    analytics = {
        'user_id': user_id,
        'total_orders': len(orders),
        'recent_orders': orders[:5],
        'session_count': len(sessions),
        'preferences': preferences,
        'analytics_generated_at': datetime.now().isoformat(),
        'processing_time_seconds': round(execution_time, 3)
    }
    
    return analytics

@app.get("/users/{user_id}/analytics")
async def get_user_analytics(
    user_id: str,
    current_user: dict = Depends(get_current_user)
):
    """Endpoint with async dependency injection and concurrent processing"""
    
    # Check authorization
    if current_user['id'] != user_id and not current_user.get('is_admin'):
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="Not authorized to view this user's analytics"
        )
    
    try:
        # Execute async analytics processing
        analytics = await process_user_analytics(user_id)
        
        return {
            'status': 'success',
            'data': analytics
        }
        
    except Exception as e:
        logging.error(f"Error generating analytics for user {user_id}: {str(e)}")
        raise HTTPException(
            status_code=status.HTTP_500_INTERNAL_SERVER_ERROR,
            detail="Failed to generate analytics"
        )

# Async batch processing endpoint
@app.post("/users/batch-process")
async def batch_process_users(
    user_ids: List[str],
    current_user: dict = Depends(get_current_user)
):
    """Process multiple users concurrently"""
    
    if not current_user.get('is_admin'):
        raise HTTPException(
            status_code=status.HTTP_403_FORBIDDEN,
            detail="Admin access required"
        )
    
    # Limit batch size
    if len(user_ids) > 100:
        raise HTTPException(
            status_code=status.HTTP_400_BAD_REQUEST,
            detail="Batch size cannot exceed 100 users"
        )
    
    # Process users concurrently with semaphore to limit concurrency
    semaphore = asyncio.Semaphore(10)  # Limit to 10 concurrent operations
    
    async def process_single_user(user_id: str):
        async with semaphore:
            try:
                return await process_user_analytics(user_id)
            except Exception as e:
                return {'user_id': user_id, 'error': str(e)}
    
    # Execute batch processing
    start_time = time.time()
    results = await asyncio.gather(
        *[process_single_user(user_id) for user_id in user_ids],
        return_exceptions=True
    )
    total_time = time.time() - start_time
    
    # Separate successful and failed results
    successful = []
    failed = []
    
    for result in results:
        if isinstance(result, Exception):
            failed.append({'error': str(result)})
        elif 'error' in result:
            failed.append(result)
        else:
            successful.append(result)
    
    return {
        'status': 'completed',
        'total_users': len(user_ids),
        'successful': len(successful),
        'failed': len(failed),
        'processing_time_seconds': round(total_time, 3),
        'results': successful,
        'errors': failed
    }
```

**📖 What This Does**  
Advanced async patterns demonstrate dependency injection, concurrent processing, connection pooling, and batch operations. Functions execute efficiently with proper resource management.

---

## 🔄 **Function Execution Patterns in Web Applications**

### **Synchronous vs Asynchronous Execution**
```python
import time
import asyncio
import aiohttp
import requests
from concurrent.futures import ThreadPoolExecutor

# Synchronous function execution
def sync_api_call(url: str) -> dict:
    """Synchronous API call - blocks until complete"""
    start_time = time.time()
    
    response = requests.get(url, timeout=5)
    result = {
        'url': url,
        'status_code': response.status_code,
        'response_time': time.time() - start_time
    }
    
    return result

def sync_multiple_api_calls(urls: List[str]) -> List[dict]:
    """Synchronous calls - executed sequentially"""
    results = []
    total_start = time.time()
    
    for url in urls:
        result = sync_api_call(url)
        results.append(result)
    
    print(f"Sync total time: {time.time() - total_start:.2f} seconds")
    return results

# Asynchronous function execution
async def async_api_call(session: aiohttp.ClientSession, url: str) -> dict:
    """Asynchronous API call - non-blocking"""
    start_time = time.time()
    
    async with session.get(url, timeout=5) as response:
        result = {
            'url': url,
            'status_code': response.status,
            'response_time': time.time() - start_time
        }
        
        return result

async def async_multiple_api_calls(urls: List[str]) -> List[dict]:
    """Asynchronous calls - executed concurrently"""
    total_start = time.time()
    
    async with aiohttp.ClientSession() as session:
        # Execute all calls concurrently
        tasks = [async_api_call(session, url) for url in urls]
        results = await asyncio.gather(*tasks, return_exceptions=True)
    
    print(f"Async total time: {time.time() - total_start:.2f} seconds")
    return results

# Threading-based concurrent execution
def thread_multiple_api_calls(urls: List[str]) -> List[dict]:
    """Thread-based concurrent execution"""
    total_start = time.time()
    
    with ThreadPoolExecutor(max_workers=5) as executor:
        # Submit all tasks to thread pool
        futures = [executor.submit(sync_api_call, url) for url in urls]
        
        # Collect results
        results = [future.result() for future in futures]
    
    print(f"Thread total time: {time.time() - total_start:.2f} seconds")
    return results

# Flask endpoints demonstrating different execution patterns
@app.route('/api/sync-calls')
def sync_calls_endpoint():
    """Endpoint using synchronous function execution"""
    urls = [
        'https://httpbin.org/delay/1',
        'https://httpbin.org/delay/1',
        'https://httpbin.org/delay/1'
    ]
    
    results = sync_multiple_api_calls(urls)
    
    return jsonify({
        'execution_type': 'synchronous',
        'results': results
    })

@app.route('/api/thread-calls')
def thread_calls_endpoint():
    """Endpoint using thread-based concurrent execution"""
    urls = [
        'https://httpbin.org/delay/1',
        'https://httpbin.org/delay/1',
        'https://httpbin.org/delay/1'
    ]
    
    results = thread_multiple_api_calls(urls)
    
    return jsonify({
        'execution_type': 'threaded',
        'results': results
    })

# FastAPI endpoint with async execution
@app.get("/api/async-calls")
async def async_calls_endpoint():
    """Endpoint using asynchronous function execution"""
    urls = [
        'https://httpbin.org/delay/1',
        'https://httpbin.org/delay/1',
        'https://httpbin.org/delay/1'
    ]
    
    results = await async_multiple_api_calls(urls)
    
    return {
        'execution_type': 'asynchronous',
        'results': results
    }
```

**📖 What This Does**  
Different execution patterns demonstrate trade-offs between synchronous, asynchronous, and threaded approaches. Async execution provides the best performance for I/O-bound operations in web applications.

---

## 🎯 **Error Handling and Function Execution**

### **Comprehensive Error Handling**
```python
from flask import Flask, jsonify, request
from functools import wraps
import traceback
import logging
from typing import Callable, Any

# Custom exception classes
class ValidationError(Exception):
    """Custom exception for validation errors"""
    pass

class BusinessLogicError(Exception):
    """Custom exception for business logic errors"""
    pass

class ExternalServiceError(Exception):
    """Custom exception for external service errors"""
    pass

# Error handling decorator
def handle_errors(func: Callable) -> Callable:
    """Decorator to handle function execution errors"""
    @wraps(func)
    def wrapper(*args, **kwargs):
        try:
            return func(*args, **kwargs)
        
        except ValidationError as e:
            logging.warning(f"Validation error in {func.__name__}: {str(e)}")
            return jsonify({
                'status': 'error',
                'type': 'validation_error',
                'message': str(e)
            }), 400
        
        except BusinessLogicError as e:
            logging.error(f"Business logic error in {func.__name__}: {str(e)}")
            return jsonify({
                'status': 'error',
                'type': 'business_logic_error',
                'message': str(e)
            }), 422
        
        except ExternalServiceError as e:
            logging.error(f"External service error in {func.__name__}: {str(e)}")
            return jsonify({
                'status': 'error',
                'type': 'external_service_error',
                'message': 'External service temporarily unavailable'
            }), 503
        
        except Exception as e:
            logging.error(f"Unexpected error in {func.__name__}: {str(e)}")
            logging.error(traceback.format_exc())
            return jsonify({
                'status': 'error',
                'type': 'internal_error',
                'message': 'An unexpected error occurred'
            }), 500
    
    return wrapper

# Business logic functions with error handling
def validate_order_data(order_data: dict) -> dict:
    """Validate order data with specific error types"""
    
    if not order_data.get('customer_id'):
        raise ValidationError("Customer ID is required")
    
    if not order_data.get('items') or len(order_data['items']) == 0:
        raise ValidationError("Order must contain at least one item")
    
    total_amount = sum(item.get('price', 0) * item.get('quantity', 0) 
                      for item in order_data['items'])
    
    if total_amount <= 0:
        raise ValidationError("Order total must be greater than zero")
    
    if total_amount > 10000:
        raise BusinessLogicError("Order amount exceeds maximum limit of $10,000")
    
    # Add calculated fields
    order_data['total_amount'] = total_amount
    order_data['item_count'] = len(order_data['items'])
    
    return order_data

def check_inventory_availability(items: list) -> dict:
    """Check inventory with external service"""
    
    try:
        # Simulate external service call
        import requests
        
        response = requests.post(
            'https://api.inventory-service.com/check',
            json={'items': items},
            timeout=5
        )
        
        if response.status_code != 200:
            raise ExternalServiceError("Inventory service returned error")
        
        inventory_result = response.json()
        
        # Check if all items are available
        unavailable_items = [
            item for item in inventory_result.get('items', [])
            if not item.get('available', False)
        ]
        
        if unavailable_items:
            raise BusinessLogicError(f"Items not available: {[item['id'] for item in unavailable_items]}")
        
        return inventory_result
        
    except requests.RequestException as e:
        raise ExternalServiceError(f"Failed to connect to inventory service: {str(e)}")

def process_payment(customer_id: str, amount: float, payment_method: dict) -> dict:
    """Process payment with error handling"""
    
    if amount <= 0:
        raise ValidationError("Payment amount must be positive")
    
    if not payment_method.get('type'):
        raise ValidationError("Payment method type is required")
    
    try:
        # Simulate payment processing
        import random
        
        if random.random() < 0.1:  # 10% chance of payment failure
            raise ExternalServiceError("Payment gateway error")
        
        if random.random() < 0.05:  # 5% chance of insufficient funds
            raise BusinessLogicError("Insufficient funds")
        
        # Successful payment
        return {
            'transaction_id': f"txn_{int(time.time())}",
            'amount': amount,
            'status': 'completed',
            'processed_at': datetime.now().isoformat()
        }
        
    except Exception as e:
        if isinstance(e, (ExternalServiceError, BusinessLogicError)):
            raise
        else:
            raise ExternalServiceError(f"Payment processing failed: {str(e)}")

# Flask endpoint with comprehensive error handling
@app.route('/orders', methods=['POST'])
@handle_errors
def create_order():
    """Create order with comprehensive error handling"""
    
    # Get and validate request data
    order_data = request.get_json()
    
    if not order_data:
        raise ValidationError("Request body is required")
    
    # Execute business logic functions with error propagation
    validated_order = validate_order_data(order_data)
    
    inventory_check = check_inventory_availability(validated_order['items'])
    
    payment_result = process_payment(
        validated_order['customer_id'],
        validated_order['total_amount'],
        validated_order.get('payment_method', {})
    )
    
    # Create order record
    order_record = {
        'order_id': f"ord_{int(time.time())}",
        'customer_id': validated_order['customer_id'],
        'items': validated_order['items'],
        'total_amount': validated_order['total_amount'],
        'payment_transaction': payment_result['transaction_id'],
        'status': 'confirmed',
        'created_at': datetime.now().isoformat()
    }
    
    # Log successful order creation
    logging.info(f"Order created successfully: {order_record['order_id']}")
    
    return jsonify({
        'status': 'success',
        'message': 'Order created successfully',
        'order': order_record
    }), 201
```

**📖 What This Does**  
Comprehensive error handling ensures functions execute safely with appropriate error responses. Different error types (validation, business logic, external service) are handled with specific HTTP status codes and messages.

---

## 🎯 **Best Practices for Web Application Function Execution**

### **Function Design Principles**
1. **Single Responsibility**: Each function should have one clear purpose
2. **Pure Functions**: Avoid side effects when possible
3. **Error Handling**: Use appropriate exception types and handling
4. **Async When Beneficial**: Use async for I/O-bound operations
5. **Dependency Injection**: Use FastAPI's dependency system for better testing

### **Performance Considerations**
- Use async functions for I/O operations (database, API calls)
- Implement connection pooling for database operations
- Use caching for frequently accessed data
- Limit concurrent operations with semaphores
- Monitor function execution times

### **Security Considerations**
- Validate all input data
- Use proper authentication and authorization
- Sanitize data before processing
- Log security-related events
- Implement rate limiting for public endpoints

---

📄 **File Path:** `/DevOps/01-Fundamentals_And_Environment_Setup/04-Python_For_DevOps/Python_Basics/05-Python_Function_Execution_Web_Applications.md` 