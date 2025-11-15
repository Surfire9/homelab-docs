# Home Automation Service – Troubleshooting Guide

**Last Updated:** 2024-11-15

---

# Table of Contents

* [Quick Diagnostics](#quick-diagnostics)
* [Home Assistant Not Loading](#home-assistant-not-loading)
* [UI Unreachable](#ui-unreachable)
* [Zigbee / Z-Wave / Matter Issues](#zigbee--z-wave--matter-issues)
* [Automations Not Triggering](#automations-not-triggering)
* [Add-ons Not Starting](#add-ons-not-starting)
* [Backup / Snapshot Issues](#backup--snapshot-issues)
* [Slow Performance / High CPU Usage](#slow-performance--high-cpu-usage)
* [Time / Date Issues](#time--date-issues)
* [SSL / HTTPS Issues](#ssl--https-issues)
* [Emergency Recovery](#emergency-recovery)
* [Diagnostic Command Reference](#diagnostic-command-reference)
* [Getting Help](#getting-help)
* [Preventive Measures](#preventive-measures)

---

# Quick Diagnostics

* Check HA status: `ha core info`, `ha core logs --tail 50`, `ha core check`
* Restart services:
  `ha core restart`, `ha supervisor restart`, `ha host reboot`
* System logs:
  `ha supervisor logs`, `ha host logs`
* Add-ons:
  `ha addons`, `ha addons logs <addon>`

---

# Home Assistant Not Loading

### Symptoms

* Stuck on “Preparing Home Assistant…”
* Supervisor unhealthy
* UI never finishes loading

### Diagnostics

* `ha core logs`
* `ha supervisor logs`
* `ha core check`

### Fix: Database Corruption

1. `ha core stop`
2. Rename: `mv /config/home-assistant_v2.db /config/home-assistant_v2.db.corrupt`
3. `ha core start`

### Fix: Disk Full

* `df -h /`
* `ha host info`

### Fix: Add-on Crash

* `ha addons logs <addon>`
* `ha addons stop <addon>`

---

# UI Unreachable

### Diagnostics

* `ha core info`
* `ha core logs --tail 20`

### Fix: Core Restart

* `ha core restart`

### Fix: DNS Issue

Try direct IP:
**http://<IP>:8123**

### Fix: Firewall / VLAN

Test network connectivity:
`curl -v http://<IP>:8123`

---

# Zigbee / Z-Wave / Matter Issues

### Diagnostics

* `ha hardware info`
* `ha core logs | grep zha`
* `ha addons logs zigbee2mqtt`

### Fix: USB Passthrough in Proxmox

* Pass by **vendor ID** instead of tty path
* Replug USB if needed

### Fix: Zigbee Interference

* Use channels **15, 20, or 25**
* Move adapter away from metal or routers

### Fix: Corrupt Zigbee Database

* Rename: `mv /config/zigbee.db /config/zigbee.db.old`
* Restart: `ha core restart`

---

# Automations Not Triggering

### Checks

* Settings → Automations → **Trace**
* Ensure conditions allow trigger
* Verify entity states
* Check timezone configuration

### Common Fix

* Adjust trigger type (e.g., from `state` to `numeric_state`)

---

# Add-ons Not Starting

### Diagnostics

* `ha addons logs <addon>`
* `ha supervisor logs`

### Fix: Port Conflict

* Check listening ports: `ss -tulpn | grep <port>`

### Fix: Invalid Config

* Check `/config/<addon>/` YAML

### Fix: Resource Limit

* Increase vCPU or RAM in Proxmox

---

# Backup / Snapshot Issues

### Diagnostics

* `ha backups new --name test`
* `ha backups list`
* `ha backups info <id>`

### Fix: Disk Full

* `df -h /`

### Fix: Corrupt Backup

* `ha backups remove <id>`

### Fix: Proxmox Backup Failure

* Ensure free storage
* Use **stop mode** backups for HAOS

---

# Slow Performance / High CPU Usage

### Diagnostics

* `ha core logs | grep -i warning`
* `ha host info`
* Proxmox: `top`, `qm status <vmid>`

### Fixes

* Increase RAM to 4–8GB
* Increase CPU to 2–4 vCPU
* Disable unused integrations

### Reduce Recorder DB Bloat

Add to `configuration.yaml`:

**recorder:**
**purge_keep_days: 7**
**auto_purge: true**

*(Formatting without code blocks so GitHub accepts paste.)*

---

# Time / Date Issues

### Diagnostics

* `ha core info | grep time`

### Fix

* `ha host options --timezone "America/New_York"`
* Restart: `ha core restart`

---

# SSL / HTTPS Issues

### Diagnostics

* `curl -vI https://homeautomation.lab.local`

### Fixes

* Renew cert in proxy (NGINX Proxy Manager, Traefik, etc.)
* Ensure websocket support **for port 8123**
* Ensure proper DNS mapping

---

# Emergency Recovery

### Rebuild Core

* `ha core rebuild`
* `ha core start`

### If VM Will Not Boot

* Machine Type: **q35**
* BIOS: **OVMF (UEFI)**
* Disk Bus: **virtio-scsi**

### Restore Snapshot

Supervisor → Backups → **Restore**

---

# Diagnostic Command Reference

**HAOS commands:**
`ha core logs`
`ha core restart`
`ha supervisor logs`
`ha host reboot`
`ha addons logs <addon>`

**Network tests:**
`ping homeautomation.lab.local`
`curl http://<IP>:8123`
`nslookup homeautomation.lab.local`

---

# Getting Help

Before asking for help, collect:

`ha core info`
`ha core logs --tail 100`
`ha supervisor logs --tail 100`
`df -h /`
`free -h`

Helpful resources:

* Home Assistant Docs
* HAOS GitHub
* Home Assistant Community Forum

---

# Preventive Measures

### Monitoring

* Add uptime check in Uptime Kuma
* Monitor RAM/CPU via Proxmox
* Monitor certificate expiration

### Maintenance

* Monthly HAOS updates
* Weekly log review
* Weekly snapshots
* Snapshot before upgrades

---
