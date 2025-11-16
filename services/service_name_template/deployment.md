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
