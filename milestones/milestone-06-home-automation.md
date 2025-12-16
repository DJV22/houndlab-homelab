# Milestone 06 — Home Automation

## Objective
Deploy a reliable and extensible home automation platform within HoundLab to
centrally manage smart devices, automations, and integrations while maintaining
security, stability, and local control.

This milestone focuses on the core home automation platform and hardware access.
Device-specific automations and dashboards are configured incrementally afterward.

---

## Scope

### Included
- Home automation platform deployment
- Hardware passthrough planning (USB, radios)
- Network and DNS integration
- Backup and recovery considerations

### Excluded
- Complex automation logic
- Dashboard customization
- Cloud-based integrations (optional later)

---

## Platform Selection

### Primary Platform
- **Software:** Home Assistant (open source)
- **Deployment Type:** Virtual Machine (preferred)

### Rationale for VM
- Simplifies hardware passthrough (USB, Zigbee, Z-Wave)
- Better isolation for frequent updates
- Reduced risk of container privilege issues
- Widely recommended by Home Assistant maintainers

---

## Deployment Architecture

### Virtual Machine Configuration
- **OS:** Home Assistant OS
- **CPU:** 2–4 cores
- **RAM:** 4–8GB
- **Storage:** 32–64GB (NVMe, Tier 1)

### Networking
- Bridged networking via `vmbr0`
- Static IP or DHCP reservation
- Example hostname:
  - `homeassistant.houndlab.local`

---

## Hardware Integration

### USB Passthrough
- Zigbee coordinators
- Z-Wave controllers
- Bluetooth adapters (if required)

#### Proxmox Configuration
- Enable IOMMU in BIOS
- Pass USB devices directly to VM
- Avoid USB hubs where possible

---

## DNS & Network Integration

### Internal DNS
Add a Pi-hole DNS entry:
```
homeassistant.houndlab.local → x.x.x.40
```

### Access Model
- Web UI accessible only from LAN
- No direct WAN exposure

---

## Update Strategy

- Home Assistant OS handles supervised updates
- Snapshot VM before major upgrades
- Schedule updates during maintenance windows

---

## Backup Strategy

### Snapshot Backups
- Proxmox snapshots before updates
- Regular VM backups to external ZFS pool

### Home Assistant Backups
- Enable built-in full backups
- Store copies on file server or external storage

---

## Security Considerations

- Local-only access by default
- Strong admin credentials
- Disable unused integrations
- Keep OS and firmware updated

---

## Testing & Validation

### Functional Tests
- Web UI accessibility
- Hardware device detection
- Automation execution

### Resilience Tests
- VM restart
- Proxmox host reboot
- USB device reconnection validation

---

## Documentation Requirements

- Record hardware devices passed through
- Document IP address and hostname
- List enabled integrations

---

## Completion Criteria

This milestone is complete when:
- Home Assistant VM is operational
- Hardware devices are detected
- Web UI is reachable via hostname
- Backups are verified
- System recovers cleanly after reboot

---

## Next Milestone

Proceed to **Milestone 07 — Media Server** once home automation is stable.
