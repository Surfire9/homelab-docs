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

# HomeLab Automation OS Platform

## Overview
HomeLab Automation OS (HAOS) provides a dedicated, appliance-style platform designed to host the Home Assistant application and associated automation services.  
It delivers a stable, self-contained environment with minimal administrative overhead, supporting integrations, add-ons, and device automations across the home network.

This platform acts as the foundational layer for all Home Automation services within the HomeLab environment.

---

## Inventory

### Platform Node: homeautomation01.lab.local

- **Hostname:** homeautomation01.lab.local  
- **Role:** Home Automation Platform (Home Assistant OS)  
- **Location:** [Fill In: e.g., Network Closet / Shelf / Rack]  
- **Acquired:** [Fill In Date]  
- **Status:** 🟢 Production

#### Hardware Specifications
- **Hardware Type:** [Mini PC / Single-Board Computer / VM / NUC / etc.]  
- **CPU:** [Fill In]  
- **RAM:** [Fill In]  
- **Storage:**
  - **Boot:** [Fill In — e.g., 64GB SSD, eMMC, NVMe]  
  - **Data:** [Fill In — e.g., SSD, external storage if used]
- **Network Interfaces:** [Fill In — e.g., 1× 1GbE]  
- **Out-of-Band Management:** [IPMI / None / Local access only]

#### Power & Environmental
- **Power Draw:** ~[Fill In] W  
- **UPS Protected:** [Yes/No; model if applicable]  
- **Cooling:** [Passive / Active — fill in hardware specifics]

#### Warranty & Support
- **Warranty Expiration:** [Fill In]  
- **Support Contact:** Vendor or community support via Home Assistant documentation

#### Notes
- HAOS version installed: **[Fill In Version]**  
- Add-ons used: **[e.g., Mosquitto, ESPHome, File Editor, etc.]**  
- Last major update applied: **[Fill In Date]**  
- Integrated device ecosystems: **[Zigbee, Z-Wave, Wi-Fi devices, Matter, etc.]**

---

### Platform Node: [Next Hostname]
_Replicate the above section if you add a second automation node (redundancy, test instance, etc.)._
