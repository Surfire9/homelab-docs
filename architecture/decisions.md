# Architecture Decision Records

---

## ADR-001: Deploy Proxmox VE as Primary Virtualization Platform (2025-10-15)

### Status
Accepted

### Context
- The home lab requires a robust, flexible virtualization platform capable of running multiple servers and security tools (Ubuntu Server, pfSense, Wazuh, TrueNAS clients, Docker hosts, etc.).
- The platform must support both full virtual machines and lightweight containers.
- Needs to integrate cleanly with ZFS, VLAN networking, snapshots, templates, and potential clustering for future expansion.
- Alternative hypervisors (ESXi, XCP-ng, KVM/libvirt) were considered but have limitations for homelab use cases.

### Options Considered
- Proxmox VE
- VMware ESXi
- XCP-ng
- KVM / libvirt
- Running services directly on bare metal

### Decision
Deploy **Proxmox VE** as the primary hypervisor for the entire home lab and host all core virtual machines, containers, and infrastructure services.

### Rationale
**Pros:**
- Open-source and free to use (subscription optional)
- Supports both **VMs** and **LXC containers** within the same platform
- Built-in **ZFS support** with native snapshots, replication, and dataset integration
- Excellent web interface with comprehensive management tools
- Strong community documentation and long-term reliability
- Easy future expansion into multi-node clusters with shared storage
- Seamless integration with VLAN-aware bridges and pfSense firewall design
- Template-based VM deployment simplifies provisioning

**Cons:**
- Steeper learning curve than ESXi for new users
- Smaller enterprise ecosystem compared to VMware
- LXC containers require attention to template and compatibility constraints
- Advanced clustering requires additional network/storage planning

### Consequences
- All core infrastructure (Ubuntu servers, pfSense, Wazuh, Docker hosts, monitoring servers) will run on Proxmox
- Simplified VM lifecycle management through snapshots, backups, cloning, and templates
- ZFS integration enables reliable storage, snapshots, and easy rollback
- Consistent virtualization layer for future expansion (HA, clustering, replication)
- Requires ongoing familiarity with Proxmox-specific workflows and networking concepts

### Alternatives Considered
- **VMware ESXi:** Strong enterprise support but heavily limited in the free version and poor fit for homelab expansion
- **XCP-ng:** Capable but smaller community, less flexible UI, and fewer built-in features
- **KVM/libvirt (raw):** Very powerful but requires significantly more manual configuration and lacks Proxmox’s unified management interface
- **Bare Metal Services:** No centralized VM management, harder to scale, and limits isolation for security testing

---

# Architecture Decision Records

---

## ADR-002: Add Ubuntu Server to Home Lab Infrastructure (2025-10-27)

### Status
Accepted

### Context
- A dedicated Linux server is needed to support containerized workloads, automation services, monitoring tools, and security-focused labs.
- Current infrastructure includes Proxmox hosts, Windows system, and several service VMs.
- Adding an Ubuntu Server VM provides a stable, standardized environment for Linux-based services.
- Ubuntu Server LTS offers long-term support and excellent compatibility with automation and security tooling.

### Options Considered
- Deploy Ubuntu Server LTS as a VM inside Proxmox
- Use Debian, Rocky Linux, AlmaLinux
- Continue relying on existing mixed OS environments

### Decision
Deploy Ubuntu Server LTS as a VM within Proxmox with allocated resources based on the intended service role.

### Rationale
**Pros:**
- Stable, popular, and widely supported in enterprise environments
- Excellent package ecosystem and documentation
- Integrates cleanly with Proxmox (templates, snapshots, backups)
- Easier maintenance compared to some alternatives

**Cons:**
- Slight performance overhead due to virtualization
- Requires periodic updates and security maintenance
- Additional resource consumption on the host

### Consequences
- Standardized Linux base for automation and security tools
- Easier replication via Proxmox templates
- Improved isolation and organization of services
- Minor increase in Proxmox resource usage

### Alternatives Considered
- Debian
- Rocky Linux / AlmaLinux
- Bare-metal deployment for performance

---

# Architecture Decision Records

---

## ADR-003: Deploy pfSense as Primary Firewall & Network Segmentation Platform (2025-10-28)

### Status
Accepted

### Context
- The home lab needs a dedicated, enterprise-grade firewall to manage VLAN segmentation, IDS/IPS, VPN access, DNS filtering, and traffic shaping.
- Existing routers (ISP or consumer-grade) lack adequate visibility, logging, and granular security controls.
- A full-featured firewall is required to support lab environments, security research, remote access (Tailscale/OpenVPN), and isolation of test networks.

### Options Considered
- Deploy pfSense CE/Plus on dedicated hardware or as a Proxmox VM
- Deploy OPNsense
- Use a consumer router with third-party firmware (OpenWRT/DD-WRT)
- Continue using ISP-provided gateway/router

### Decision
Deploy pfSense as the primary firewall on dedicated hardware (or virtualized with proper NIC passthrough) to enable network segmentation, monitoring, and advanced security controls.

### Rationale
**Pros:**
- Mature, stable, and widely adopted firewall platform
- Strong VLAN support for segmentation of production, lab, IoT, DMZ, and testing networks
- Built-in VPN support (WireGuard, OpenVPN, IPsec)
- IDS/IPS options (Snort or Suricata)
- Advanced NAT, firewall rules, DHCP, DNS Resolver/Forwarder, and traffic shaping
- Works well with Proxmox via virtIO or NIC passthrough
- Extensive official documentation and long-term support

**Cons:**
- Steeper learning curve for advanced configurations
- Requires multiple NICs or careful virtual NIC configuration
- Some enhanced features require pfSense Plus or Netgate hardware
- Heavier UI/management overhead compared to minimal solutions

### Consequences
- Enhanced security posture and visibility across the home lab
- Fully isolated and segmented VLAN architecture
- Ability to simulate enterprise-grade network architecture
- Centralized management of firewall rules, DNS, VPN, and logging
- Higher hardware usage if running virtualized; requires proper tuning

### Alternatives Considered
- OPNsense
- OpenWRT
- ISP Router
- Untangle / Sophos XG

---

# Architecture Decision Records

---

## ADR-004: Deploy Linux Mint 22.2 as Admin Workstation for Firewall and Security Console Access (2025-10-28)

### Status
Accepted

### Context
- A dedicated and secure administrative workstation is required for managing the pfSense firewall, Proxmox hosts, Wazuh Dashboard, and other critical lab services.
- Using a general-purpose Windows machine for administrative tasks increases risk and mixes daily-use activities with privileged actions.
- Linux Mint 22.2 offers a stable, lightweight, user-friendly environment with long-term support, making it suitable for secure administrative operations.
- The workstation must support SSH, VPN, browser-based dashboards, and secure credential storage.

### Options Considered
- Deploy Linux Mint 22.2 as a dedicated admin workstation
- Use Windows 10/11 as the admin machine
- Use Ubuntu Desktop / Fedora Workstation
- Use a browser-based Chromebook or lightweight Linux distro

### Decision
Deploy **Linux Mint 22.2** as the dedicated admin workstation for secure access to pfSense, Wazuh, Proxmox, and related infrastructure dashboards.

### Rationale
**Pros:**
- Stable, long-term support and user-friendly environment
- Low resource usage; ideal for admin utility machines
- Strong built-in support for SSH, OpenVPN, WireGuard, and administrative tooling
- Compatible with browser-based consoles (pfSense, Wazuh, Proxmox GUI)
- Reduced attack surface compared to daily-use Windows environments
- Cinnamon desktop provides familiar UI with minimal overhead
- Easy to maintain and update with predictable package management (APT)

**Cons:**
- Requires familiarity with Linux administration
- Some commercial tools are Windows-only (though not needed for this role)
- Hardware driver compatibility depends on Mint’s Ubuntu base
- Must ensure secure configuration (updates, firewall, no unnecessary apps)

### Consequences
- Stronger separation between daily personal computing and privileged administrative tasks
- More secure access pathway to core security infrastructure (firewall + Wazuh)
- Improved operational hygiene by isolating credentials, certificates, and admin tools
- Consistent, predictable environment for SSH, VPN, and browser-based management
- Slight overhead maintaining an additional workstation or VM

### Alternatives Considered
- Windows Admin Workstation
- Ubuntu Desktop or Fedora Workstation
- Chromebook or thin client with browser-only access
- Headless admin approach via SSH-only from various machines

---

# Architecture Decision Records

---

## ADR-005: Deploy TrueNAS for Centralized Storage, Backups, and ZFS-Based Data Integrity (2025-11-12)

### Status
Accepted

### Context
- The home lab requires a reliable and centralized storage solution capable of hosting VMs, backups, media libraries, configuration archives, and security/log data.
- Current ad-hoc storage (external drives, local VM disks, cloud free tiers) is fragmented, difficult to manage, and lacks enterprise-grade protection.
- A storage platform with snapshots, data integrity checks, redundancy, and network protocols (NFS, SMB, iSCSI) is needed.
- ZFS provides end-to-end data integrity, snapshots, replication, and self-healing capabilities ideal for home lab and security-focused environments.

### Options Considered
- Deploy TrueNAS (CORE or SCALE)
- Use Unraid
- Use plain Linux with ZFS on Ubuntu/Debian
- Use Windows Server with Storage Spaces
- Continue with local disks inside Proxmox VMs

### Decision
Deploy **TrueNAS** as the central storage and backup appliance using ZFS with redundancy, snapshots, and network shares for the home lab.

### Rationale
**Pros:**
- ZFS provides enterprise-grade data integrity, checksums, and self-healing
- Native support for SMB, NFS, and iSCSI for Proxmox, Linux, and Windows systems
- Provides snapshots, replication, and dataset-level permissions
- Highly stable and well-documented with strong community and enterprise support
- TrueNAS SCALE offers Linux-based flexibility with containers and Kubernetes
- Easy integration into Proxmox as shared storage or backup target
- Web interface makes management intuitive without sacrificing advanced features

**Cons:**
- Requires more RAM (ZFS prefers 16 GB or more for optimal performance)
- Must follow strict ZFS disk management rules (no mixing sizes, no expansion of RAIDZ vdevs)
- Heavier than simple NAS solutions like OpenMediaVault
- Virtualized deployments require careful configuration to preserve ZFS integrity

### Consequences
- Reliable and unified storage system for the entire home lab
- Strong data-protection model with snapshots, replication, and integrity checks
- Simplifies Proxmox storage management and offloads VM disk storage
- Provides a single source for backups, media, logs, and ISO repositories
- Hardware requirements increase (RAM, CPU, proper HBAs or SATA passthrough)
- Long-term maintainability improves but requires thoughtful pool design

### Alternatives Considered
- **Unraid** – flexible and user-friendly but lacks ZFS-native integrity
- **Linux ZFS Server** – powerful but requires manual configuration and lacks TrueNAS UI/tools
- **Windows Server Storage Spaces** – adequate but weaker integrity model and complex licensing
- **Local Proxmox Disks Only** – no centralization; poor for redundancy and backups

---

# Architecture Decision Records

---

## ADR-006: Deploy Wazuh as Centralized Security Monitoring and SIEM Platform (YYYY-MM-DD)

### Status
Accepted

### Context
- The home lab requires a centralized security monitoring solution to track events, detect anomalies, and consolidate logs from servers, network devices, and security appliances.
- Existing logging is fragmented across Proxmox, pfSense, Linux VMs, and application containers, making correlation and alerting difficult.
- A SIEM-like tool is needed to support security practice, incident response training, and compliance-focused workflows.
- Wazuh provides host intrusion detection, file integrity monitoring, vulnerability detection, log aggregation, and Elastic-based dashboards in a unified platform.

### Options Considered
- Deploy Wazuh (Manager + Dashboard)
- Use the ELK Stack (Elastic, Logstash, Kibana) alone
- Use Graylog
- Use Splunk Free / Community edition
- Rely on pfSense + system logs alone

### Decision
Deploy **Wazuh** as the primary security monitoring, log aggregation, and SIEM-style platform within the home lab for centralized visibility and incident detection.

### Rationale
**Pros:**
- Comprehensive security platform combining SIEM, HIDS, FIM, vulnerability detection, and compliance modules
- Native agent support for Linux, Windows, macOS, and containers
- Tight integration with Elastic stack for powerful search and visualization
- Unified dashboard for alerts, events, vulnerabilities, and system inventory
- Free and open-source with strong documentation
- Supports pfSense, Proxmox, TrueNAS, and Linux agent-based monitoring
- Ideal for cybersecurity study and hands-on blue-team practice

**Cons:**
- Resource-heavy (Elastic stack requires CPU/RAM tuning)
- More complex initial setup compared to simple log collectors
- Requires ongoing maintenance of indices, dashboards, and agent versions
- Generates large volumes of logs; needs proper storage planning

### Consequences
- Centralized visibility of events across the entire home lab environment
- Strong foundation for security analysis, alerting, and incident response practice
- Ability to simulate and analyze real-world attack patterns
- Increased resource usage within the Wazuh VM and Elastic components
- Requires tuning to prevent excessive alert noise or unnecessary logging

### Alternatives Considered
- **Elastic Stack alone** – powerful but lacks built-in security modules without heavy customization
- **Graylog** – simpler but weaker security and compliance feature set
- **Splunk Free** – excellent UI but heavily limited in free-tier data ingestion
- **Native system logs** – insufficient for correlation, security detection, or centralized monitoring

---

## ADR-###: Deploy Home Assistant OS for Home Automation Platform (2025-11-15)

### Status
Accepted

### Context
A centralized, reliable automation platform is needed to manage IoT devices, sensors, routines, scenes, and smart-home integrations.  

Several architectural choices existed:

- Run Home Assistant in a Docker container  
- Run Home Assistant in a VM on a general-purpose Linux OS  
- Use Home Assistant Core (manual Python install)  
- Use Home Assistant OS (appliance-style system)  
- Host on dedicated physical hardware  
- Host as a VM on Proxmox  

Key considerations included:
- Stability and simplicity  
- Ease of backup and restore  
- Long-term maintainability  
- Integration support (Zigbee, Z-Wave, Matter, WiFi devices)  
- Isolation from other services  
- Update reliability  
- Alignment with homelab infrastructure standards  

### Decision
Deploy **Home Assistant OS (HAOS)** as a **dedicated VM** on the Proxmox virtualization platform.

### Rationale
**Pros**
- Appliance-style OS designed specifically for Home Assistant  
- Simplified lifecycle management (updates, add-ons, snapshots)  
- Strong isolation compared to Docker or mixed-use hosts  
- Works cleanly with Proxmox backup jobs and VM snapshots  
- Reliable upgrade path with low risk of breakage  
- Supports add-ons (ESPHome, Mosquitto, Zigbee2MQTT, etc.)  
- Ideal for long-term “set-and-forget” operation  
- No underlying OS maintenance required  
- Consistent behavior across hardware and VM environments  

**Cons**
- Limited OS-level shell access (appliance-style restrictions)  
- Less flexible than Docker-based deployments  
- Migration between hypervisors requires full image export/import  
- Slightly higher resource overhead than a containerized setup  

### Consequences
- The Home Automation Service will run exclusively on this VM  
- Backups will use *both* Home Assistant Snapshots and Proxmox backups  
- Integrations (Zigbee, WiFi, Matter, etc.) will depend on this stable VM  
- Updates will be handled via HAOS UI (monthly recommended)  
- Z-Wave/Zigbee adapters may require USB passthrough configuration in Proxmox  
- Future scaling (Matter bridges, extra integrations) will remain compatible  

### Alternatives Considered
- **Docker container:** More flexibility but requires managing the host OS, manual add-ons, and additional maintenance overhead  
- **Home Assistant Core:** Most complex; no add-on system; harder to maintain long-term  
- **Dedicated physical device (Pi / Mini PC):** Good isolation but less efficient than using existing Proxmox resources  
- **General-purpose Linux VM + Home Assistant Supervised:** Not officially supported; higher breakage risk  

---

