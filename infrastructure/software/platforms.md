# Software Platforms

This file contains the master template for software platforms and the individual platform entries below it.

---

# 📌 Platform Template

## [Platform Name]

### Overview
Brief description of this software platform, its purpose, and what services or applications depend on it.  
Explain whether it is a VM, container host, appliance OS, or general-purpose system.

---

## Deployment Details
- **Type:** Virtual Machine / Container Host / Appliance OS  
- **Host Location:** [e.g., Proxmox Node: server01.lab.local]  
- **Operating System:** [e.g., Home Assistant OS / Ubuntu Server 22.04]  
- **Deployment Date:** [Fill In]  
- **Status:** 🟢 Production | 🟡 Testing | 🔴 Deprecated

---

## Configuration

### System Resources
- **vCPU:** [Fill In]  
- **Memory Allocation:** [Fill In]  
- **Storage Allocation:**  
  - **Primary Disk:** [Size + Type]  
  - **Additional Disks:** [If any]  
- **Network Interfaces:**  
  - **Primary IP:** [Fill In]  
  - **VLAN / Network Zone:** [Fill In]  
  - **MAC Address:** [Optional]

### Platform Components
- **Core Software Version:** [Fill In]  
- **Kernel Version:** [Optional]  
- **Key Add-ons / Modules:**  
  - [Module 1]  
  - [Module 2]  

### Integrations & Dependencies
- **Depends On:**  
  - [Proxmox Node]  
  - [Storage]  
  - [Network]  

- **Used By:**  
  - [Service 1]  
  - [Service 2]  

---

## Network & Access
- **Management URL:** [Fill In]  
- **API Endpoints:** [If applicable]  
- **Authentication:** [Local / SSO / Token]  
- **Remote Access:** [SSH / Web UI / None]

---

## Maintenance & Lifecycle
- **Update Cadence:** [Fill In]  
- **Backup Method:** [Snapshot / Export / Tool-based / None]  
- **Last Update Applied:** [Fill In]  
- **Scheduled Tasks:**  
  - [Backups]  
  - [Log rotations]  

---

## Notes
- [Any relevant operational notes, upgrade history, integrations, issues]

---

## Revision History

| Date       | Change                         |
|------------|--------------------------------|
| 2024-xx-xx | Initial deployment             |
| 2024-xx-xx | Updated to version X.X         |
| 2024-xx-xx | Configuration change           |

---

# 📘 home-automation-os

## Overview
HomeLab Automation OS (HAOS) provides the dedicated operating environment required to run Home Assistant and its automation ecosystem.  
It is deployed as a VM on the HomeLab virtualization platform, offering stability, isolation, and reliable access to connected IoT devices on the home network.

This platform hosts the **Home Automation** service and all related integrations.

---

## Deployment Details
- **Type:** Virtual Machine (Appliance OS)  
- **Host Location:** Proxmox Node: **[Fill In]**  
- **Operating System:** Home Assistant OS  
- **Deployment Date:** **[Fill In]**  
- **Status:** 🟢 Production

---

## Configuration

### System Resources
- **vCPU:** [Fill In]  
- **Memory Allocation:** [Fill In]  
- **Storage Allocation:**  
  - **Primary Disk:** [Fill In]  
  - **Additional Disks:** (Optional)  
- **Network Interfaces:**  
  - **Primary IP:** [Fill In]  
  - **VLAN / Network Zone:** [Fill In]  
  - **MAC Address:** [Fill In]

### Platform Components
- **Core Software Version:** [Fill In — e.g., HAOS 12.x]  
- **Kernel Version:** [Optional]  
- **Key Add-ons / Modules:**  
  - [e.g., Mosquitto Broker]  
  - [ESPHome]  
  - [File Editor]  
  - [Zigbee2MQTT / ZHA]

### Integrations & Dependencies
- **Depends On:**  
  - Proxmox Virtualization Platform  
  - Home Network (IoT VLAN if applicable)  
  - Local storage on Proxmox node  

- **Used By:**  
  - Home Automation Service  
  - Integrations with Zigbee, Wi-Fi, Z-Wave, and Matter devices  

---

## Network & Access
- **Management URL:** https://[your-haos-IP]:8123  
- **API Endpoints:** Native Home Assistant API  
- **Authentication:** Home Assistant user authentication  
- **Remote Access:**  
  - Via Tailscale / VPN (if configured)  
  - Web UI (primary)  
  - No SSH (HAOS restricts shell access)

---

## Maintenance & Lifecycle
- **Update Cadence:** Manual approval (monthly recommended)  
- **Backup Method:**  
  - Home Assistant Snapshots  
  - Optional off-host backup (Proxmox backup jobs)  
- **Last Update Applied:** [Fill In]  
- **Scheduled Tasks:**  
  - Automated snapshots (if configured)  
  - Add-on updates  

---

## Notes
- Platform optimized for appliance-style deployment — limited shell access by design  
- Integrates with multiple IoT ecosystems (Zigbee, Wi-Fi, Matter, etc.)  
- Best practice: enable regular full snapshots before major updates  
- Suitable candidate for future migration to dedicated hardware if automation load grows

---

## Revision History

| Date       | Change                                      |
|------------|----------------------------------------------|
| 2024-xx-xx | Platform entry created                       |
| 2024-xx-xx | HAOS updated to version X.X                  |
| 2024-xx-xx | Added integrations and add-on documentation  |
