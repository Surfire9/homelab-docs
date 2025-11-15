# [Platform Name]

## Overview
Brief description of what this platform provides, why it exists, and what major systems depend on it.

---

## Inventory

### Platform Node: [Hostname]

- **Hostname:** server01.lab.local  
- **Role:** Proxmox Hypervisor — Primary Compute Node  
- **Location:** Rack 1U, Home Office  
- **Acquired:** 2024-01-15  
- **Status:** 🟢 Production

#### Hardware Specifications
- **CPU:** Intel Xeon E-2388G (8c/16t)  
- **RAM:** 128GB DDR4 ECC  
- **Storage:**
  - **Boot:** 2× 500GB NVMe (RAID1)
  - **Data:** 4× 2TB SSD (RAIDZ1)
- **Network Interfaces:** 2× 10GbE, 2× 1GbE  
- **Out-of-Band Management:** IPMI (192.168.1.10)

#### Power & Environmental
- **Power Draw:** ~150W idle / ~250W load  
- **UPS Protected:** Yes (APC 1500VA)  
- **Cooling:** Active (3× 120mm fans)

#### Warranty & Support
- **Warranty Expiration:** 2027-01-15  
- **Support Contact:** manufacturer-support@example.com  

#### Notes
- BIOS updated to **v2.10** on *2024-03-15*  
- RAM upgraded from **64GB → 128GB** on *2024-06-20*

---

### Platform Node: [Next Hostname]

Repeat the above structure for each platform node.
