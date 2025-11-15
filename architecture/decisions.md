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

## ADR-002: Deploy Home Assistant OS for Home Automation Platform (2025-11-15)

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

