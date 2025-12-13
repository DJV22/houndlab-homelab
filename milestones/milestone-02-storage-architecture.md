# Milestone 02 — Storage Architecture & Expansion Plan

## Goal
Design and implement a scalable, fault-tolerant storage architecture that
separates high-performance workloads from bulk media and archival storage.

---

## Storage Overview

### Internal Storage (Primary)
- 2 × 4TB NVMe SSD
- Installed internally in UM890 PRO
- ZFS RAID1 mirror (`rpool`)

### External Storage (Secondary)
- Cennmate 6-bay USB 3.0 / USB-C enclosure
- Hot-swappable
- Currently populated with:
  - 3 × 20TB Seagate IronWolf Pro

---

## Internal Storage Design

### ZFS Pool: `rpool`

**Purpose**
- VM disks
- LXC containers
- Application data
- High-performance workloads

**Characteristics**
- High IOPS
- Redundant
- Always available
- Not used for bulk media

---

## External Storage Design

### ZFS Pool: `houndpool`
- RAIDZ1 across 3 × 20TB drives
- Connected via USB 3.0
- Mounted persistently on the Proxmox host

**Primary Uses**
- Media storage
- Backup targets
- Long-term archives
- ISO and large-file storage

---

## Why USB Storage Is Acceptable
- Media and backups are sequential workloads
- RAIDZ1 provides single-disk fault tolerance
- External enclosure allows hot-swap
- Separation protects VM storage from heavy I/O

---

## Expansion Strategy

### Short-Term Expansion
- Populate remaining enclosure bays
- Add new ZFS **vdevs** to `houndpool`

### Long-Term Expansion Options
- Create additional ZFS pools
- Add a second external enclosure
- Offload cold data to removable storage

> ⚠️ Existing RAIDZ vdevs cannot be expanded by adding disks.
> Expansion is done by adding new vdevs or pools.

---

## Backup & Data Flow

```
VMs / Containers (rpool)
        ↓
Nightly Backups
        ↓
External ZFS Pool (houndpool)
        ↓
Optional Offsite Rotation
```

---

## Filesystem Access Model
- ZFS datasets bind-mounted into containers
- Media and file servers consume `houndpool`
- Proxmox host retains disk ownership

---

## Validation Checklist
- [x] External enclosure recognized by host
- [x] ZFS pool created successfully
- [x] Mount persists across reboots
- [x] Read/write access verified

---

## Notes
- External storage is not required for host boot
- Removal or failure does not impact Proxmox availability
- Design prioritizes recoverability and simplicity
