# Architecture Decision Records

## ADR-001: Use Proxmox for Virtualization (2025-10-15)

### Status
Accepted

### Context
Need a hypervisor platform for homelab. Options considered:
- Proxmox VE
- VMware ESXi
- XCP-ng
- KVM/libvirt

### Decision
Selected Proxmox VE for virtualization platform.

### Rationale
**Pros:**
- Open source and free
- Built-in clustering and HA
- Excellent web UI
- Strong community support
- Supports both VMs and containers (LXC)
- ZFS support for storage

**Cons:**
- Steeper learning curve than ESXi
- Smaller ecosystem than VMware

### Consequences
- All VMs will be managed through Proxmox
- Need to learn Proxmox-specific tooling
- Can leverage ZFS for storage efficiency
- Easy to expand cluster in future

### Alternatives Considered
- **ESXi:** Better enterprise support but limited free version
- **XCP-ng:** Similar to Proxmox but smaller community

---
# Architecture Decision Records

## ADR-002: Add Ubuntu Server to Home Lab Infrastructure (2025-10-27)

### Status
Accepted

### Context
- A dedicated Linux server is needed to support containerized workloads, automation services, monitoring tools, and security-focused labs.
- Current infrastructure includes Proxmox hosts, Windows system, and several service VMs.
- Adding an Ubuntu Server VM provides a stable, standardized environment for Linux-based services.
- Ubuntu Server LTS offers long-term support and excellent compatibility with automation and security tooling.

### Options Considered
- Deploy Ubuntu Server LTS as a VM inside Proxmox
- Use Debian, Rocky Linux, AlmaLinux
- Continue relying on existing mixed OS environments

### Decision
Deploy Ubuntu Server LTS as a VM within Proxmox with allocated resources based on the intended service role.

### Rationale
**Pros:**
- Stable, popular, and widely supported in enterprise environments
- Excellent package ecosystem and documentation
- Integrates cleanly with Proxmox (templates, snapshots, backups)
- Easier maintenance compared to some alternatives

**Cons:**
- Slight performance overhead due to virtualization
- Requires periodic updates and security maintenance
- Additional resource consumption on the host

### Consequences
- Standardized Linux base for automation and security tools
- Easier replication via Proxmox templates
- Improved isolation and organization of services
- Minor increase in Proxmox resource usage

### Alternatives Considered
- Debian
- Rocky Linux / AlmaLinux
- Bare-metal deployment for performance

---
# Architecture Decision Records

## ADR-003: Deploy pfSense as Primary Firewall & Network Segmentation Platform (2025-10-28)

### Status
Accepted

### Context
- The home lab needs a dedicated, enterprise-grade firewall to manage VLAN segmentation, IDS/IPS, VPN access, DNS filtering, and traffic shaping.
- Existing routers (ISP or consumer-grade) lack adequate visibility, logging, and granular security controls.
- A full-featured firewall is required to support lab environments, security research, remote access (Tailscale/OpenVPN), and isolation of test networks.

### Options Considered
- Deploy pfSense CE/Plus on dedicated hardware or as a Proxmox VM
- Deploy OPNsense
- Use a consumer router with third-party firmware (OpenWRT/DD-WRT)
- Continue using ISP-provided gateway/router

### Decision
Deploy pfSense as the primary firewall on dedicated hardware (or virtualized with proper NIC passthrough) to enable network segmentation, monitoring, and advanced security controls.

### Rationale
**Pros:**
- Mature, stable, and widely adopted firewall platform
- Strong VLAN support for segmentation of production, lab, IoT, DMZ, and testing networks
- Built-in VPN support (WireGuard, OpenVPN, IPsec)
- IDS/IPS options (Snort or Suricata)
- Advanced NAT, firewall rules, DHCP, DNS Resolver/Forwarder, and traffic shaping
- Works well with Proxmox via virtIO or NIC passthrough
- Extensive official documentation and long-term support

**Cons:**
- Steeper learning curve for advanced configurations
- Requires multiple NICs or careful virtual NIC configuration
- Some enhanced features require pfSense Plus or Netgate hardware
- Heavier UI/management overhead compared to minimal solutions

### Consequences
- Enhanced security posture and visibility across the home lab
- Fully isolated and segmented VLAN architecture
- Ability to simulate enterprise-grade network architecture
- Centralized management of firewall rules, DNS, VPN, and logging
- Higher hardware usage if running virtualized; requires proper tuning

### Alternatives Considered
- OPNsense
- OpenWRT
- ISP Router
- Untangle / Sophos XG
---
## ADR-004: Deploy Home Assistant OS for Home Automation Platform (2025-11-15)

### Status
Accepted

### Context
A centralized, reliable automation platform is needed to manage IoT devices, sensors, routines, scenes, and smart-home integrations.  

Several architectural choices existed:

- Run Home Assistant in a Docker container  
- Run Home Assistant in a VM on a general-purpose Linux OS  
- Use Home Assistant Core (manual Python install)  
- Use Home Assistant OS (appliance-style system)  
- Host on dedicated physical hardware  
- Host as a VM on Proxmox  

Key considerations included:
- Stability and simplicity  
- Ease of backup and restore  
- Long-term maintainability  
- Integration support (Zigbee, Z-Wave, Matter, WiFi devices)  
- Isolation from other services  
- Update reliability  
- Alignment with homelab infrastructure standards  

### Decision
Deploy **Home Assistant OS (HAOS)** as a **dedicated VM** on the Proxmox virtualization platform.

### Rationale
**Pros**
- Appliance-style OS designed specifically for Home Assistant  
- Simplified lifecycle management (updates, add-ons, snapshots)  
- Strong isolation compared to Docker or mixed-use hosts  
- Works cleanly with Proxmox backup jobs and VM snapshots  
- Reliable upgrade path with low risk of breakage  
- Supports add-ons (ESPHome, Mosquitto, Zigbee2MQTT, etc.)  
- Ideal for long-term “set-and-forget” operation  
- No underlying OS maintenance required  
- Consistent behavior across hardware and VM environments  

**Cons**
- Limited OS-level shell access (appliance-style restrictions)  
- Less flexible than Docker-based deployments  
- Migration between hypervisors requires full image export/import  
- Slightly higher resource overhead than a containerized setup  

### Consequences
- The Home Automation Service will run exclusively on this VM  
- Backups will use *both* Home Assistant Snapshots and Proxmox backups  
- Integrations (Zigbee, WiFi, Matter, etc.) will depend on this stable VM  
- Updates will be handled via HAOS UI (monthly recommended)  
- Z-Wave/Zigbee adapters may require USB passthrough configuration in Proxmox  
- Future scaling (Matter bridges, extra integrations) will remain compatible  

### Alternatives Considered
- **Docker container:** More flexibility but requires managing the host OS, manual add-ons, and additional maintenance overhead  
- **Home Assistant Core:** Most complex; no add-on system; harder to maintain long-term  
- **Dedicated physical device (Pi / Mini PC):** Good isolation but less efficient than using existing Proxmox resources  
- **General-purpose Linux VM + Home Assistant Supervised:** Not officially supported; higher breakage risk  

---

