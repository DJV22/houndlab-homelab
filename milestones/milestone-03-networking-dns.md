# Milestone 03 — Networking, DNS, and Pi-hole

## Objective
Establish a stable, secure, and well-documented network foundation for HoundLab.
This milestone focuses on internal networking, DNS resolution, and ad-blocking
to support all current and future services.

---

## Scope

### Included
- IP addressing strategy
- Proxmox network configuration
- Internal DNS design
- Pi-hole deployment and integration

### Excluded
- VPN setup
- Advanced firewall rules
- Public service exposure

These topics are handled in later milestones.

---

## Network Design Overview

### Assumptions
- Single flat LAN (no VLANs initially)
- Router provides DHCP
- Proxmox host uses a static IP
- Containers and VMs may use DHCP or static reservations

---

## IP Addressing Plan

### Recommended Static Assignments

| Device / Service | IP Address | Notes |
|------------------|-----------|-------|
| Router / Gateway | x.x.x.1 | Existing router |
| Proxmox Host | x.x.x.10 | Static |
| Pi-hole | x.x.x.11 | Primary DNS |
| File Server | x.x.x.20 | Optional static |
| Media Server | x.x.x.21 | Optional static |

> Replace `x.x.x` with your LAN subnet (e.g., `192.168.1`).

---

## Proxmox Network Configuration

### Bridge Setup
- Default bridge: `vmbr0`
- Attached to the physical NIC
- Used by all containers and virtual machines

### Validation
1. Log into Proxmox Web UI
2. Navigate to **Node → Network**
3. Verify:
   - Correct static IP
   - Correct gateway
   - DNS reachable
   - Bridge is active after reboot

---

## DNS Strategy

### Goals
- Resolve internal services by hostname
- Centralized DNS control
- Reduce reliance on external DNS for local traffic

### Approach
- Pi-hole functions as:
  - Primary DNS resolver
  - Ad and tracker blocker
  - Local DNS authority

---

## Pi-hole Deployment

### Deployment Method
- LXC container
- Minimal Debian-based template
- Hosted on Tier 1 NVMe storage

### Resource Allocation
- CPU: 1 core
- RAM: 512MB–1GB
- Storage: 8–16GB

---

## Pi-hole Configuration

### Initial Setup
1. Install Pi-hole using the official installer
2. Select upstream DNS providers (Cloudflare, Quad9, etc.)
3. Enable web admin interface
4. Set a strong admin password

### Local DNS Records
Example entries:
```
proxmox.houndlab.local     → x.x.x.10
fileserver.houndlab.local → x.x.x.20
media.houndlab.local      → x.x.x.21
```

---

## Router Integration

### DNS Assignment
- Configure router DHCP to advertise Pi-hole as DNS
- Disable ISP DNS overrides if applicable

### Verification
- Clients appear in Pi-hole dashboard
- Ads and trackers are blocked
- Internal hostnames resolve correctly

---

## Resilience Considerations

### Secondary DNS
- Configure a fallback DNS (router or public resolver)
- Ensures connectivity if Pi-hole is temporarily unavailable

---

## Testing & Validation

### Functional Tests
- Internet access from LAN clients
- DNS resolution from containers and VMs
- Internal hostname resolution

### Reboot Test
- Restart Proxmox host
- Restart Pi-hole container
- Confirm DNS recovers automatically

---

## Documentation Requirements

- Final IP plan recorded
- Pi-hole credentials stored securely
- Any deviations documented

---

## Completion Criteria

This milestone is complete when:
- Proxmox host has a stable static IP
- Pi-hole is serving DNS to the LAN
- Internal hostnames resolve consistently
- Network survives reboot without manual intervention

---

## Next Milestone

Proceed to **Milestone 04 — File Server** once networking and DNS are stable.
