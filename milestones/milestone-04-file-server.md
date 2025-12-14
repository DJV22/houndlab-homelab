# Milestone 04 — File Server

## Objective
Deploy a reliable, secure, and scalable file server for HoundLab to provide
centralized storage for documents, media, backups, and application data.

This milestone focuses on storage presentation and access, not backup policies
or media indexing (handled in later milestones).

---

## Scope

### Included
- File server deployment
- ZFS dataset usage
- Network file sharing (SMB and optional NFS)
- Permissions and access control

### Excluded
- Media server configuration
- Automated backups
- Offsite replication

---

## Design Overview

### Deployment Model
- **Type:** LXC container
- **Base OS:** Debian (latest stable)
- **Storage Tier:** Tier 2 (external ZFS pool)

### Rationale
- LXC provides near-native performance
- Separation from Proxmox host improves safety
- External ZFS pool supports large, expandable storage

---

## Storage Layout

### ZFS Pool
- **Pool Name:** `houndpool`
- **Location:** External 6-bay USB enclosure
- **Configuration:** RAIDZ1 (initial)

### Recommended Datasets
```
houndpool/
├── files
├── media
├── backups
└── archives
```

### Dataset Properties (Recommended)
- `compression=lz4`
- `atime=off`
- `xattr=sa`
- `recordsize=1M` (media datasets)

---

## Container Configuration

### Resource Allocation
- CPU: 2 cores
- RAM: 2–4GB
- Storage: Minimal root disk (8–16GB)

### Mount Points
Bind-mount ZFS datasets into the container:
```
/mnt/files
/mnt/media
/mnt/backups
/mnt/archives
```

---

## Network Configuration

- Static IP or DHCP reservation
- Registered in Pi-hole local DNS
- Hostname example:
  - `fileserver.houndlab.local`

---

## File Sharing Services

### SMB (Primary)
- Provides compatibility with Windows, macOS, and Linux
- Recommended for most use cases

#### Shares
| Share Name | Path | Purpose |
|----------|------|---------|
| Files | /mnt/files | General storage |
| Media | /mnt/media | Media content |
| Backups | /mnt/backups | Backup targets |

---

### NFS (Optional)
- For Linux-based services and containers
- Higher performance for trusted hosts

---

## User & Permission Model

### Principles
- No anonymous access
- Least-privilege users
- Group-based permissions

### Suggested Groups
- `files_rw`
- `media_rw`
- `backups_rw`

---

## Security Considerations

- Restrict SMB/NFS to LAN only
- Disable guest access
- Strong passwords or key-based auth
- Regular OS updates

---

## Testing & Validation

### Functional Tests
- Mount shares from Windows and Linux clients
- Read/write verification
- Permission enforcement testing

### Resilience Tests
- Container restart
- Proxmox host reboot
- USB enclosure power cycle

---

## Documentation Requirements

- Dataset layout documented
- Share names and paths recorded
- User/group mappings documented

---

## Completion Criteria

This milestone is complete when:
- File server container is running
- ZFS datasets are mounted correctly
- SMB shares accessible from LAN
- Permissions enforced correctly
- System recovers cleanly after reboot

---

## Next Milestone

Proceed to **Milestone 05 — Web Server** once file storage is stable.
