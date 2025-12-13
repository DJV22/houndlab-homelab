# Milestone 01 — Proxmox VE Installation

## Goal
Install a stable, open-source virtualization platform to serve as the foundation
for the HoundLab homelab.

This milestone establishes the Proxmox host, internal storage layout, and
baseline system configuration.

---

## Hardware
- MinisForum UM890 PRO
- 96GB RAM
- 2 × 4TB NVMe SSD (internal)

---

## Software
- Proxmox Virtual Environment (latest stable release at time of install)
- ZFS (included with Proxmox)

---

## Pre-Installation Checklist

### BIOS / Firmware Settings
- Secure Boot: **Disabled**
- Boot Mode: **UEFI**
- IOMMU / SVM: **Enabled**
- Virtualization Extensions: **Enabled**
- SATA Mode: **AHCI**
- Power settings: Disable aggressive power saving if present

---

## Installation Summary

### Installation Media
- Proxmox VE ISO written to USB
- Installed directly on bare metal

### Storage Configuration
- Selected **ZFS RAID1 (Mirror)**
- Both 4TB NVMe drives used
- Resulting pool: `rpool`

**Rationale**
- Redundancy against single NVMe failure
- ZFS snapshots and data integrity
- High IOPS for VMs and containers

---

## Network Configuration
- Static IP assigned to Proxmox host
- Hostname configured (e.g., `houndlab-pve`)
- Management access via web UI (port 8006)

---

## Post-Install Tasks

### System Updates
```bash
apt update && apt full-upgrade
pveam update
