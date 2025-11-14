# Service Inventory

**Last Updated:** 2024-11-13 
**Total Services:** 2 
**Active Services:** 2 🟢

---

## Quick Stats

- **Infrastructure Services:** 2
- **Application Services:** 0
- **Media Services:** 0
- **Monitoring Services:** 0

---

## Services Overview

### Infrastructure Services

| Service | Status | Host | Purpose | Links |
|---------|--------|------|---------|-------|
| Proxmox VE | 🟢 | proxmox.lab.local | Hypervisor / VM management | [Docs](../infrastructure/hypervisors/proxmox.md) |
| TrueNAS Scale | 🟢 | truenas.lab.local | Network storage (ZFS) | [Docs](truenas/README.md) |

### Application Services

| Service | Status | Host | Purpose | Links |
|---------|--------|------|---------|-------|
| (Add services here) |  |  |  |  |

### Media Services

| Service | Status | Host | Purpose | Links |
|---------|--------|------|---------|-------|
| (Add services here) |  |  |  |  |

### Monitoring Services

| Service | Status | Host | Purpose | Links |
|---------|--------|------|---------|-------|
| (Add services here) |  |  |  |  |

---

## Service Details

### TrueNAS Scale
- **URL:** https://truenas.lab.local
- **Type:** Storage / Infrastructure
- **Platform:** VM (Proxmox VM 103)
- **Resources:** 4 vCPU, 14GB RAM, 32GB boot + 50GB media
- **Purpose:** Primary network storage with ZFS for media files
- **Dependencies:** None (infrastructure)
- **Access:** Web UI, SSH
- **Backup:** VM snapshots
- **Notes:** Using WWN for disk identification (0x5000000000000001, 0x5000000000000002)

### Proxmox VE
- **URL:** https://proxmox.lab.local:8006
- **Type:** Hypervisor / Infrastructure
- **Platform:** Bare metal
- **Resources:** Full server hardware
- **Purpose:** Virtual machine and container management
- **Dependencies:** None (base infrastructure)
- **Access:** Web UI (port 8006), SSH
- **Backup:** Configuration backup
- **Notes:** Main hypervisor, hosts TrueNAS VM

---

## Service URLs Quick Reference

### Infrastructure
- 🖥️ Proxmox: https://proxmox.lab.local:8006
- 💾 Storage: https://truenas.lab.local

### Applications
- (Add application URLs here)

### Media
- (Add media service URLs here)

### Monitoring
- (Add monitoring URLs here)

---

## Resource Allocation

### By Host

**Primary Server**
- Proxmox overhead: 4GB RAM, 2 cores
- TrueNAS VM: 14GB RAM, 4 cores
- Available: (Calculate remaining resources)

### By Service

| Service | CPU | RAM | Storage |
|---------|-----|-----|---------|
| Proxmox | 2 cores | 4GB | System disk |
| TrueNAS | 4 cores | 14GB | 82GB total |
| (Add more services) |  |  |  |

---

## Access Matrix

| Service | Internal URL | External Access | Auth Method | Port |
|---------|--------------|-----------------|-------------|------|
| Proxmox | https://proxmox.lab.local:8006 | VPN only | Local auth | 8006 |
| TrueNAS | https://truenas.lab.local | VPN only | Local auth | 443 |
| (Add services) |  |  |  |  |

---

## Planned Services

### Short Term (Next Month)
- [ ] Service name - Brief description
- [ ] Service name - Brief description

### Medium Term (Next Quarter)
- [ ] Service name - Brief description
- [ ] Service name - Brief description

### Long Term (Future)
- [ ] Service name - Brief description
- [ ] Service name - Brief description

---

## Deprecated Services

| Service | Deprecated Date | Reason | Replacement |
|---------|-----------------|--------|-------------|
| (Services you've retired) |  |  |  |

---

## Maintenance Schedule

### Daily (Automated)
- Health checks for all services
- Backup verification

### Weekly (Manual)
- Review service logs for errors
- Check disk space on all hosts
- Update this inventory if changed

### Monthly (Manual)
- Security updates for all services
- Test backup restore (one random service)
- Review resource usage

### Quarterly (Manual)
- Full system review
- Update documentation
- Capacity planning

---

## Notes

### Adding a New Service
1. Create service directory: `services/[service-name]/`
2. Add service documentation (README.md, deployment.md, etc.)
3. Add entry to this inventory
4. Update resource allocation section
5. Add to monitoring/backups

### Removing a Service
1. Export any needed data
2. Move entry to "Deprecated Services" section
3. Remove from monitoring and backups
4. Stop and remove containers/VMs
5. Clean up firewall rules and DNS entries

### Service Health Legend
- 🟢 **Production** - Stable, in active use
- 🟡 **Testing** - Works but being evaluated
- 🔴 **Deprecated** - Being phased out
- ⚪ **Planned** - Not yet deployed
- 🔵 **Maintenance** - Temporarily offline for updates

---

## Related Documentation
- [Hardware Inventory](../infrastructure/hardware/servers.md)
- [Network Topology](../infrastructure/network/topology.md)
- [Backup Procedures](../procedures/backup-restore.md)
- [Incident Reports](../incidents/)
