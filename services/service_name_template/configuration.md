# [Service Name] - Deployment Guide

**Last Updated:** 2024-11-13
**Deployment Method:** Docker Compose / VM / Bare Metal
**Estimated Time:** 30 minutes

## Prerequisites

### System Requirements
- Docker 24.0+ and Docker Compose 2.0+
- 4GB RAM minimum, 8GB recommended
- 50GB free disk space
- Network access to internal services

### Dependencies
Ensure these services are running:
- [ ] PostgreSQL database server
- [ ] LDAP authentication server
- [ ] Network file storage (NFS/CIFS)

### Accounts & Access
- [ ] Database user created with appropriate permissions
- [ ] LDAP service account configured
- [ ] SSL certificates obtained (or Let's Encrypt setup)
- [ ] Firewall rules configured

## Installation Steps

### 1. Prepare Host System
```bash
# Update system
apt update && apt upgrade -y

# Install required packages
apt install -y docker.io docker-compose git curl

# Create service user
useradd -r -s /bin/false service-user

# Create directory structure
mkdir -p /opt/services/service-name
mkdir -p /mnt/storage/service-data
mkdir -p /var/log/service
```

### 2. Clone and Configure
```bash
# Clone repository
cd /opt/services
git clone https://github.com/user/service-name.git
cd service-name

# Copy example config
cp .env.example .env

# Edit configuration
nano .env
```

### 3. Configure Environment

**File:** `.env`
```bash
# Service Configuration
SERVICE_PORT=8080
SERVICE_HOST=0.0.0.0

# Database
DATABASE_URL=postgresql://serviceuser:password@database.lab.local:5432/servicedb
DATABASE_POOL_SIZE=10

# Authentication
LDAP_URL=ldap://ldap.lab.local:389
LDAP_BIND_DN=cn=service,ou=services,dc=lab,dc=local
LDAP_BIND_PASSWORD=stored-in-vault

# Storage
DATA_PATH=/data
BACKUP_PATH=/backups

# Monitoring
METRICS_ENABLED=true
METRICS_PORT=9090

# Security
SECRET_KEY=generate-with-openssl-rand
API_KEY=stored-in-vault
```

### 4. Deploy with Docker Compose

**File:** `docker-compose.yml`
```yaml
version: '3.8'

services:
  service:
    image: service/service-name:latest
    container_name: service-name
    restart: unless-stopped
    environment:
      - SERVICE_PORT=${SERVICE_PORT}
      - DATABASE_URL=${DATABASE_URL}
      - LDAP_URL=${LDAP_URL}
    volumes:
      - /mnt/storage/service-data:/data
      - /var/log/service:/logs
      - ./config:/config:ro
    ports:
      - "8080:8080"
      - "9090:9090"
    networks:
      - services
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s

networks:
  services:
    external: true
```
```bash
# Start the service
docker-compose up -d

# View logs
docker-compose logs -f

# Check status
docker-compose ps
```

### 5. Post-Deployment Configuration
```bash
# Run initial setup script
docker exec -it service-name /app/setup.sh

# Create admin user
docker exec -it service-name /app/create-admin.sh

# Import initial data (if applicable)
docker exec -it service-name /app/import-data.sh
```

### 6. Configure Reverse Proxy

**Nginx Configuration:** `/etc/nginx/sites-available/service.lab.local`
```nginx
server {
    listen 80;
    server_name service.lab.local;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name service.lab.local;

    ssl_certificate /etc/ssl/certs/service.lab.local.crt;
    ssl_certificate_key /etc/ssl/private/service.lab.local.key;

    location / {
        proxy_pass http://192.168.1.50:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /metrics {
        proxy_pass http://192.168.1.50:9090;
        allow 192.168.1.0/24;
        deny all;
    }
}
```
```bash
# Enable site
ln -s /etc/nginx/sites-available/service.lab.local /etc/nginx/sites-enabled/
nginx -t
systemctl reload nginx
```

## Verification

### Health Checks
```bash
# Check service health
curl http://192.168.1.50:8080/health

# Check metrics endpoint
curl http://192.168.1.50:9090/metrics

# Test external access
curl https://service.lab.local

# Verify database connection
docker exec -it service-name psql -h database.lab.local -U serviceuser -d servicedb -c "SELECT 1;"
```

### Monitoring Setup
1. Add Prometheus scrape target
2. Import Grafana dashboard
3. Configure alerting rules
4. Test alert notifications

### Backup Configuration
```bash
# Test backup script
/opt/services/service-name/backup.sh

# Verify backup files created
ls -lh /mnt/backup/service/

# Add to cron
echo "0 2 * * * /opt/services/service-name/backup.sh" | crontab -
```

## Rollback Procedure

If deployment fails:
```bash
# Stop and remove containers
docker-compose down

# Restore previous version
git checkout previous-version
docker-compose up -d

# Or restore from backup
./restore.sh /mnt/backup/service/backup-latest.tar.gz
```

## Troubleshooting Deployment

### Container Won't Start
```bash
# Check logs
docker-compose logs service

# Common issues:
# - Database not accessible: Verify DATABASE_URL
# - Port already in use: Check with `netstat -tulpn | grep 8080`
# - Permission issues: Check volume mounts with `ls -la /mnt/storage/service-data`
```

### Can't Connect to Service
- Verify firewall rules: `ufw status`
- Check service is listening: `netstat -tulpn | grep 8080`
- Test from localhost first: `curl http://localhost:8080`
- Check reverse proxy logs: `tail -f /var/log/nginx/error.log`

## Next Steps
- [ ] Configure monitoring and alerting
- [ ] Set up automated backups
- [ ] Document in service inventory
- [ ] Add to uptime monitoring
- [ ] Schedule first maintenance window

Template: Service Configuration
File: services/[service-name]/configuration.md

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
