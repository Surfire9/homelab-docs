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

## ADR-002: Use for (2025-xx-xx)
