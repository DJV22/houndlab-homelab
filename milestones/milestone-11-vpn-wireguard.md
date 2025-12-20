# Milestone 11 — VPN & Secure Remote Connectivity (WireGuard)

## Objective
Provide secure, encrypted remote access to **HoundLab** using a self-hosted VPN.
This milestone enables safe access to internal services from outside the LAN
without exposing services publicly, while remaining fully free and open source.

---

## Design Goals
- Zero trust over the public internet
- No port exposure beyond VPN endpoint
- Simple client configuration
- High performance and low overhead
- Easy revocation and rotation of access

---

## Technology Selection

- **VPN:** WireGuard
- **Deployment Type:** Proxmox LXC container (preferred) or host-level
- **Authentication:** Public/private key pairs
- **Encryption:** ChaCha20-Poly1305

### Why WireGuard
- Modern, minimal codebase
- Excellent performance
- Widely supported across platforms
- Fully open source
- Easy to audit and maintain

---

## Deployment Model

### Option A — Dedicated LXC (Recommended)
- Isolated from Proxmox host
- Easier lifecycle management
- Cleaner security boundaries

### Option B — Proxmox Host
- Fewer moving parts
- Slightly tighter coupling
- Acceptable for small environments

This milestone assumes **Option A**.

---

## Container Configuration

| Resource | Recommendation |
|--------|----------------|
| CPU | 1–2 cores |
| RAM | 512 MB – 1 GB |
| Disk | 8–16 GB |
| Network | Static IP |

**Hostname Example:**  
`vpn.houndlab.local`

---

## Network Design

### VPN Address Space
```
10.10.10.0/24
```

- Non-overlapping with LAN
- Reserved for VPN clients only

### Routing
- VPN clients can access internal services
- No LAN-to-VPN initiation by default
- Internet-bound traffic is not routed through VPN unless explicitly enabled

---

## WireGuard Configuration

### Server
- Generate server keys
- Listen on a single UDP port
- Define peers explicitly

### Client Peers
- One peer per device
- Unique key pair per peer
- Optional persistent keepalive

Example peer concept:
```
Laptop
Phone
Tablet
Admin workstation
```

---

## Firewall Rules

### VPN Container
- Allow UDP WireGuard port
- Allow forwarding to internal subnets
- Deny all other inbound traffic

### Proxmox Host
- Forward VPN port to container
- Block VPN access to Proxmox management unless explicitly allowed

---

## DNS Integration

- VPN clients use Pi-hole as DNS
- Internal DNS resolution available
- No DNS leakage to external resolvers

---

## Security Considerations

- No shared keys
- Easy peer revocation
- No passwords
- Minimal attack surface
- Logs reviewed periodically

---

## Backup & Recovery

- Backup WireGuard config files
- Store private keys securely
- Document peer inventory
- Test restore procedure

---

## Testing & Validation

- Client connects successfully
- Internal services reachable
- DNS resolves internal names
- Disconnected clients lose access
- Revoked peers cannot reconnect

---

## Documentation Requirements

- VPN subnet
- Listening port
- Peer list (device names only)
- Key rotation procedure

---

## Completion Criteria

This milestone is complete when:
- WireGuard is operational
- At least one remote client can connect
- Internal services are accessible securely
- No internal services are exposed publicly

---

## Next Milestone

Proceed to **Milestone 12 — Monitoring & Alerting**.
