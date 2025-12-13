# HoundLab Architecture Overview

## Purpose
This document provides a high-level overview of the HoundLab homelab architecture.
It is intended to explain *how the system is designed* and *why certain decisions
were made*, without focusing on step-by-step installation details.

Detailed setup instructions are documented separately in the `milestones/`,
`services/`, `storage/`, and `network/` directories.

---

## Hardware Layer

### Primary Host
- **Platform:** MinisForum UM890 PRO
- **Memory:** 96GB RAM
- **Role:** Primary virtualization host

### Internal Storage
- 2 × 4TB NVMe SSD
- Installed internally in the UM890 PRO
- Configured as a ZFS RAID1 mirror

### External Storage
- **Enclosure:** Cennmate 6-bay USB 3.0 / USB-C enclosure
- **Drives:** 3 × 20TB Seagate IronWolf Pro (initial)
- **Purpose:** Media, backups, and archival storage
- **Expansion:** Additional drives can be added to unused bays

---

## Virtualization Layer

### Hypervisor
- **Proxmox Virtual Environment** (latest stable release)
- Installed directly on bare metal

### Workload Types
- **LXC Containers**
  - Preferred for most services
  - Lightweight and efficient
- **Virtual Machines**
  - Used when full OS isolation is required
  - Reserved for complex or security-sensitive workloads

---

## Storage Architecture

### Tier 1 — Performance Storage
- **ZFS Pool:** `rpool`
- **Configuration:** RAID1 mirror (NVMe)
- **Used For:**
  - VM disks
  - LXC container storage
  - Application data
- **Characteristics:**
  - High IOPS
  - Low latency
  - Redundant

### Tier 2 — Capacity Storage
- **ZFS Pool:** `houndpool`
- **Configuration:** RAIDZ1 (external USB enclosure)
- **Used For:**
  - Media libraries
  - Backup targets
  - Long-term archives
- **Characteristics:**
  - High capacity
  - Sequential workloads
  - Expandable via additional vdevs or pools

---

## Network Architecture

### Core Networking
- Static IP assigned to Proxmox host
- All services operate on the internal LAN
- No direct public exposure of services

### DNS
- Centralized DNS and ad-blocking via Pi-hole
- Internal hostnames resolved locally

### Remote Access
- Secure access via SSH tunneling or VPN
- No services exposed directly to WAN

---

## Service Placement Overview

| Service Type        | Deployment | Storage Tier |
|---------------------|------------|--------------|
| File Server         | LXC        | Tier 2       |
| Media Server        | LXC        | Tier 2       |
| Web Server          | LXC        | Tier 1       |
| Home Automation     | VM / LXC   | Tier 1       |
| Game Servers        | LXC        | Tier 1       |
| DNS (Pi-hole)       | LXC        | Tier 1       |

---

## Security Boundaries

- Proxmox host remains minimal and stable
- Services are isolated from the host
- Least-privilege principles applied to containers
- External storage mounted and controlled by host
- Backups stored separately from live workloads

---

## Backup Strategy (High-Level)

- Nightly VM and container backups from `rpool`
- Replication to external ZFS pool (`houndpool`)
- Optional offsite or removable media rotation

---

## Design Principles

- Open-source software preferred
- Simplicity over unnecessary complexity
- Clear separation of responsibilities
- Scalable storage design
- Easy recovery and rebuild

---

## Change Policy

This document should be updated when:
- Major services are added or removed
- Storage architecture changes
- Network topology changes
- Additional hosts are introduced

Minor configuration changes should be documented elsewhere.
