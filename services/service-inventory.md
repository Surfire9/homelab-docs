Service Inventory  
Last Updated: 2024-11-15 
Total Services: 3  
Active Services: 3 🟢

---

## Quick Stats
- **Infrastructure Services:** 2  
- **Application Services:** 1  
- **Media Services:** 0  
- **Monitoring Services:** 0  

---

# Services Overview

## Infrastructure Services
| Service      | Status | Host               | Purpose                     | Links |
|--------------|--------|-------------------|-----------------------------|-------|
| Proxmox VE   | 🟢     | proxmox.lab.local | Hypervisor / VM management  | Docs  |
| TrueNAS Scale| 🟢     | truenas.lab.local | Network storage (ZFS)       | Docs  |

---

## Application Services
| Service            | Status | Host                      | Purpose                     | Links |
|--------------------|--------|---------------------------|-----------------------------|-------|
| Home Automation    | 🟢     | homeautomation.lab.local  | Home automation platform    | Docs  |

---

## Media Services
| Service | Status | Host | Purpose | Links |
|---------|--------|------|---------|-------|
| (Add services here) |

---

## Monitoring Services
| Service | Status | Host | Purpose | Links |
|---------|--------|------|---------|-------|
| (Add services here) |

---

# Service Details

## TrueNAS Scale
- **URL:** https://truenas.lab.local  
- **Type:** Storage / Infrastructure  
- **Platform:** VM (Proxmox VM 103)  
- **Resources:** 4 vCPU, 14GB RAM, 32GB boot + 50GB media  
- **Purpose:** Primary network storage with ZFS for media files  
- **Dependencies:** None (infrastructure)  
- **Access:** Web UI, SSH  
- **Backup:** VM snapshots  
- **Notes:** Using WWN for disk identification (0x5000000000000001, 0x5000000000000002)

---

## Proxmox VE
- **URL:** https://proxmox.lab.local:8006  
- **Type:** Hypervisor / Infrastructure  
- **Platform:** Bare metal  
- **Resources:** Full server hardware  
- **Purpose:** Virtual machine and container management  
- **Dependencies:** None (base infrastructure)  
- **Access:** Web UI (port 8006), SSH  
- **Backup:** Configuration backup  
- **Notes:** Main hypervisor, hosts TrueNAS VM and Home Automation VM

---

## Home Automation Service
- **URL:** https://homeautomation.lab.local:8123  
- **Type:** Application Service / Home Automation  
- **Platform:** HomeAssistantOS VM (Proxmox VM [ID])  
- **Resources:** [Fill In — vCPU, RAM, Disk]  
- **Purpose:** Centralized automation platform for IoT devices, routines, scenes, and integrations  
- **Dependencies:**  
  - Proxmox Virtualization Platform  
  - HomeAutomationOS Software Platform  
  - IoT VLAN (if applicable)  
- **Access:** Web UI (8123), Tailscale (if configured)  
- **Backup:** Home Assistant Snapshots + Proxmox Backup Jobs  
- **Notes:** Hosts all home automation logic; integrates Zigbee, Wi-Fi, Matter, and add-on modules

---

# Service URLs Quick Reference

## Infrastructure
- 🖥️ **Proxmox:** https://proxmox.lab.local:8006  
- 💾 **Storage (TrueNAS):** https://truenas.lab.local  

## Applications
- 🏠 **Home Automation:** https://homeautomation.lab.local:8123  

## Media
(Add media service URLs here)

## Monitoring
(Add monitoring URLs here)

---

# Resource Allocation

## By Host

### Primary Server
- Proxmox overhead: **4GB RAM, 2 cores**  
- TrueNAS VM: **14GB RAM, 4 cores**  
- Home Automation VM: **[Fill In RAM and cores]**  
- Available: *(Calculate remaining resources)*  

---

## By Service
| Service            | CPU      | RAM     | Storage         |
|--------------------|----------|---------|-----------------|
| Proxmox            | 2 cores  | 4GB     | System disk     |
| TrueNAS            | 4 cores  | 14GB    | 82GB total      |
| Home Automation    | [Fill]   | [Fill]  | [Fill]          |
| (Add more services) |

---

# Access Matrix

| Service          | Internal URL                        | External Access | Auth Method | Port |
|------------------|--------------------------------------|----------------|-------------|------|
| Proxmox          | https://proxmox.lab.local:8006       | VPN only       | Local auth  | 8006 |
| TrueNAS          | https://truenas.lab.local            | VPN only       | Local auth  | 443  |
| Home Automation  | https://homeautomation.lab.local:8123| VPN/Tailscale  | Local auth  | 8123 |
| (Add services)   |                                      |                |             |      |

---

# Planned Services

## Short Term (Next Month)
- Media server – Deploy Jellyfin or Plex  
- Monitoring service – Deploy Zabbix or Uptime Kuma  

## Medium Term (Next Quarter)
- Identity service – Deploy Authentik  
- Remote Access Gateway – Harden Tailscale + multi-factor  

## Long Term (Future)
- Security SIEM lab  
- Additional storage node  
- Kubernetes / Docker cluster (optional)

---

# Deprecated Services
| Service | Deprecated Date | Reason | Replacement |
|---------|------------------|--------|-------------|
| (Services you've retired) |

---

# Maintenance Schedule

### Daily (Automated)
- Health checks  
- Backup verification  

### Weekly (Manual)
- Review service logs  
- Check disk space  
- Update inventory if services change  

### Monthly (Manual)
- Security updates  
- Test backup restore (random service)  
- Check resource consumption  

### Quarterly (Manual)
- Full system audit  
- Update documentation  
- Capacity planning  

---

# Notes

### Adding a New Service
1. Create folder: `services/[service-name]/`  
2. Add docs (README.md, deployment.md, etc.)  
3. Add entry to this inventory  
4. Update resource allocation  
5. Add to backups/monitoring  

### Removing a Service
1. Export data  
2. Move to Deprecated section  
3. Remove from monitoring/backups  
4. Delete VM/container  
5. Clean firewall + DNS  

---

# Service Health Legend
- 🟢 **Production** – Stable, active  
- 🟡 **Testing** – In evaluation  
- 🔴 **Deprecated** – Phased out  
- ⚪ **Planned** – Not yet deployed  
- 🔵 **Maintenance** – Temporarily offline  

---

# Related Documentation
- Hardware Inventory  
- Network Topology  
- Backup Procedures  
- Incident Reports  
