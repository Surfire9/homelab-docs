## Configuration

### Environment Variables
```bash
SERVICE_PORT=8080
DATABASE_URL=postgresql://user:pass@db:5432/service
API_KEY=stored-in-vault
```

### Key Config Files
- `/etc/service/config.yaml` - Main configuration
- `/etc/service/nginx.conf` - Reverse proxy config

## Monitoring & Alerts
- **Health Check:** https://service.lab.local/health
- **Metrics Endpoint:** http://192.168.1.50:9090/metrics
- **Alert Conditions:**
  - Service down > 2 minutes
  - CPU > 80% for 5 minutes
  - Disk usage > 90%

## Backup & Recovery

### Backup Process
```bash
# Automated backup command
./backup-service.sh
```
- **Frequency:** Daily at 2:00 AM
- **Retention:** 30 days local, 90 days offsite
- **Last Successful Backup:** 2024-11-13 02:15 AM

### Recovery Procedure
1. Stop service: `docker-compose down`
2. Restore data: `./restore.sh backup-2024-11-13.tar.gz`
3. Start service: `docker-compose up -d`
4. Verify: Check health endpoint
