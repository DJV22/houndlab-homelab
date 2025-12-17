# Milestone 07 — Media Server

## Objective
Deploy a self-hosted, open-source media server within **HoundLab** to manage and stream
movies, TV shows, music, and personal media. This milestone prioritizes privacy,
local control, and reliable performance.

---

## Platform Selection

- **Media Server:** Jellyfin (open source)
- **Deployment Type:** Proxmox LXC Container
- **Base OS:** Debian (latest stable)

### Why Jellyfin
- Fully free and open source
- No cloud accounts or telemetry
- Broad client support (web, mobile, smart TVs)
- Active community and plugin ecosystem

---

## Container Configuration

| Resource | Recommendation |
|--------|----------------|
| CPU | 2–4 cores |
| RAM | 4–8 GB |
| Root Disk | 16–32 GB (NVMe) |
| Network | Static IP or DHCP reservation |

**Hostname Example:**  
`media.houndlab.local`

---

## Storage Architecture

### Media Location
- Stored on **external ZFS pool** hosted in the 6‑bay USB enclosure
- Pool example: `houndpool`

### Recommended Dataset
```
houndpool/media
```

### Dataset Properties
- `compression=lz4`
- `atime=off`
- `recordsize=1M`

### Mount into Container
Bind mount:
```
/mnt/media
```

---

## Library Structure

Recommended directory layout:
```
/mnt/media/
├── Movies/
├── TV/
├── Music/
└── HomeVideos/
```

Consistent naming improves metadata accuracy and scraping.

---

## Networking & Access

- LAN-only access by default
- DNS entry managed by Pi-hole
- Access via:
  - Web browser
  - Mobile apps (Android / iOS)
  - Smart TVs
  - Desktop clients

No public internet exposure at this stage.

---

## Hardware Acceleration (Optional)

### Options
- Intel iGPU passthrough (if available)
- Software transcoding fallback

> Hardware acceleration can be added later without redesigning the container.

---

## Security Considerations

- Strong admin password
- Disable unused plugins
- Keep Jellyfin and OS updated
- Restrict access to trusted subnets

---

## Backup Strategy

### Media Files
- Media is typically reproducible (optional backup)

### Critical Data
- Jellyfin configuration
- Metadata and user data

Include Jellyfin config directory in Proxmox backups.

---

## Testing & Validation

- Media libraries scan successfully
- Clients can stream content
- Playback performance is acceptable
- Container and host reboot cleanly

---

## Documentation

- Record dataset paths
- Note hostname and IP address
- Document any transcoding configuration

---

## Completion Criteria

This milestone is complete when:
- Jellyfin is operational
- Media libraries are available
- Clients can stream reliably
- System recovers after reboots

---

## Next Milestone

Proceed to **Milestone 08 — Game Servers**.
