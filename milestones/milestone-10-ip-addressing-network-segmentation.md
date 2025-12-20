# Milestone 10 — IP Addressing & Network Segmentation (HoundLab)

## Objective
Design and implement a clear, scalable IP addressing plan and basic network
segmentation for **HoundLab** to improve security, manageability, and future growth.
This milestone establishes logical boundaries between services without unnecessary
complexity.

---

## Design Principles
- Predictable IP addressing
- Separation of concerns by service type
- Minimal trust between segments
- Easy troubleshooting
- VLAN-ready without requiring VLANs today

---

## Network Overview

### Physical Network
- Single physical LAN
- Proxmox host bridged via `vmbr0`
- Containers/VMs receive first-class IPs

---

## Addressing Scheme

### Base Network
```
Network: 192.168.50.0/24
Gateway: 192.168.50.1
DNS: Pi-hole
```

---

## IP Allocation Plan

### Infrastructure (Static)
| Device | IP Address |
|------|------------|
| Proxmox Host | 192.168.50.10 |
| Pi-hole DNS | 192.168.50.11 |
| Bastion / SSH | 192.168.50.12 |

---

### Core Services (DHCP Reservations)
| Service | Hostname | IP |
|--------|----------|----|
| File Server | files.houndlab.local | 192.168.50.20 |
| Web Server | web.houndlab.local | 192.168.50.21 |
| Home Assistant | ha.houndlab.local | 192.168.50.22 |
| Media Server | media.houndlab.local | 192.168.50.23 |

---

### Game Servers (Dedicated Range)
```
192.168.50.50 – 192.168.50.99
```
- One IP per server
- Simplifies port management and isolation

---

### Client Devices (Dynamic)
```
192.168.50.100 – 192.168.50.199
```

---

### Reserved / Expansion
```
192.168.50.200 – 192.168.50.254
```

---

## DNS Strategy
- Pi-hole provides authoritative internal DNS
- All services use `.houndlab.local`
- No external DNS dependency

Example records:
```
media.houndlab.local
files.houndlab.local
minecraft.houndlab.local
```

---

## Logical Segmentation Model

### Zones
- **Infrastructure:** Proxmox, DNS, Bastion
- **Services:** Web, media, file, automation
- **Games:** Dedicated game containers
- **Clients:** User endpoints

Segmentation is enforced using:
- Proxmox firewall (host + guest)
- Service-level access controls

---

## Proxmox Firewall Guidance

### Host Level
- Allow SSH only from trusted IPs
- Deny all other inbound traffic

### Guest Level
- Allow only required ports
- Default deny inbound

---

## Future VLAN Planning (Optional)

| VLAN | Purpose |
|------|---------|
| 10 | Infrastructure |
| 20 | Services |
| 30 | Games |
| 40 | Clients |

IP addressing supports VLAN migration without renumbering.

---

## Documentation Requirements
- Maintain IP allocation tables
- Track DNS entries
- Record firewall rules
- Update diagrams on change

---

## Testing & Validation
- DNS resolves all services
- No IP conflicts observed
- Firewall rules enforce isolation
- Services survive host reboots

---

## Completion Criteria
This milestone is complete when:
- IP plan is implemented and documented
- DNS is authoritative and consistent
- Segmentation rules are active
- Expansion ranges are reserved

---

## Next Milestone
Proceed to **Milestone 11 — VPN & Secure Remote Connectivity (WireGuard)**.
