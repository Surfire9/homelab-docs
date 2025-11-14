# HomeLab Documentation

**Last Updated:** 2025-11-13
**Maintained By:** Michael
**Lab Name:** OpenSource Enterprise Home Lab

## Overview
Personal homelab for learning, development, and self-hosted services. This documentation serves as both operational reference and portfolio demonstration.

## Quick Links
- 🏠 [Network Topology](infrastructure/network/topology.md)
- 🖥️ [Hardware Inventory](infrastructure/hardware/servers.md)
- 📦 [Service Inventory](services/service-inventory.md)
- 📋 [Procedures](procedures/)
- 🔥 [Recent Incidents](incidents/)

## Lab Statistics
- **Physical Servers:** 1
- **Virtual Machines:** 5
- **Containers:** 0
- **Total Storage:** 1.5 TB
- **Services Running:** TrueNAS, pfSense, Wazuh
- **Uptime:** Pending Full Stable% (30-day average)

## Infrastructure Overview

### Network
- **Public IP:** Dynamic (DDNS configured)
- **Internal Network:** 192.168.10.0/24
- **VLANs:** Pending
- **Internet:** 1Gbps fiber

### Compute
- **Hypervisor:** Proxmox VE 8.x
- **Total CPU:** 8 cores / 16 threads
- **Total RAM:** 32GB ECC
- **Clustering:** 2-node HA cluster

### Storage
- **Primary:** TrueNAS Scale (ZFS)
- **Capacity:** 50GBT usable (RAIDZ1)
- **Backup:** Pending

## Key Services
- **Authentication:** Pending (Authentik (SSO), 
- **Monitoring:** Pending (Prometheus + Grafana,
- **DNS:** Pending (Pi-hole + Unbound,
- **Reverse Proxy:** Pending (Traefik
- **Media:** Pending (Plex, *arr stack
- **Automation:** Pending (Home Assistant, Ansible
- **Development:** GitLab, Pending (Docker Registry

## Documentation Standards

### File Naming
- Use kebab-case: `my-service-name.md`
- Date format: `YYYY-MM-DD-description.md`
- No spaces in filenames

### Obsidian Links
Use WikiLinks for cross-referencing:
```markdown
[[services/plex/README|Plex Service]]
[[infrastructure/network/topology]]
```

### Status Indicators
- 🟢 Production / Healthy
- 🟡 Testing / Warning
- 🔴 Deprecated / Critical
- ⚪ Planned / Offline

### Git Commit Messages
Follow conventional commits:
- `feat: Add new service documentation`
- `fix: Correct IP address in network docs`
- `docs: Update backup procedures`
- `chore: Reorganize directory structure`

## Portfolio Highlights

### Skills Demonstrated
- **Virtualization:** Proxmox, KVM, LXC containers
- **Networking:** VLANs, firewall rules, VPN, DNS
- **Storage:** ZFS, NFS, iSCSI, backup strategies
- **Automation:** Ansible, Docker Compose, CI/CD
- **Monitoring:** Wazuh, Prometheus, Grafana, alerting
- **Security:** SSO, certificates, network segmentation

### Notable Projects
1. **Self-Hosted CI/CD** - GitLab with automated testing and deployment

## Getting Started

### For Visitors
This documentation demonstrates my homelab setup and technical capabilities. 
Browse the [Service Inventory](services/service-inventory.md) to see what I'm running, or check [Architecture Decisions](architecture/decisions.md) to understand my technical choices.

### For Future Me
Common tasks:
- Adding new service: Use template in `services/`
- Network change: Update [Network Topology](infrastructure/network/topology.md)
- Incident: Create new file in `incidents/`
- Hardware change: Update [Hardware Inventory](infrastructure/hardware/)

## Roadmap
- [ ] Automated disaster recovery planning
- [ ] Add GPU passthrough for AI/ML workloads
- [ ] Implement automated documentation updates
- [ ] Kubernetes

## Contact & Links
- **Email:** mj76652@email.vccs.edu
- **GitHub:** github.com/yourusername
- **LinkedIn:** linkedin.com/in/michael-johnson-

---

**Note:** Sensitive information (passwords, API keys, IP addresses) has been redacted or replaced with examples. Actual credentials stored in encrypted vault.
