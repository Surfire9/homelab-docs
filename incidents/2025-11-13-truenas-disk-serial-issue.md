# Incident: TrueNAS Disk Serial Duplicate Error

**Date:** 2024-11-13
**Severity:** Medium
**Status:** Resolved
**Duration:** 45 minutes
**Reported By:** Surfire9

## Summary
TrueNAS unable to configure storage pool due to duplicate serial number error on Proxmox virtual disks.

## Timeline
- **14:00** - Issue discovered during TrueNAS storage configuration
- **14:15** - Attempted serial number parameter in VM config
- **14:30** - Switched to WWN (World Wide Name) parameter
- **14:45** - Issue resolved, disks now uniquely identified

## Impact
- **Services Affected:** TrueNAS (not yet in production)
- **Users Affected:** 0
- **Data Loss:** None

## Root Cause
Proxmox/QEMU virtual disks did not properly propagate serial numbers to guest OS, causing TrueNAS to see all disks with serial "None".

## Resolution
Changed VM disk configuration to use WWN instead of serial numbers:
```bash
scsi0: local-lvm:vm-103-disk-1,size=32G,wwn=0x5000000000000001
scsi1: local-lvm:vm-103-disk-2,size=50G,wwn=0x5000000000000002
```

## Prevention
- Document WWN requirement for all TrueNAS VMs
- Update VM creation procedures
- Add to troubleshooting guide

## Action Items
- [x] Update TrueNAS deployment documentation
- [x] Add note to Proxmox VM creation checklist
- [ ] Create script to validate disk IDs before TrueNAS deployment

## Lessons Learned
- WWN is more reliable than serial numbers for QEMU virtual disks
- Always verify disk identification in guest OS after VM creation

## References
- [[services/truenas/troubleshooting|TrueNAS Troubleshooting Guide]]
- [[infrastructure/hypervisors/proxmox|Proxmox Configuration]]
