# Change: Connected Home Assistant OS to Proxmox via CLI
**Date:** 2025-11-15  
**Type:** System Integration  
**Risk Level:** Medium  
**Implemented By:** Surfire9  
**Approved By:** Surfire9

## Change Description
Connected Home Assistant OS (HAOS) as a virtual machine within the Proxmox home lab environment using the Proxmox command-line interface, enabling centralized smart-home automation, environment monitoring, device control, and future integration with security tooling.

This change included:
- Creating the HAOS VM directly through CLI  
- Downloading/importing the HAOS QCOW2 image  
- Converting and relocating the disk to Proxmox storage  
- Configuring necessary hardware flags (UEFI, SCSI, Q35)  
- Initial boot and network configuration  

## Justification
Home Assistant OS is required for:
- Centralized home automation  
- Integration with sensors, lights, security devices, and network equipment  
- Remote access (Tailscale planned)  
- Building a “small company” style lab with automation, monitoring, and alerting systems  

Proxmox CLI setup provides:
- Cleaner automation pipeline  
- Repeatable infrastructure  
- Better control of VM parameters  
- Reduced overhead vs. manual UI configuration  

## Systems Affected
**VM:** HomeAssistantOS (VM ID: 120)  
**Host:** proxmox.lab.local  
**Storage:** local-lvm  
**Networks:** vmbr0 (LAN), future vmbrX/VLANs planned  

## Implementation Steps
1. Downloaded HAOS QCOW2 image via CLI  
2. Uploaded image to /tmp  
3. Created a new VM using CLI with correct hardware specs  
4. Imported the QCOW2 disk to local-lvm  
5. Attached disk as SCSI with virtio-scsi-single  
6. Enabled UEFI + Secure Boot support  
7. Set machine type to Q35  
8. Configured network adapter to virtio model  
9. Started VM  
10. Verified boot and initial setup wizard  
11. Confirmed IP assignment via Proxmox + router  

## Rollback Plan
- Remove VM ID 120  
- Restore previous Proxmox state (snapshot “Pre-HAOS-Install” if available)  
- Delete imported disk image  
- No impact to existing services (change is isolated)  

## Testing
- Confirmed HAOS boots into installation environment  
- Successfully reached Home Assistant onboarding UI  
- Verified HAOS reachable via assigned LAN IP  
- Checked CPU and Memory utilization (normal)  
- Ensured no impact to other running VMs or storage pools  

## Result
✔️ Successful — Home Assistant OS fully deployed and operational in Proxmox.  
System reachable, responsive, and ready for integration with home sensors, automations, and network equipment.

## Related
[[systems/Proxmox-VM-Builds]]  
[[network/2025-11-10-proxmox-network-map]]  
[[incidents/2025-11-15-haos-initialization-issues]]
