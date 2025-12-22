# Milestone 03 — Networking, DNS & Pi-hole (Command Guide)

> **Aligned with:** `milestone-03-networking-dns.md`  
> **Project:** HoundLab Homelab  
> **Focus:** Internal DNS, Ad-blocking, Network Foundation  
> **License:** Free & Open Source Only

---

## Objective
Deploy a **centralized DNS and network foundation** for HoundLab using:
- Pi-hole (ad-blocking + DNS)
- Static IP addressing
- Internal DNS namespace (`houndlab.local`)
- Proxmox-aware networking

This milestone establishes **name resolution and traffic control** for all future services.

---

## Network Assumptions

| Item | Value |
|----|----|
| LAN Subnet | 192.168.10.0/24 |
| Gateway | 192.168.10.1 |
| Proxmox Host | 192.168.10.10 |
| Pi-hole | 192.168.10.20 |
| Domain | houndlab.local |

---

## Step 1 — Create Pi-hole LXC Container

### Download Debian Template
```bash
pveam update
pveam available | grep debian
pveam download local debian-12-standard_*.tar.zst
```

### Create Container
```bash
pct create 120 local:vztmpl/debian-12-standard_*.tar.zst \
  --hostname pihole \
  --cores 2 \
  --memory 2048 \
  --swap 512 \
  --net0 name=eth0,bridge=vmbr0,ip=192.168.10.20/24,gw=192.168.10.1 \
  --rootfs local-zfs:8 \
  --unprivileged 1 \
  --features nesting=1
```

Start container:
```bash
pct start 120
pct enter 120
```

---

## Step 2 — Install Dependencies

```bash
apt update && apt full-upgrade -y
apt install -y curl sudo dnsutils
```

---

## Step 3 — Install Pi-hole

Run official installer:
```bash
curl -sSL https://install.pi-hole.net | bash
```

### Installer Choices
- Interface: `eth0`
- Static IP: **Keep current**
- Upstream DNS: Cloudflare (1.1.1.1) or Quad9
- Blocklists: Default
- Web UI: Enabled
- Logging: Enabled

---

## Step 4 — Secure Pi-hole Admin Interface

```bash
pihole -a -p
```

Set a strong admin password.

Access UI:
```
http://192.168.10.20/admin
```

---

## Step 5 — Configure Local DNS Records

Edit custom DNS:
```bash
nano /etc/pihole/custom.list
```

Example entries:
```
192.168.10.10 pve01.houndlab.local
192.168.10.20 pihole.houndlab.local
192.168.10.30 fileserver.houndlab.local
192.168.10.40 web.houndlab.local
```

Restart DNS:
```bash
pihole restartdns
```

---

## Step 6 — Configure Conditional Forwarding (Optional)

In Pi-hole Web UI:
- Settings → DNS
- Enable **Conditional Forwarding**
- Router IP: `192.168.10.1`
- Domain: `houndlab.local`

---

## Step 7 — Update Router DNS Settings

On your router:
- Set **Primary DNS** → `192.168.10.20`
- Remove ISP DNS
- Apply to DHCP clients

---

## Step 8 — Point Proxmox to Pi-hole

Edit resolv.conf:
```bash
nano /etc/resolv.conf
```

Set:
```
nameserver 192.168.10.20
search houndlab.local
```

Optional lock:
```bash
chattr +i /etc/resolv.conf
```

---

## Step 9 — Verify DNS Resolution

From Proxmox:
```bash
dig pve01.houndlab.local
dig google.com
```

From a client:
```bash
nslookup pihole.houndlab.local
```

---

## Step 10 — Enable Pi-hole as DHCP (Optional)

```bash
pihole -a enabledhcp
```

Configure range:
- 192.168.10.100 – 192.168.10.200
- Gateway: 192.168.10.1

---

## Step 11 — Firewall Baseline

Allow internally:
- TCP/UDP 53
- TCP 80 (admin)

No WAN exposure.

---

## Validation Checklist

- [ ] Pi-hole container running
- [ ] Internal DNS resolving
- [ ] Clients using Pi-hole
- [ ] Ads blocked
- [ ] Admin UI secured

---

## Completion Criteria

Milestone 03 is complete when:
- Pi-hole is primary DNS
- Internal hostnames resolve
- Network filtering is active

---

## Next Milestone

➡ **Milestone 04 — File Server**
