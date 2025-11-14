# [Service Name] - Troubleshooting Guide

**Last Updated:** 2024-11-13

## Quick Diagnostics

### Check Service Status
```bash
# Docker status
docker ps | grep service-name

# View logs (last 100 lines)
docker logs --tail 100 service-name

# Follow logs in real-time
docker logs -f service-name

# Check resource usage
docker stats service-name
```

### Health Check
```bash
# Internal health check
curl http://localhost:8080/health

# External health check
curl https://service.lab.local/health

# Expected response:
# {"status": "healthy", "version": "1.0.0", "uptime": 86400}
```

## Common Issues

### 1. Service Won't Start

#### Symptoms
- Container exits immediately after starting
- Status shows "Exited (1)" or "Restarting"

#### Diagnosis
```bash
# Check last 50 log lines for errors
docker logs --tail 50 service-name

# Inspect container
docker inspect service-name

# Check exit code
docker inspect service-name --format='{{.State.ExitCode}}'
```

#### Common Causes & Solutions

**Database Connection Failure**
```bash
# Test database connectivity
docker exec -it service-name ping database.lab.local

# Test database login
docker exec -it service-name psql -h database.lab.local -U serviceuser -d servicedb

# Solutions:
# 1. Verify DATABASE_URL in .env
# 2. Check database is running: systemctl status postgresql
# 3. Verify firewall allows connection
# 4. Check database user permissions
```

**Missing Environment Variables**
```bash
# Check what env vars are set
docker exec -it service-name env | grep SERVICE

# Solution: Add missing variables to .env file
```

**Port Already in Use**
```bash
# Check what's using the port
netstat -tulpn | grep 8080
lsof -i :8080

# Solution: Stop conflicting service or change port
```

**Permission Issues**
```bash
# Check volume mount permissions
ls -la /mnt/storage/service-data

# Solution: Fix permissions
chown -R 1000:1000 /mnt/storage/service-data
chmod -R 755 /mnt/storage/service-data
```

### 2. Service Running But Unreachable

#### Symptoms
- Container is running but can't access via browser
- Connection timeout or refused errors

#### Diagnosis
```bash
# Test from localhost
curl http://localhost:8080

# Test from local network
curl http://192.168.1.50:8080

# Test through reverse proxy
curl https://service.lab.local

# Check if service is listening
docker exec -it service-name netstat -tulpn
```

#### Solutions

**Firewall Blocking**
```bash
# Check firewall status
ufw status
iptables -L -n

# Allow port
ufw allow 8080/tcp
```

**Nginx Reverse Proxy Issues**
```bash
# Test nginx config
nginx -t

# Check nginx logs
tail -f /var/log/nginx/error.log

# Reload nginx
systemctl reload nginx
```

**DNS Issues**
```bash
# Test DNS resolution
nslookup service.lab.local
dig service.lab.local

# Solution: Add to /etc/hosts or fix DNS server
echo "192.168.1.50 service.lab.local" >> /etc/hosts
```

**Wrong Network Configuration**
```bash
# Check Docker networks
docker network ls
docker network inspect services

# Reconnect to network
docker network connect services service-name
```

### 3. High CPU/Memory Usage

#### Symptoms
- Service slow or unresponsive
- High resource consumption

#### Diagnosis
```bash
# Check resource usage
docker stats service-name

# Check processes inside container
docker exec -it service-name top

# Check application logs for errors
docker logs service-name | grep -i error
```

#### Solutions

**Memory Leak**
```bash
# Restart service (temporary fix)
docker restart service-name

# Long-term: Update to patched version
# Check application metrics for memory growth pattern
```

**Too Many Workers**
```yaml
# Reduce workers in config.yaml
server:
  workers: 2  # Reduce from 4 to 2
```

**Database Connection Pool Exhaustion**
```yaml
# Adjust pool settings
database:
  pool:
    max: 10  # Reduce from 20
```

**Runaway Background Job**
```bash
# Check job queue
docker exec -it service-name redis-cli LLEN job_queue

# Clear stuck jobs (use with caution)
docker exec -it service-name redis-cli FLUSHDB
```

### 4. Authentication Issues

#### Symptoms
- Can't log in
- "Invalid credentials" errors
- LDAP timeouts

#### Diagnosis
```bash
# Test LDAP connection
ldapsearch -x -H ldap://ldap.lab.local:389 \
  -D "cn=service,ou=services,dc=lab,dc=local" \
  -w "password" -b "dc=lab,dc=local"

# Check auth logs
docker logs service-name | grep -i auth
```

#### Solutions

**LDAP Server Unreachable**
```bash
# Test connectivity
docker exec -it service-name ping ldap.lab.local
docker exec -it service-name telnet ldap.lab.local 389

# Check LDAP server is running
systemctl status slapd
```

**Wrong LDAP Credentials**
```bash
# Verify bind DN and password in config
docker exec -it service-name cat /config/config.yaml | grep ldap

# Test with ldapsearch
ldapsearch -x -H ldap://ldap.lab.local:389 \
  -D "cn=service,ou=services,dc=lab,dc=local" \
  -w "correct-password" -b "dc=lab,dc=local"
```

**User Not in Correct Group**
```bash
# Check user's group membership
ldapsearch -x -H ldap://ldap.lab.local:389 \
  -D "cn=admin,dc=lab,dc=local" \
  -w "password" -b "ou=users,dc=lab,dc=local" \
  "(uid=username)" memberOf

# Add user to group (if needed)
ldapmodify -x -D "cn=admin,dc=lab,dc=local" -w "password" <<EOF
dn: cn=service-users,ou=groups,dc=lab,dc=local
changetype: modify
add: member
member: uid=username,ou=users,dc=lab,dc=local
EOF
```

**Session/Token Issues**
```bash
# Clear sessions (Redis-based)
docker exec -it service-name redis-cli FLUSHDB

# Clear browser cookies and try again
```

### 5. Database Issues

#### Symptoms
- Slow queries
- Connection timeouts
- "Too many connections" errors

#### Diagnosis
```bash
# Check database connections
docker exec -it database psql -U postgres -c "SELECT count(*) FROM pg_stat_activity;"

# Check slow queries
docker exec -it database psql -U postgres -c "SELECT query, state, wait_event FROM pg_stat_activity WHERE state != 'idle';"

# Check database size
docker exec -it database psql -U postgres -c "SELECT pg_size_pretty(pg_database_size('servicedb'));"
```

#### Solutions

**Connection Pool Exhausted**
```yaml
# Increase pool size in config.yaml
database:
  pool:
    min: 10
    max: 30  # Increased from 20
```

**Database Locks**
```sql
-- Find blocking queries
SELECT 
    blocked_locks.pid AS blocked_pid,
    blocking_locks.pid AS blocking_pid,
    blocked_activity.query AS blocked_statement
FROM pg_catalog.pg_locks blocked_locks
JOIN pg_catalog.pg_stat_activity blocked_activity ON blocked_activity.pid = blocked_locks.pid
JOIN pg_catalog.pg_locks blocking_locks ON blocking_locks.locktype = blocked_locks.locktype
WHERE NOT blocked_locks.granted;

-- Kill blocking query (use carefully)
SELECT pg_terminate_backend(blocking_pid);
```

**Database Disk Full**
```bash
# Check disk usage
df -h /var/lib/postgresql

# Clean up old data/logs
docker exec -it database vacuumdb -U postgres --all --analyze
```

### 6. Performance Issues

#### Symptoms
- Slow page loads
- High response times
- Timeouts

#### Diagnosis
```bash
# Check response times
time curl -o /dev/null -s -w '%{time_total}\n' https://service.lab.local

# Monitor with Apache Bench
ab -n 100 -c 10 https://service.lab.local/

# Check metrics
curl http://192.168.1.50:9090/metrics | grep response_time
```

#### Solutions

**Enable Caching**
```yaml
# Add/enable cache in config.yaml
cache:
  enabled: true
  type: redis
  host: redis.lab.local
  ttl: 3600
```

**Optimize Database Queries**
```sql
-- Find slow queries
SELECT query, mean_exec_time, calls
FROM pg_stat_statements
ORDER BY mean_exec_time DESC
LIMIT 10;

-- Add missing indexes
CREATE INDEX idx_table_column ON table_name(column_name);
```

**Increase Resources**
```yaml
# Update docker-compose.yml
services:
  service:
    deploy:
      resources:
        limits:
          cpus: '4'  # Increased from 2
          memory: 8G  # Increased from 4G
```

### 7. Storage Issues

#### Symptoms
- "Disk full" errors
- Can't upload files
- Backup failures

#### Diagnosis
```bash
# Check disk usage
df -h /mnt/storage/service-data
du -sh /mnt/storage/service-data/*

# Find large files
find /mnt/storage/service-data -type f -size +100M -exec ls -lh {} \;

# Check inode usage
df -i /mnt/storage/service-data
```

#### Solutions

**Clean Up Old Data**
```bash
# Delete old logs
find /var/log/service -name "*.log" -mtime +30 -delete

# Clean temporary files
docker exec -it service-name rm -rf /tmp/*

# Compress old backups
gzip /mnt/backup/service/*.tar
```

**Increase Storage**
```bash
# Extend volume (Proxmox)
qm resize 103 scsi1 +50G

# Resize filesystem (inside VM)
resize2fs /dev/sdb1
```

**Move to Larger Volume**
```bash
# Stop service
docker-compose down

# Copy data
rsync -av /mnt/storage/service-data/ /mnt/new-storage/service-data/

# Update docker-compose.yml
# Change volume mount path

# Start service
docker-compose up -d
```

### 8. Certificate/SSL Issues

#### Symptoms
- Browser shows "Not secure" warning
- Certificate expired errors
- SSL handshake failures

#### Diagnosis
```bash
# Check certificate expiration
openssl s_client -connect service.lab.local:443 -servername service.lab.local </dev/null 2>/dev/null | openssl x509 -noout -dates

# Test SSL configuration
curl -vI https://service.lab.local

# Check certificate chain
openssl s_client -showcerts -connect service.lab.local:443 -servername service.lab.local
```

#### Solutions

**Renew Certificate**
```bash
# Let's Encrypt renewal
certbot renew --nginx

# Or manually
certbot certonly --nginx -d service.lab.local

# Reload nginx
systemctl reload nginx
```

**Self-Signed Certificate (Testing)**
```bash
# Generate new certificate
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
  -keyout /etc/ssl/private/service.lab.local.key \
  -out /etc/ssl/certs/service.lab.local.crt \
  -subj "/CN=service.lab.local"

# Restart nginx
systemctl restart nginx
```

**Fix Certificate Permissions**
```bash
chmod 644 /etc/ssl/certs/service.lab.local.crt
chmod 600 /etc/ssl/private/service.lab.local.key
chown root:root /etc/ssl/certs/service.lab.local.crt
chown root:root /etc/ssl/private/service.lab.local.key
```

### 9. Backup/Restore Issues

#### Symptoms
- Backup script fails
- Restore doesn't work
- Corrupt backup files

#### Diagnosis
```bash
# Check backup logs
tail -f /var/log/backup-service.log

# Verify backup file integrity
tar -tzf /mnt/backup/service/backup-latest.tar.gz > /dev/null

# Check backup size
ls -lh /mnt/backup/service/
```

#### Solutions

**Backup Script Failing**
```bash
# Run manually to see error
/opt/services/service-name/backup.sh

# Common fixes:
# - Check disk space: df -h
# - Verify permissions: ls -la /mnt/backup/service/
# - Test database access: docker exec -it service-name pg_dump
```

**Restore Failing**
```bash
# Verify backup file
tar -tzf backup.tar.gz | head

# Try manual restore
tar -xzf backup.tar.gz -C /tmp/restore-test/
# Inspect contents, then copy to correct location
```

**Automated Backups Not Running**
```bash
# Check crontab
crontab -l | grep backup

# Check cron service
systemctl status cron

# View cron logs
grep CRON /var/log/syslog | grep backup
```

## Diagnostic Commands Reference

### Container Management
```bash
# View all containers
docker ps -a

# Inspect container details
docker inspect service-name

# View container resource usage
docker stats service-name

# Execute command in container
docker exec -it service-name bash

# Copy files from container
docker cp service-name:/path/to/file /local/path
```

### Network Troubleshooting
```bash
# Test connectivity
ping service.lab.local
traceroute service.lab.local
mtr service.lab.local

# Check DNS
nslookup service.lab.local
dig service.lab.local

# Check port
telnet service.lab.local 443
nc -zv service.lab.local 443

# Check what's listening
netstat -tulpn | grep 8080
ss -tulpn | grep 8080
```

### Log Analysis
```bash
# Search logs for errors
docker logs service-name 2>&1 | grep -i error

# Filter by time
docker logs --since 1h service-name

# Save logs to file
docker logs service-name > service.log 2>&1

# Follow multiple containers
docker-compose logs -f
```

### Performance Analysis
```bash
# CPU usage
top -p $(docker inspect -f '{{.State.Pid}}' service-name)

# Memory usage
docker stats service-name --no-stream

# Disk I/O
iotop -p $(docker inspect -f '{{.State.Pid}}' service-name)

# Network traffic
iftop -i docker0
```

## Emergency Procedures

### Service Completely Down

1. **Immediate Steps**
```bash
   # Check if container is running
   docker ps -a | grep service-name
   
   # Try restart
   docker restart service-name
   
   # If that fails, check logs
   docker logs --tail 100 service-name
```

2. **If Restart Doesn't Work**
```bash
   # Stop service
   docker-compose down
   
   # Check for zombie processes
   ps aux | grep service
   
   # Remove container
   docker rm -f service-name
   
   # Recreate from backup/snapshot
   git checkout last-known-good-version
   docker-compose up -d
```

3. **Nuclear Option - Full Rebuild**
```bash
   # Backup data first!
   tar -czf /tmp/service-backup.tar.gz /mnt/storage/service-data
   
   # Remove everything
   docker-compose down -v
   docker rmi service-image
   
   # Redeploy fresh
   docker-compose pull
   docker-compose up -d
   
   # Restore data if needed
   tar -xzf /tmp/service-backup.tar.gz -C /
```

### Data Corruption

1. **Stop service immediately**
```bash
   docker-compose down
```

2. **Assess damage**
```bash
   # Check database integrity
   docker exec -it database pg_dump -U postgres servicedb > /dev/null
   
   # Check file system
   fsck /dev/sdb1
```

3. **Restore from backup**
```bash
   # Find latest good backup
   ls -ltr /mnt/backup/service/
   
   # Restore
   ./restore.sh /mnt/backup/service/backup-YYYY-MM-DD.tar.gz
   
   # Start service
   docker-compose up -d
```

## Getting Help

### Before Asking for Help

Collect this information:
```bash
# System info
uname -a
docker version
docker-compose version

# Service info
docker ps -a | grep service
docker logs --tail 50 service-name
docker inspect service-name

# Resource usage
df -h
free -h
docker stats --no-stream

# Recent changes
git log --oneline -10
```

### Where to Get Help

1. **Check documentation**
   - Service README
   - Official documentation
   - This troubleshooting guide

2. **Search existing issues**
   - GitHub issues for the service
   - HomeLab documentation incidents
   - Community forums

3. **Ask in communities**
   - r/homelab on Reddit
   - HomeLab Discord servers
   - Service-specific forums

4. **Create detailed issue report**
   - Symptoms and when they started
   - Steps to reproduce
   - Error messages and logs
   - What you've tried already
   - System information

## Preventive Measures

### Monitoring Setup
```bash
# Install monitoring stack
# See: [[procedures/monitoring-setup]]

# Set up alerts for:
# - Service down
# - High CPU/memory
# - Disk space low
# - Certificate expiring
# - Backup failures
```

### Regular Health Checks
```bash
# Weekly script
#!/bin/bash
# Check service health
curl -f http://localhost:8080/health || echo "Service health check failed"

# Check disk space
df -h /mnt/storage/service-data | awk '$5+0 > 80 {print "Disk space critical"}'

# Check backups
find /mnt/backup/service -name "*.tar.gz" -mtime -1 | grep -q . || echo "No recent backup"

# Check logs for errors
docker logs --since 24h service-name 2>&1 | grep -i error && echo "Errors in logs"
```

### Documentation
- Keep this guide updated with new issues
- Document all incidents in `incidents/`
- Update configuration after changes
- Review quarterly and remove outdated info

## Related Documentation
- [[services/service-name/README|Service Overview]]
- [[services/service-name/deployment|Deployment Guide]]
- [[services/service-name/configuration|Configuration Reference]]
- [[procedures/disaster-recovery|Disaster Recovery Procedures]]
- [[incidents/|Past Incidents]]
