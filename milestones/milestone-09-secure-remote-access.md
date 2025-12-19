# Milestone 09 — Secure Remote Access (SSH & Tunneling)

## Objective
Provide secure, controlled remote access to **HoundLab** for administration and
limited service exposure without opening unnecessary ports or relying on
cloud-based services. This milestone prioritizes security, auditability,
and least-privilege access.

---

## Scope

### Included
- SSH hardening
- Key-based authentication
- Bastion / jump-host model
- SSH tunneling and port forwarding
- Internal-only service access

### Excluded
- Public web exposure
- VPN configuration (covered in a future milestone)
- Third-party remote access services

---

## Access Model Overview

### Bastion Host Concept
- One hardened system is exposed to the network
- All management access flows through this host
- No direct access to internal containers/VMs

**Recommended Bastion:** Proxmox host or dedicated management container

---

## SSH Configuration

### Authentication
- Disable password authentication
- Require SSH key-based login
- Use modern key types (ed25519 preferred)

### Hardening Settings
Recommended `/etc/ssh/sshd_config`:
```
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
AllowUsers adminuser
MaxAuthTries 3
```

Restart SSH after changes.

---

## Key Management

### Local Key Generation
Generate keys on the admin workstation:
```
ssh-keygen -t ed25519 -C "houndlab-admin"
```

### Key Distribution
- Copy public keys to bastion host only
- Use `ssh-copy-id` or manual install
- Remove unused or compromised keys immediately

---

## Network Restrictions

- Limit SSH to management subnet if possible
- Use firewall rules to restrict source IPs
- Log all SSH access attempts

---

## SSH Tunneling

### Use Cases
- Access internal web UIs (Proxmox, Jellyfin, Home Assistant)
- Temporary access without exposing services publicly

### Example: Local Port Forward
```
ssh -L 8080:internal-host:80 adminuser@bastion
```

Access service via:
```
http://localhost:8080
```

---

## Proxmox-Specific Considerations

- Use Proxmox firewall to restrict SSH
- Disable root SSH login
- Use sudo for privileged operations
- Keep Proxmox and SSH packages updated

---

## Logging & Auditing

- Enable SSH logging
- Review `/var/log/auth.log`
- Monitor failed login attempts
- Consider fail2ban (optional, open source)

---

## Backup & Recovery

- Backup SSH configuration files
- Store private keys securely
- Document key rotation procedures

---

## Testing & Validation

- Password login attempts fail
- SSH key access works
- Tunnels connect successfully
- Unauthorized IPs are blocked
- Bastion reboot does not break access

---

## Documentation Requirements

- Bastion hostname/IP
- Allowed admin users
- Key rotation schedule
- Firewall rules summary

---

## Completion Criteria

This milestone is complete when:
- SSH access is key-only
- Bastion model is enforced
- Internal services are accessible via tunnels
- No unnecessary ports are exposed

---

## Next Milestone

Proceed to **Milestone 10 — IP Addressing & Network Segmentation (HoundLab)**.
