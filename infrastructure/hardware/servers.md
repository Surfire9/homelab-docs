# Physical Servers

## Overview
Brief description of your server infrastructure.

## Inventory

### Server: [Hostname]
- **Hostname:** server01.lab.local
- **Purpose:** Proxmox hypervisor, primary compute
- **Location:** Rack 1U, Home office
- **Acquired:** 2024-01-15
- **Status:** 🟢 Production

#### Hardware Specs
- **CPU:** Intel Xeon E-2388G (8c/16t)
- **RAM:** 128GB DDR4 ECC
- **Storage:**
  - Boot: 2x 500GB NVMe (RAID1)
  - Data: 4x 2TB SSD (RAIDZ1)
- **Network:** 2x 10GbE, 2x 1GbE
- **IPMI:** 192.168.1.10

#### Power & Environment
- **Power Draw:** ~150W idle, ~250W load
- **UPS Protected:** Yes (APC 1500VA)
- **Cooling:** Active (3x 120mm fans)

#### Warranty & Support
- **Warranty Expires:** 2027-01-15
- **Support Contact:** manufacturer-support@example.com

#### Notes
- BIOS updated to v2.10 on 2024-03-15
- RAM upgraded from 64GB on 2024-06-20

---

### Server: [Next Server]
[Repeat structure]

Template: Service README
File: services/[service-name]/README.md
