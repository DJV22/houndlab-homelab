# Milestone 05 — Web Server

## Objective
Deploy a secure, maintainable web server environment within HoundLab to host
internal and limited external websites. This milestone establishes a clean web
stack with proper isolation, DNS integration, and TLS handling.

---

## Scope

### Included
- Web server container deployment
- Reverse proxy configuration
- Internal DNS integration
- TLS strategy (internal-first)

### Excluded
- Application-specific deployments
- CI/CD pipelines
- Public CDN or WAF services

---

## Design Overview

### Deployment Model
- **Type:** LXC container
- **Base OS:** Debian (latest stable)
- **Storage Tier:** Tier 1 (internal NVMe ZFS mirror)

### Rationale
- LXC offers low overhead and fast startup
- Isolates web workloads from other services
- NVMe ensures responsive web performance

---

## Web Stack Components

### Reverse Proxy
- **Software:** NGINX (open source)
- **Responsibilities:**
  - HTTP/HTTPS termination
  - TLS handling
  - Virtual host routing
  - Optional backend proxying

### Backend Options
- Static content served directly by NGINX
- Optional PHP-FPM for dynamic content
- Application backends hosted in separate containers if needed

---

## Container Configuration

### Resource Allocation
- CPU: 2 cores
- RAM: 2GB
- Disk: 16–32GB root filesystem

### Networking
- Static IP or DHCP reservation
- Registered in Pi-hole DNS
- Example hostname:
  - `web.houndlab.local`

---

## DNS Integration

### Internal DNS
Create a local DNS record in Pi-hole:
```
web.houndlab.local → x.x.x.30
```

### External DNS (Optional)
- Only required if services are intentionally exposed
- Prefer split-horizon DNS where supported

---

## TLS Strategy

### Internal TLS
- Use self-signed certificates or an internal Certificate Authority
- Trust the internal CA on client devices
- Enforce HTTPS for all internal sites

### External TLS (Optional)
- ACME-compatible certificates (e.g., Let's Encrypt)
- Automated renewal
- HTTPS-only exposure

---

## Access Control

### Internal-Only Services
- LAN-restricted access
- No port forwarding
- Firewall rules enforced at router or host

### External Access (If Required)
- Reverse proxy only
- Strong authentication
- Rate limiting enabled

---

## Security Hardening

- Minimal OS packages installed
- Automatic security updates enabled
- Secure NGINX headers
- Optional Fail2ban integration
- Regular configuration backups

---

## Testing & Validation

### Functional Tests
- Access site via internal hostname
- Verify HTTPS and certificate trust
- Confirm correct virtual host routing

### Resilience Tests
- Container restart
- Proxmox host reboot
- Certificate renewal test (if applicable)

---

## Documentation Requirements

- List hosted sites and domains
- Document TLS approach used
- Record DNS entries and IP assignments

---

## Completion Criteria

This milestone is complete when:
- Web server container is operational
- Internal DNS resolves correctly
- TLS is functioning as intended
- Web content is reachable
- System recovers cleanly after reboot

---

## Next Milestone

Proceed to **Milestone 06 — Home Automation** once the web server is stable.
