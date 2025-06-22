# Load Balancing and Content Delivery

## 📖 What This File Covers
Master load balancing strategies, Content Delivery Networks (CDNs), and traffic distribution patterns. Learn how to scale applications globally, improve performance, and ensure high availability.

## 🎯 Learning Objectives
- Understand different load balancing algorithms and strategies
- Configure Application Load Balancers (ALB) and Network Load Balancers (NLB)
- Implement CDN strategies for global content delivery
- Design fault-tolerant, scalable architectures
- Monitor and optimize traffic distribution

---

## ⚖️ **Load Balancing Fundamentals**

### **Load Balancing Algorithms**
```
ROUND ROBIN
├── Requests distributed in order
├── Simple and fair distribution
├── Best for: Similar server capacity
└── Issue: Doesn't consider server load

LEAST CONNECTIONS
├── Routes to server with fewest active connections
├── Better for long-running requests
├── Best for: Variable request processing times
└── Issue: Requires connection tracking

WEIGHTED ROUND ROBIN
├── Assigns weights based on server capacity
├── More powerful servers get more requests
├── Best for: Mixed server configurations
└── Example: Server A (weight 3), Server B (weight 1)

IP HASH
├── Routes based on client IP hash
├── Same client always goes to same server
├── Best for: Session affinity requirements
└── Issue: Uneven distribution possible

LEAST RESPONSE TIME
├── Routes to server with fastest response
├── Monitors health and performance
├── Best for: Performance-critical applications
└── Issue: More complex to implement
```

### **Real-World Load Balancer Architecture**
```
INTERNET
    ↓
┌─────────────────────────────────────────────────────────┐
│               CLOUDFLARE CDN                            │
│           (Global Edge Locations)                       │
│    ┌─────────────┐  ┌─────────────┐  ┌─────────────┐   │
│    │ Los Angeles │  │   London    │  │   Tokyo     │   │
│    │ 104.21.35.42│  │104.21.35.43 │  │104.21.35.44 │   │
│    └─────────────┘  └─────────────┘  └─────────────┘   │
└─────────────────────┬───────────────────────────────────┘
                      ↓
┌─────────────────────────────────────────────────────────┐
│          AWS APPLICATION LOAD BALANCER                  │
│              alb-prod.company.com                       │
│              203.0.113.100                             │
│                                                        │
│  Health Checks: Every 30s                              │
│  Targets: 6 web servers                                │
│  Algorithm: Least outstanding requests                  │
└─────────────────────┬───────────────────────────────────┘
                      ↓
┌─────────┬───────────┴───────────┬─────────────────────┐
│         ↓                       ↓                     ↓
│   WEB-01 (AZ-1a)          WEB-02 (AZ-1b)      WEB-03 (AZ-1c)
│   10.0.10.10              10.0.10.11          10.0.10.12
│   Status: Healthy         Status: Healthy     Status: Healthy
│   Connections: 45         Connections: 52     Connections: 38
│                           
│   WEB-04 (AZ-1a)          WEB-05 (AZ-1b)      WEB-06 (AZ-1c)
│   10.0.10.13              10.0.10.14          10.0.10.15
│   Status: Healthy         Status: Draining    Status: Healthy
│   Connections: 41         Connections: 12     Connections: 44
```

---

## 🌐 **AWS Load Balancer Configuration**

### **Application Load Balancer (ALB)**
```hcl
# Application Load Balancer
resource "aws_lb" "main" {
  name               = "production-alb"
  internal           = false
  load_balancer_type = "application"
  security_groups    = [aws_security_group.alb.id]
  subnets            = aws_subnet.public[*].id

  enable_deletion_protection = true
  enable_http2              = true
  idle_timeout              = 60

  tags = {
    Name        = "production-alb"
    Environment = "production"
  }
}

# Target Group for Web Servers
resource "aws_lb_target_group" "web" {
  name     = "web-servers-tg"
  port     = 80
  protocol = "HTTP"
  vpc_id   = aws_vpc.main.id

  health_check {
    enabled             = true
    healthy_threshold   = 2
    unhealthy_threshold = 3
    timeout             = 5
    interval            = 30
    path                = "/health"
    matcher             = "200"
  }

  tags = {
    Name = "web-servers-tg"
  }
}

# HTTPS Listener
resource "aws_lb_listener" "https" {
  load_balancer_arn = aws_lb.main.arn
  port              = "443"
  protocol          = "HTTPS"
  ssl_policy        = "ELBSecurityPolicy-TLS-1-2-2017-01"
  certificate_arn   = aws_acm_certificate.main.arn

  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.web.arn
  }
}

# HTTP Redirect to HTTPS
resource "aws_lb_listener" "http" {
  load_balancer_arn = aws_lb.main.arn
  port              = "80"
  protocol          = "HTTP"

  default_action {
    type = "redirect"
    redirect {
      port        = "443"
      protocol    = "HTTPS"
      status_code = "HTTP_301"
    }
  }
}
```

### **Network Load Balancer (NLB)**
```hcl
# High-performance Network Load Balancer
resource "aws_lb" "network" {
  name               = "production-nlb"
  internal           = false
  load_balancer_type = "network"
  subnets            = aws_subnet.public[*].id

  enable_deletion_protection       = true
  enable_cross_zone_load_balancing = true

  tags = {
    Name        = "production-nlb"
    Environment = "production"
  }
}

# NLB Target Group
resource "aws_lb_target_group" "tcp" {
  name     = "tcp-servers-tg"
  port     = 80
  protocol = "TCP"
  vpc_id   = aws_vpc.main.id

  health_check {
    enabled             = true
    healthy_threshold   = 2
    unhealthy_threshold = 2
    timeout             = 6
    interval            = 10
    port                = "traffic-port"
    protocol            = "TCP"
  }

  tags = {
    Name = "tcp-servers-tg"
  }
}
```

---

## 🚀 **Content Delivery Network (CDN)**

### **CloudFront Distribution**
```hcl
# CloudFront Distribution
resource "aws_cloudfront_distribution" "main" {
  enabled             = true
  is_ipv6_enabled     = true
  comment             = "Production CDN"
  default_root_object = "index.html"
  price_class         = "PriceClass_All"

  # Custom domain
  aliases = ["cdn.company.com"]

  # Origin for Static Content (S3)
  origin {
    domain_name = aws_s3_bucket.static_content.bucket_regional_domain_name
    origin_id   = "S3-static-content"

    s3_origin_config {
      origin_access_identity = aws_cloudfront_origin_access_identity.main.cloudfront_access_identity_path
    }
  }

  # Origin for Dynamic Content (ALB)
  origin {
    domain_name = aws_lb.main.dns_name
    origin_id   = "ALB-dynamic-content"

    custom_origin_config {
      http_port              = 80
      https_port             = 443
      origin_protocol_policy = "https-only"
      origin_ssl_protocols   = ["TLSv1.2"]
    }
  }

  # Default Cache Behavior (Dynamic)
  default_cache_behavior {
    allowed_methods        = ["DELETE", "GET", "HEAD", "OPTIONS", "PATCH", "POST", "PUT"]
    cached_methods         = ["GET", "HEAD"]
    target_origin_id       = "ALB-dynamic-content"
    compress               = true
    viewer_protocol_policy = "redirect-to-https"

    forwarded_values {
      query_string = true
      headers      = ["Host", "Authorization"]
      
      cookies {
        forward = "all"
      }
    }

    min_ttl     = 0
    default_ttl = 0
    max_ttl     = 31536000
  }

  # Cache Behavior for Static Assets
  ordered_cache_behavior {
    path_pattern           = "/static/*"
    allowed_methods        = ["GET", "HEAD"]
    cached_methods         = ["GET", "HEAD"]
    target_origin_id       = "S3-static-content"
    compress               = true
    viewer_protocol_policy = "redirect-to-https"

    forwarded_values {
      query_string = false
      cookies {
        forward = "none"
      }
    }

    min_ttl     = 86400    # 1 day
    default_ttl = 2592000  # 30 days
    max_ttl     = 31536000 # 1 year
  }

  # SSL Certificate
  viewer_certificate {
    acm_certificate_arn      = aws_acm_certificate.main.arn
    ssl_support_method       = "sni-only"
    minimum_protocol_version = "TLSv1.2_2021"
  }

  # Geographic Restrictions
  restrictions {
    geo_restriction {
      restriction_type = "none"
    }
  }

  tags = {
    Name        = "production-cloudfront"
    Environment = "production"
  }
}
```

### **Global CDN Strategy**
```
GLOBAL EDGE LOCATIONS

North America (30+ locations)
├── US East: Virginia, Ohio, N. California
├── US West: Los Angeles, Seattle, San Francisco
├── Canada: Toronto, Montreal
└── Mexico: Mexico City

Europe (25+ locations)
├── UK: London, Manchester
├── Germany: Frankfurt, Berlin
├── France: Paris, Marseille
├── Netherlands: Amsterdam
└── Spain: Madrid

Asia Pacific (20+ locations)
├── Japan: Tokyo, Osaka
├── Singapore: Singapore
├── Australia: Sydney, Melbourne
├── India: Mumbai, Chennai, New Delhi
└── South Korea: Seoul

CONTENT CACHING STRATEGY

Static Assets (Long TTL)
├── Images, CSS, JS: 1 year cache
├── Fonts, Icons: 1 year cache
├── Videos: 6 months cache
└── Documents: 1 month cache

Dynamic Content (Short TTL)
├── HTML pages: 1 hour cache
├── API responses: No cache
├── User-specific data: No cache
└── Search results: 15 minutes cache
```

---

## 📊 **Load Balancer Monitoring**

### **Health Check Script**
```python
#!/usr/bin/env python3
"""
Load Balancer Health Monitoring
"""

import boto3

class LoadBalancerMonitor:
    def __init__(self, region='us-east-1'):
        self.elbv2 = boto3.client('elbv2', region_name=region)
        self.cloudwatch = boto3.client('cloudwatch', region_name=region)
    
    def check_target_health(self, target_group_arn):
        """Check health of targets in target group"""
        response = self.elbv2.describe_target_health(
            TargetGroupArn=target_group_arn
        )
        
        targets = response['TargetHealthDescriptions']
        healthy_targets = []
        unhealthy_targets = []
        
        for target in targets:
            target_info = {
                'id': target['Target']['Id'],
                'port': target['Target']['Port'],
                'state': target['TargetHealth']['State']
            }
            
            if target_info['state'] == 'healthy':
                healthy_targets.append(target_info)
            else:
                unhealthy_targets.append(target_info)
        
        return {
            'healthy': healthy_targets,
            'unhealthy': unhealthy_targets,
            'healthy_count': len(healthy_targets),
            'total_count': len(targets)
        }
    
    def generate_health_report(self, target_group_arns):
        """Generate health report for target groups"""
        print("🏥 Load Balancer Health Report")
        print("=" * 40)
        
        for i, tg_arn in enumerate(target_group_arns):
            tg_name = tg_arn.split('/')[-2]
            health_info = self.check_target_health(tg_arn)
            
            print(f"\nTarget Group {i+1}: {tg_name}")
            print(f"Healthy: {health_info['healthy_count']}/{health_info['total_count']}")
            
            if health_info['unhealthy']:
                print("Unhealthy targets:")
                for target in health_info['unhealthy']:
                    print(f"  - {target['id']}:{target['port']} ({target['state']})")

# Usage
monitor = LoadBalancerMonitor()
target_groups = [
    "arn:aws:elasticloadbalancing:us-east-1:123456789012:targetgroup/web-servers-tg/abc123",
    "arn:aws:elasticloadbalancing:us-east-1:123456789012:targetgroup/api-servers-tg/def456"
]

monitor.generate_health_report(target_groups)
```

### **Performance Monitoring**
```bash
#!/bin/bash
# Load Balancer Performance Check

LOAD_BALANCER_DNS="alb-prod.company.com"
ENDPOINTS=("/health" "/api/status" "/metrics")

echo "⚡ Load Balancer Performance Test"
echo "=================================="
echo "Target: $LOAD_BALANCER_DNS"
echo ""

for endpoint in "${ENDPOINTS[@]}"; do
    echo "Testing $endpoint..."
    
    # Test response time and status
    response=$(curl -o /dev/null -s -w "%{http_code},%{time_total},%{time_connect}" \
        "https://$LOAD_BALANCER_DNS$endpoint")
    
    IFS=',' read -r status_code total_time connect_time <<< "$response"
    
    if [[ "$status_code" == "200" ]]; then
        echo "✅ Status: $status_code"
        echo "   Response Time: ${total_time}s"
        echo "   Connect Time: ${connect_time}s"
    else
        echo "❌ Status: $status_code"
        echo "   Response Time: ${total_time}s"
    fi
    echo ""
done

echo "Load test with 10 concurrent requests:"
ab -n 100 -c 10 "https://$LOAD_BALANCER_DNS/health" | grep -E "(Requests per second|Time per request)"
```

---

**💡 Key Takeaway**: Effective load balancing and CDN strategies are essential for scalable, high-performance applications - distribute traffic intelligently and cache content globally for optimal user experience!