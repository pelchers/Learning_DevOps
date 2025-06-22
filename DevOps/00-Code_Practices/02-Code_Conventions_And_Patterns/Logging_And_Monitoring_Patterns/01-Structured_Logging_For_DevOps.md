# Structured Logging for DevOps

## 📖 What This File Covers
Master structured logging patterns that enable effective monitoring, troubleshooting, and observability in DevOps environments. Learn how to implement consistent, searchable, and actionable logging across your infrastructure.

## 🎯 Learning Objectives
- Implement structured logging for consistent data format
- Create meaningful log levels and categories
- Build correlation IDs for request tracking
- Design logging for observability and alerting
- Integrate logs with monitoring and alerting systems

---

## 📊 **Structured Logging Fundamentals**

### **Basic Structured Logging Pattern**
```python
import json
import logging
import datetime
import uuid
from typing import Dict, Any, Optional

class StructuredLogger:
    """Structured logger for DevOps applications"""
    
    def __init__(self, service_name: str, environment: str):
        self.service_name = service_name
        self.environment = environment
        self.logger = logging.getLogger(service_name)
        self._setup_logging()
    
    def _setup_logging(self):
        """Configure structured logging format"""
        handler = logging.StreamHandler()
        handler.setFormatter(self._get_json_formatter())
        self.logger.addHandler(handler)
        self.logger.setLevel(logging.INFO)
    
    def _get_json_formatter(self):
        """Create JSON formatter for structured logs"""
        class JSONFormatter(logging.Formatter):
            def format(self, record):
                log_entry = {
                    'timestamp': datetime.datetime.utcnow().isoformat() + 'Z',
                    'service': record.name,
                    'level': record.levelname,
                    'message': record.getMessage(),
                    'environment': self.environment
                }
                
                # Add any extra fields
                if hasattr(record, 'correlation_id'):
                    log_entry['correlation_id'] = record.correlation_id
                
                if hasattr(record, 'user_id'):
                    log_entry['user_id'] = record.user_id
                
                if hasattr(record, 'request_id'):
                    log_entry['request_id'] = record.request_id
                
                if hasattr(record, 'deployment_id'):
                    log_entry['deployment_id'] = record.deployment_id
                
                return json.dumps(log_entry)
        
        return JSONFormatter()
    
    def info(self, message: str, **kwargs):
        """Log info level message with context"""
        extra = self._build_extra(**kwargs)
        self.logger.info(message, extra=extra)
    
    def warning(self, message: str, **kwargs):
        """Log warning level message with context"""
        extra = self._build_extra(**kwargs)
        self.logger.warning(message, extra=extra)
    
    def error(self, message: str, exception: Optional[Exception] = None, **kwargs):
        """Log error level message with context"""
        extra = self._build_extra(**kwargs)
        if exception:
            extra['exception_type'] = type(exception).__name__
            extra['exception_message'] = str(exception)
        self.logger.error(message, extra=extra)
    
    def _build_extra(self, **kwargs) -> Dict[str, Any]:
        """Build extra context for logging"""
        extra = {}
        
        # Standard context fields
        for key, value in kwargs.items():
            if key in ['correlation_id', 'user_id', 'request_id', 'deployment_id']:
                extra[key] = value
        
        return extra

# Usage example
logger = StructuredLogger('api-service', 'production')

# Log with correlation ID for request tracking
logger.info(
    "User login successful",
    correlation_id="req_12345",
    user_id="user_67890",
    request_id="api_req_001"
)

# Log deployment events
logger.info(
    "Deployment started",
    deployment_id="deploy_v1.2.3",
    environment="production",
    previous_version="v1.2.2"
)

# Log errors with context
try:
    # Some operation
    pass
except Exception as e:
    logger.error(
        "Database connection failed",
        exception=e,
        correlation_id="req_12345",
        database_host="db.company.com"
    )
```

---

## 🎯 **DevOps-Specific Logging Patterns**

### **Deployment and Infrastructure Logging**
```python
import time
from enum import Enum
from dataclasses import dataclass, asdict
from typing import List, Optional

class DeploymentStatus(Enum):
    STARTED = "started"
    IN_PROGRESS = "in_progress"  
    SUCCESSFUL = "successful"
    FAILED = "failed"
    ROLLED_BACK = "rolled_back"

@dataclass
class DeploymentContext:
    """Context for deployment logging"""
    deployment_id: str
    service_name: str
    version: str
    environment: str
    previous_version: Optional[str] = None
    user: Optional[str] = None
    
    def to_dict(self) -> Dict[str, Any]:
        return asdict(self)

class DeploymentLogger:
    """Specialized logger for deployment operations"""
    
    def __init__(self, base_logger: StructuredLogger):
        self.logger = base_logger
    
    def log_deployment_start(self, context: DeploymentContext):
        """Log deployment start event"""
        self.logger.info(
            f"Deployment started: {context.service_name} {context.version}",
            **context.to_dict(),
            status=DeploymentStatus.STARTED.value,
            event_type="deployment",
            action="start"
        )
    
    def log_deployment_progress(self, context: DeploymentContext, stage: str, details: Dict[str, Any]):
        """Log deployment progress"""
        self.logger.info(
            f"Deployment progress: {stage}",
            **context.to_dict(),
            status=DeploymentStatus.IN_PROGRESS.value,
            event_type="deployment",
            action="progress",
            stage=stage,
            **details
        )
    
    def log_deployment_success(self, context: DeploymentContext, duration_seconds: float):
        """Log successful deployment"""
        self.logger.info(
            f"Deployment successful: {context.service_name} {context.version}",
            **context.to_dict(),
            status=DeploymentStatus.SUCCESSFUL.value,
            event_type="deployment",
            action="complete",
            duration_seconds=duration_seconds
        )
    
    def log_deployment_failure(self, context: DeploymentContext, error: str, duration_seconds: float):
        """Log failed deployment"""
        self.logger.error(
            f"Deployment failed: {context.service_name} {context.version}",
            **context.to_dict(),
            status=DeploymentStatus.FAILED.value,
            event_type="deployment",
            action="failed",
            error_message=error,
            duration_seconds=duration_seconds
        )

# Usage example
base_logger = StructuredLogger('deployment-service', 'production')
deployment_logger = DeploymentLogger(base_logger)

# Create deployment context
context = DeploymentContext(
    deployment_id="deploy_20231120_001",
    service_name="api-gateway",
    version="v2.1.0",
    environment="production",
    previous_version="v2.0.5",
    user="john.doe@company.com"
)

# Log deployment lifecycle
deployment_logger.log_deployment_start(context)

deployment_logger.log_deployment_progress(
    context, 
    "database_migration",
    {"migrations_count": 3, "affected_tables": ["users", "orders"]}
)

deployment_logger.log_deployment_success(context, duration_seconds=120.5)
```

---

## 📈 **Monitoring Integration Patterns**

### **Metrics and Alerts from Logs**
```python
import time
from collections import Counter, defaultdict
from typing import Dict, List

class MetricsCollector:
    """Collect metrics from structured logs"""
    
    def __init__(self):
        self.error_counts = Counter()
        self.response_times = defaultdict(list)
        self.deployment_events = []
    
    def process_log_entry(self, log_entry: Dict[str, Any]):
        """Process a single log entry for metrics"""
        
        # Count errors by service
        if log_entry.get('level') == 'ERROR':
            service = log_entry.get('service', 'unknown')
            self.error_counts[service] += 1
        
        # Track response times
        if 'response_time_ms' in log_entry:
            service = log_entry.get('service', 'unknown')
            endpoint = log_entry.get('endpoint', 'unknown')
            self.response_times[f"{service}:{endpoint}"].append(
                log_entry['response_time_ms']
            )
        
        # Track deployment events
        if log_entry.get('event_type') == 'deployment':
            self.deployment_events.append({
                'timestamp': log_entry.get('timestamp'),
                'service': log_entry.get('service_name'),
                'status': log_entry.get('status'),
                'version': log_entry.get('version')
            })
    
    def generate_metrics_report(self) -> Dict[str, Any]:
        """Generate metrics report from collected data"""
        
        # Calculate average response times
        avg_response_times = {}
        for service_endpoint, times in self.response_times.items():
            avg_response_times[service_endpoint] = {
                'avg_ms': sum(times) / len(times),
                'max_ms': max(times),
                'min_ms': min(times),
                'count': len(times)
            }
        
        # Recent deployment status
        recent_deployments = sorted(
            self.deployment_events,
            key=lambda x: x['timestamp'],
            reverse=True
        )[:10]
        
        return {
            'error_counts': dict(self.error_counts),
            'response_times': avg_response_times,
            'recent_deployments': recent_deployments,
            'generated_at': time.time()
        }

class AlertingEngine:
    """Generate alerts based on log patterns"""
    
    def __init__(self, metrics_collector: MetricsCollector):
        self.metrics = metrics_collector
        self.alert_thresholds = {
            'error_rate_per_minute': 10,
            'avg_response_time_ms': 5000,
            'deployment_failure_count': 2
        }
    
    def check_error_rate_alerts(self) -> List[Dict[str, Any]]:
        """Check for high error rate alerts"""
        alerts = []
        
        for service, error_count in self.metrics.error_counts.items():
            # Simplified: assume we're checking last minute
            if error_count > self.alert_thresholds['error_rate_per_minute']:
                alerts.append({
                    'type': 'high_error_rate',
                    'service': service,
                    'error_count': error_count,
                    'threshold': self.alert_thresholds['error_rate_per_minute'],
                    'severity': 'critical' if error_count > 50 else 'warning'
                })
        
        return alerts
    
    def check_performance_alerts(self) -> List[Dict[str, Any]]:
        """Check for performance degradation alerts"""
        alerts = []
        
        for service_endpoint, metrics in self.metrics.response_times.items():
            avg_time = metrics['avg_ms']
            if avg_time > self.alert_thresholds['avg_response_time_ms']:
                alerts.append({
                    'type': 'slow_response_time',
                    'service_endpoint': service_endpoint,
                    'avg_response_time_ms': avg_time,
                    'threshold': self.alert_thresholds['avg_response_time_ms'],
                    'severity': 'warning'
                })
        
        return alerts
    
    def check_deployment_alerts(self) -> List[Dict[str, Any]]:
        """Check for deployment failure alerts"""
        alerts = []
        
        # Count recent failed deployments
        failed_deployments = [
            d for d in self.metrics.deployment_events 
            if d['status'] == 'failed'
        ]
        
        if len(failed_deployments) >= self.alert_thresholds['deployment_failure_count']:
            alerts.append({
                'type': 'deployment_failures',
                'failed_count': len(failed_deployments),
                'threshold': self.alert_thresholds['deployment_failure_count'],
                'recent_failures': failed_deployments[-3:],
                'severity': 'critical'
            })
        
        return alerts

# Usage example for monitoring integration
def process_logs_for_monitoring(log_entries: List[Dict[str, Any]]):
    """Process logs and generate alerts"""
    
    collector = MetricsCollector()
    
    # Process all log entries
    for entry in log_entries:
        collector.process_log_entry(entry)
    
    # Generate metrics report
    metrics_report = collector.generate_metrics_report()
    
    # Check for alerts
    alerting = AlertingEngine(collector)
    
    error_alerts = alerting.check_error_rate_alerts()
    performance_alerts = alerting.check_performance_alerts()
    deployment_alerts = alerting.check_deployment_alerts()
    
    all_alerts = error_alerts + performance_alerts + deployment_alerts
    
    # Log the monitoring results
    monitoring_logger = StructuredLogger('monitoring-service', 'production')
    
    monitoring_logger.info(
        "Monitoring report generated",
        event_type="monitoring",
        metrics_summary=metrics_report,
        alert_count=len(all_alerts)
    )
    
    # Log each alert
    for alert in all_alerts:
        monitoring_logger.warning(
            f"Alert triggered: {alert['type']}",
            event_type="alert",
            **alert
        )
    
    return {
        'metrics': metrics_report,
        'alerts': all_alerts
    }
```

---

## 🔍 **Log Aggregation and Search Patterns**

### **ELK Stack Integration**
```python
from datetime import datetime
from elasticsearch import Elasticsearch

class ELKIntegration:
    """Integration with Elasticsearch for log aggregation"""
    
    def __init__(self, elasticsearch_host: str):
        self.es = Elasticsearch([elasticsearch_host])
        self.index_pattern = "devops-logs-{date}"
    
    def index_log_entry(self, log_entry: Dict[str, Any]):
        """Index a log entry into Elasticsearch"""
        
        # Add timestamp if not present
        if 'timestamp' not in log_entry:
            log_entry['timestamp'] = datetime.utcnow().isoformat() + 'Z'
        
        # Determine index name based on date
        date_str = datetime.utcnow().strftime('%Y-%m-%d')
        index_name = self.index_pattern.format(date=date_str)
        
        try:
            self.es.index(
                index=index_name,
                body=log_entry
            )
        except Exception as e:
            print(f"Failed to index log entry: {e}")
    
    def search_logs(self, query: str, time_range: str = "1h") -> List[Dict[str, Any]]:
        """Search logs with specific query"""
        
        search_body = {
            "query": {
                "bool": {
                    "must": [
                        {
                            "query_string": {
                                "query": query
                            }
                        },
                        {
                            "range": {
                                "timestamp": {
                                    "gte": f"now-{time_range}"
                                }
                            }
                        }
                    ]
                }
            },
            "sort": [
                {
                    "timestamp": {
                        "order": "desc"
                    }
                }
            ]
        }
        
        try:
            response = self.es.search(
                index=self.index_pattern.format(date="*"),
                body=search_body
            )
            
            return [hit['_source'] for hit in response['hits']['hits']]
            
        except Exception as e:
            print(f"Search failed: {e}")
            return []

# Enhanced logger with ELK integration
class ELKStructuredLogger(StructuredLogger):
    """Structured logger with ELK Stack integration"""
    
    def __init__(self, service_name: str, environment: str, elk_host: str):
        super().__init__(service_name, environment)
        self.elk = ELKIntegration(elk_host)
    
    def _log_with_elk(self, level: str, message: str, **kwargs):
        """Log message and send to ELK Stack"""
        
        # Create structured log entry
        log_entry = {
            'timestamp': datetime.utcnow().isoformat() + 'Z',
            'service': self.service_name,
            'environment': self.environment,
            'level': level,
            'message': message,
            **kwargs
        }
        
        # Index in Elasticsearch
        self.elk.index_log_entry(log_entry)
        
        # Also log locally
        extra = self._build_extra(**kwargs)
        getattr(self.logger, level.lower())(message, extra=extra)
    
    def info(self, message: str, **kwargs):
        self._log_with_elk('INFO', message, **kwargs)
    
    def warning(self, message: str, **kwargs):
        self._log_with_elk('WARNING', message, **kwargs)
    
    def error(self, message: str, exception: Optional[Exception] = None, **kwargs):
        if exception:
            kwargs['exception_type'] = type(exception).__name__
            kwargs['exception_message'] = str(exception)
        self._log_with_elk('ERROR', message, **kwargs)
```

---

**💡 Key Takeaway**: Structured logging transforms logs from text into actionable data that powers monitoring, alerting, and observability across your DevOps infrastructure! 