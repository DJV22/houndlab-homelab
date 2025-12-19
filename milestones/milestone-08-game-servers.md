# Milestone 08 — Game Servers

## Objective
Deploy and manage self-hosted game servers within **HoundLab** for personal and
friends-and-family use. This milestone focuses on flexibility, isolation,
performance, and easy rebuilds while remaining fully free and open source.

---

## Scope

### Included
- Game server hosting architecture
- LXC vs VM decision guidance
- Networking and port management
- Backup and rebuild strategy

### Excluded
- Public server listing
- Anti-cheat or mod-heavy tuning
- Monetization features

---

## Deployment Strategy

### Container-First Approach
- **Preferred:** Proxmox LXC containers
- **Fallback:** Virtual Machines (when required)

#### Why LXC
- Low overhead
- Fast startup
- Easy cloning and rollback
- Ideal for Linux-based dedicated servers

#### When to Use a VM
- Games requiring Windows
- Kernel-level anti-cheat
- Complex mod loaders with system dependencies

---

## Common Game Server Stack

### Core Components
- SteamCMD
- Game-specific server binaries
- systemd service files
- Firewall rules

All components are free and open source.

---

## Resource Planning

### Baseline Container Template
| Resource | Recommendation |
|--------|----------------|
| CPU | 2–4 cores |
| RAM | 4–8 GB |
| Disk | 20–50 GB |
| Network | Bridged (vmbr0) |

Adjust per-game requirements.

---

## Storage Layout

### Server Data
- Store server files on NVMe (Tier 1)
- Optional bind mount for persistent saves

Example:
```
/srv/gameservers/<game-name>/
```

### Backup Targets
- World data
- Configuration files
- Player data

---

## Networking & Access

### IP Strategy
- One container per game server
- Static IP or DHCP reservation

### Port Management
- Document all exposed ports
- Avoid overlapping port ranges
- Restrict access via firewall rules

---

## DNS Integration

Optional DNS entries for convenience:
```
minecraft.houndlab.local
valheim.houndlab.local
```

DNS is optional; direct IP access is acceptable.

---

## Security Considerations

- Run servers as non-root users
- LAN-only exposure by default
- Strong admin passwords
- Regular OS updates
- Disable unused ports

---

## Backup & Recovery Strategy

### Snapshot Model
- Snapshot containers before updates
- Weekly automated backups

### Rebuild Philosophy
- Containers should be disposable
- Restore saves, not entire OS
- Document rebuild steps

---

## Monitoring & Maintenance

- Monitor CPU/RAM usage via Proxmox
- Watch disk growth
- Restart policies for crashes

---

## Testing & Validation

- Server starts automatically on boot
- Clients can connect and play
- Save data persists across restarts
- Container rebuild test passes

---

## Documentation Requirements

- Game name and version
- Ports used
- Save locations
- Resource allocations

---

## Completion Criteria

This milestone is complete when:
- At least one game server is operational
- Clients can connect reliably
- Backups are verified
- Rebuild process is documented

---

## Next Milestone

Proceed to **Milestone 09 — Secure Remote Access (SSH & Tunneling)**.
