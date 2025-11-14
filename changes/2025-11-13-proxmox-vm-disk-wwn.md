# Change: Added WWN to Proxmox VM Disks

**Date:** 2025-11-13
**Type:** Configuration Change
**Risk Level:** Low
**Implemented By:** Surfire9
**Approved By:** Surfire9

## Change Description
Added unique WWN (World Wide Name) identifiers to TrueNAS VM disks in Proxmox to resolve duplicate serial number issues.

## Justification
TrueNAS requires unique disk identifiers. Serial numbers were not being propagated properly through QEMU virtualization layer.

## Systems Affected
- **VM:** TrueNAS-MediaStorage (VM ID: 103)
- **Host:** proxmox.lab.local
- **Disks:** scsi0, scsi1

## Implementation Steps
1. Stopped VM 103
2. Edited `/etc/pve/qemu-server/103.conf`
3. Added WWN parameters to disk definitions
4. Started VM 103
5. Verified disk IDs in TrueNAS

## Rollback Plan
Revert to previous VM configuration snapshot "PostInstallStable".

## Testing
- [x] Verified unique disk IDs visible in TrueNAS
- [x] Confirmed storage pool creation successful
- [x] Checked VM performance (no degradation)

## Result
✅ Successful - Disks now properly identified with unique WWN.

## Related
- [[incidents/2024-11-13-truenas-disk-serial-issue|Related Incident]]
