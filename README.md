# HoundLab Homelab

HoundLab is a personal, self-hosted homelab built on open-source software and commodity hardware.
The goal is to provide a **secure, scalable, and well-documented environment** for hosting services
such as websites, file storage, media, home automation, and game servers.

This repository serves as the **authoritative documentation** for designing, building, operating,
and expanding the HoundLab environment.

---

## Project Goals

- Use **free and open-source software** wherever possible
- Maintain clear separation between host, services, and storage
- Enable easy recovery and rebuild
- Support future expansion without redesign
- Prioritize security and reliability over convenience

---

## High-Level Architecture

- **Host:** MinisForum UM890 PRO (96GB RAM)
- **Hypervisor:** Proxmox Virtual Environment (latest stable)
- **Primary Storage:** ZFS mirror on internal NVMe SSDs
- **Bulk Storage:** ZFS pool on external 6-bay USB enclosure
- **Workloads:** LXC containers and select virtual machines
- **Access:** Internal LAN with secure remote access only

For a full system overview, see [`ARCHITECTURE.md`](ARCHITECTURE.md).

---

## Repository Structure

```
houndlab-homelab/
├── README.md              # This file
├── ARCHITECTURE.md        # High-level system design
├── milestones/            # Step-by-step build phases
├── services/              # Individual service documentation
├── storage/               # ZFS, disks, expansion plans
├── network/               # DNS, IP plans, firewall rules
├── scripts/               # Automation and helper scripts
└── backups/               # Backup strategy documentation
```

---

## Milestones

Milestones represent **major build phases** and should be completed in order.

- **Milestone 01:** Proxmox VE Installation
- **Milestone 02:** Storage Architecture & Expansion
- **Milestone 03:** Networking, DNS, and Pi-hole
- **Milestone 04:** File Server
- **Milestone 05:** Web Server
- **Milestone 06:** Home Automation
- **Milestone 07:** Media Server
- **Milestone 08:** Game Servers
- **Milestone 09:** Secure Remote Access & Hardening

See the [`milestones/`](milestones/) directory for details.

---

## Service Philosophy

- Prefer **LXC containers** for efficiency
- Use **VMs only when required**
- One service per container/VM
- Minimal base images
- Explicit documentation for every service

---

## Backup & Recovery

Backups are treated as a **first-class requirement**:
- Regular snapshots
- External storage replication
- Clear restore procedures

Details are documented under `storage/` and `backups/`.

---

## Security Principles

- No unnecessary public exposure
- Least-privilege containers
- Encrypted access channels
- Regular updates and audits

---

## Status

This project is actively evolving.
Changes are tracked through milestone updates and documentation revisions.

---

## License

Documentation and scripts in this repository are released under the **MIT License**
unless otherwise noted.
