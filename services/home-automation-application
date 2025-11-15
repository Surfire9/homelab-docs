# Home Automation Application

## Overview
**Purpose:**  
The Home Automation Application provides centralized control, automation, scheduling, notifications, and integration for all smart-home and IoT devices in the homelab environment.  
It orchestrates Zigbee, Wi-Fi, Matter, and other device ecosystems into a unified automation layer.

**Status:** 🟢 Production  
**Owner:** Mikey  
**Created:** 2024-11-15  
**Last Updated:** 2024-11-15

## Quick Facts
- **Type:** Application Service (Home Automation)
- **Access URL:** https://homeautomation.lab.local:8123
- **Dependencies:**  
  - Home Assistant OS Platform  
  - Proxmox Virtualization Platform  
  - IoT VLAN (optional)  
  - Zigbee/Matter radio integrations  
- **Backup Schedule:**  
  - Daily Home Assistant Snapshots  
  - Weekly VM-level Proxmox Backup
- **Monitoring:**  
  - Uptime Kuma (HTTPS check)  
  - Internal HA health indicators  

---

# Architecture

## Deployment
- **Platform:** Virtual Machine (Home Assistant OS)
- **Host:** proxmox.lab.local (Proxmox VE)
- **Resources:**  
  - **CPU:** [Fill In — typically 2–4 vCPU]  
  - **RAM:** [Fill In — typically 4–8GB]  
  - **Storage:** [Fill In — typically 32–64GB allocated]

## Network
- **IP Address:** [Fill In — e.g., 192.168.X.X]  
- **Ports:**  
  - **8123/tcp** – Home Assistant Web UI  
  - **API** – Native Home Assistant API  
  - **Add-ons** – Dynamic ports depending on module  
- **VLAN:** [Fill In — e.g., IoT VLAN or Services VLAN]  
- **Firewall Zone:** Internal / VPN-only

## Storage
- **Data Location:** Managed internally by HAOS (`/data` partition)  
- **Backup Location:**  
  - VM Snapshots → Proxmox Storage  
  - HA Snapshots → Internal or external backup target  
- **Size:** [Fill In based on VM disk usage]

---

# Access & Credentials
- **Admin Portal:** https://homeautomation.lab.local:8123  
- **Credentials:**  
  - Stored in Bitwarden under: `HomeLab/HomeAutomation/Admin`  
- **API Keys:**  
  - Stored securely in vault.lab.local (`HomeAutomation/API`)  
- **SSH Access:**  
  - Not applicable (HAOS restricts shell access)  
  - Console access available through Proxmox only  

---

# Dependencies

1. **HomeAssistantOS Platform**  
   - Provides appliance-style runtime for the application  

2. **Proxmox Virtualization Platform**  
   - VM hosting, snapshots, backup jobs  

3. **IoT Network/VLAN** (optional but recommended)  
   - Isolates IoT devices from production LAN  

4. **Radio Integrations**  
   - Zigbee (ZHA or Zigbee2MQTT)  
   - Matter / Thread (if using compatible hardware)  
   - Wi-Fi devices via integrations  

---

# Installation

## Prerequisites
```bash
# None required on the service itself.
# Installation is handled through Proxmox VM provisioning.
# Add-on modules managed through Home Assistant Add-on Store.

# Deployment steps
# 1. Download HAOS VM image from official Home Assistant site
# 2. Upload the QCOW2/OVA image to Proxmox
# 3. Create VM and attach storage + network
# 4. Start VM and wait for initialization
# 5. Access Web UI at: http://<IP>:8123
# 6. Complete initial HAOS onboarding
# 7. Configure integrations and add-ons as needed

# Notes
Designed for high reliability and minimal maintenance
Add-ons (MQTT, ESPHome, File Editor, etc.) expand functionality
Strongly recommended to use both HA Snapshots and Proxmox VM backups
Ideal core component of the Home Automation stack in the homelab
