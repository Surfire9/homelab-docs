# [Service Name] - Configuration Reference

**Last Updated:** 2024-11-13

## Configuration Files

### Main Configuration
**Location:** `/config/config.yaml` (inside container)
**Host Path:** `/opt/services/service-name/config/config.yaml`
```yaml
# General Settings
server:
  host: 0.0.0.0
  port: 8080
  workers: 4
  timeout: 30

# Database Configuration
database:
  type: postgresql
  host: database.lab.local
  port: 5432
  name: servicedb
  user: serviceuser
  password: ${DATABASE_PASSWORD}
  pool:
    min: 5
    max: 20
    idle_timeout: 300

# Authentication
auth:
  type: ldap
  ldap:
    url: ldap://ldap.lab.local:389
    bind_dn: cn=service,ou=services,dc=lab,dc=local
    bind_password: ${LDAP_PASSWORD}
    user_base: ou=users,dc=lab,dc=local
    user_filter: "(uid={username})"
    group_base: ou=groups,dc=lab,dc=local
  session:
    timeout: 3600
    cookie_secure: true
    cookie_httponly: true

# Storage
storage:
  data_path: /data
  temp_path: /tmp
  max_upload_size: 100MB
  allowed_types:
    - image/jpeg
    - image/png
    - application/pdf

# Logging
logging:
  level: INFO  # DEBUG, INFO, WARNING, ERROR
  format: json
  output: /logs/service.log
  rotation:
    max_size: 100MB
    max_files: 10
    compress: true

# Monitoring
monitoring:
  metrics_enabled: true
  metrics_port: 9090
  health_check_path: /health
  
# Features
features:
  feature_a: true
  feature_b: false
  beta_features: false

# External Services
external:
  api_endpoint: https://api.example.com
  api_key: ${EXTERNAL_API_KEY}
  timeout: 10
  retry_attempts: 3

# Email (if applicable)
email:
  smtp_host: smtp.lab.local
  smtp_port: 587
  smtp_user: service@lab.local
  smtp_password: ${SMTP_PASSWORD}
  from_address: noreply@lab.local
  use_tls: true

# Caching (if applicable)
cache:
  type: redis
  host: redis.lab.local
  port: 6379
  password: ${REDIS_PASSWORD}
  ttl: 3600
```

## Environment Variables

### Required Variables
```bash
# Must be set for service to start
DATABASE_URL=postgresql://user:pass@host:5432/db
SECRET_KEY=generate-32-char-random-string
LDAP_BIND_PASSWORD=password
```

### Optional Variables
```bash
# Override default settings
SERVICE_PORT=8080
LOG_LEVEL=INFO
WORKERS=4
CACHE_TTL=3600
METRICS_ENABLED=true
```

### Generating Secrets
```bash
# Generate SECRET_KEY
openssl rand -hex 32

# Generate API key
openssl rand -base64 32

# Generate JWT secret
openssl rand -hex 64
```

## Database Configuration

### Initial Setup
```sql
-- Create database
CREATE DATABASE servicedb;

-- Create user
CREATE USER serviceuser WITH PASSWORD 'secure-password';

-- Grant permissions
GRANT ALL PRIVILEGES ON DATABASE servicedb TO serviceuser;

-- Create extensions (if needed)
\c servicedb
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
CREATE EXTENSION IF NOT EXISTS "pg_trgm";
```

### Connection Pool Settings
- **Minimum connections:** 5 (always available)
- **Maximum connections:** 20 (prevent exhaustion)
- **Idle timeout:** 300s (close unused connections)
- **Connection lifetime:** 1800s (rotate connections)

## LDAP Integration

### Directory Structure
dc=lab,dc=local
├── ou=users
│   ├── uid=admin
│   ├── uid=user1
│   └── uid=user2
├── ou=groups
│   ├── cn=admins
│   ├── cn=users
│   └── cn=readonly
└── ou=services
└── cn=service

### User Attributes Mapping
- **Username:** `uid`
- **Email:** `mail`
- **Display Name:** `displayName`
- **Groups:** `memberOf`

### Testing LDAP Connection
```bash
# Test bind
ldapsearch -x -H ldap://ldap.lab.local:389 \
  -D "cn=service,ou=services,dc=lab,dc=local" \
  -w "password" -b "dc=lab,dc=local"

# Search for user
ldapsearch -x -H ldap://ldap.lab.local:389 \
  -D "cn=service,ou=services,dc=lab,dc=local" \
  -w "password" -b "ou=users,dc=lab,dc=local" \
  "(uid=admin)"
```

## Nginx Reverse Proxy

### Full Configuration
```nginx
upstream service_backend {
    server 192.168.1.50:8080 max_fails=3 fail_timeout=30s;
    keepalive 32;
}

server {
    listen 80;
    server_name service.lab.local;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name service.lab.local;

    # SSL Configuration
    ssl_certificate /etc/ssl/certs/service.lab.local.crt;
    ssl_certificate_key /etc/ssl/private/service.lab.local.key;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;

    # Security Headers
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;

    # Logging
    access_log /var/log/nginx/service.access.log;
    error_log /var/log/nginx/service.error.log;

    # Client limits
    client_max_body_size 100M;
    client_body_timeout 120s;

    location / {
        proxy_pass http://service_backend;
        proxy_http_version 1.1;
        
        # Headers
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_set_header Connection "";
        
        # Timeouts
        proxy_connect_timeout 60s;
        proxy_send_timeout 60s;
        proxy_read_timeout 60s;
        
        # Buffering
        proxy_buffering off;
    }

    # Metrics endpoint (internal only)
    location /metrics {
        proxy_pass http://192.168.1.50:9090;
        allow 192.168.1.0/24;
        deny all;
    }

    # Health check (internal only)
    location /health {
        proxy_pass http://192.168.1.50:8080;
        allow 192.168.1.0/24;
        deny all;
        access_log off;
    }
}
```

## Performance Tuning

### Application Settings
```yaml
# For high-load scenarios
server:
  workers: 8  # 2x CPU cores
  worker_connections: 1024
  keepalive_timeout: 65

database:
  pool:
    min: 10
    max: 50
    
cache:
  enabled: true
  ttl: 7200
```

### System-Level Optimizations
```bash
# Increase file descriptors
ulimit -n 65535

# Kernel parameters (add to /etc/sysctl.conf)
net.core.somaxconn = 1024
net.ipv4.tcp_max_syn_backlog = 2048
net.ipv4.ip_local_port_range = 10000 65535
```

## Security Configuration

### SSL/TLS Certificates
```bash
# Generate self-signed (testing only)
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/ssl/private/service.lab.local.key \
  -out /etc/ssl/certs/service.lab.local.crt

# Using Let's Encrypt (production)
certbot certonly --nginx -d service.lab.local
```

### Firewall Rules
```bash
# Allow HTTP/HTTPS
ufw allow 80/tcp
ufw allow 443/tcp

# Allow from specific IPs only (more secure)
ufw allow from 192.168.1.0/24 to any port 8080
```

### Rate Limiting (in Nginx)
```nginx
# Add to http block
limit_req_zone $binary_remote_addr zone=service_limit:10m rate=10r/s;

# Add to location block
limit_req zone=service_limit burst=20 nodelay;
```

## Configuration Validation

### Test Configuration
```bash
# Validate YAML syntax
python3 -c "import yaml; yaml.safe_load(open('/opt/services/service-name/config/config.yaml'))"

# Test with dry-run
docker-compose config

# Nginx config test
nginx -t
```

### Common Configuration Errors
1. **Invalid YAML syntax** - Check indentation
2. **Missing environment variables** - Verify .env file
3. **Wrong database credentials** - Test connection manually
4. **Certificate path errors** - Verify file permissions

## Configuration Best Practices

1. **Never commit secrets** - Use environment variables
2. **Use configuration validation** - Test before deployment
3. **Document all changes** - Keep this file updated
4. **Use sensible defaults** - Make it work out of the box
5. **Implement configuration versioning** - Track changes in git
6. **Test configuration changes** - Always in staging first

Template: Service Troubleshooting
File: services/[service-name]/troubleshooting.md
## LDAP Integration

### Directory Structure
