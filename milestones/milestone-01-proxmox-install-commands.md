# Milestone 01 — Proxmox VE Installation (Step-by-Step Command Guide)

> **Aligned with:** `milestone-01-proxmox-install.md`  
> **Project:** HoundLab Homelab  
> **License:** Open-source / community use  

---

## Objective
Install and prepare a **stable, production-ready Proxmox VE host** for HoundLab using mirrored ZFS storage, current best practices, and only free/open-source components.

---

## Hardware Summary
- **Host:** Minisforum UM890 PRO
- **Memory:** 96 GB RAM
- **Boot Storage:** 2 × 4 TB NVMe (internal)
- **External Storage:** Not configured in this milestone

---

## Step 1 — BIOS / Firmware Configuration

Enter BIOS using **DEL** or **F2** during boot.

### Required Settings

| Setting | Value |
|------|------|
| Secure Boot | Disabled |
| Boot Mode | UEFI |
| Virtualization (SVM) | Enabled |
| IOMMU | Enabled |
| Fast Boot | Disabled |

Save and exit.

---

## Step 2 — Proxmox VE Installer Preparation

1. Download the **latest stable Proxmox VE ISO**
   - proxmox-ve_9.1-1.iso
2. Create a bootable USB using:
   - Rufus (Windows)

Use:
- Partition scheme: GPT
- Target system: UEFI
- Write mode: DD (if prompted)

---

## Step 3 — Install Proxmox VE

1. Boot from USB
2. Select **Install Proxmox VE**
3. Accept the license agreement

---

## Step 4 — ZFS Storage Configuration

### Select Filesystem
- Choose **ZFS (RAID1)**

### Disk Selection
- Select both 4 TB NVMe drives

### ZFS Options

| Option | Value |
|------|------|
| ashift | 12 |
| Compression | lz4 (enabled) |
| Encryption | Disabled |

This creates the `rpool` mirrored ZFS pool.

---

## Step 5 — Network Configuration

### Hostname
```
houndlab.local
```

### Static IP (Example)

| Setting | Value |
|------|------|
| IP Address | 192.168.1.2 |
| Gateway | 192.168.1.1 |
| DNS | Temporary (router or public) |

DNS will be migrated to Pi-hole in later milestones.

---

## Step 6 — Root Credentials & Email

- Set a strong root password
- Enter an admin email address for alerts

---

## Step 7 — Initial Login

After reboot, access:
```
https://192.168.1.2:8006
```

Login as:
- User: root
- Realm: pam

---

## Step 8 — System Update

```bash
apt update
apt full-upgrade -y
pveam update
reboot
```

---

## Step 9 — Enable Community Repository

### Add No-Subscription Repo
```bash
nano /etc/apt/sources.list.d/pve-no-subscription.list
```

Add:
```text
deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription
```

### Disable Enterprise Repo
```bash
nano /etc/apt/sources.list.d/pve-enterprise.list
```

Comment out:
```text
# deb https://enterprise.proxmox.com/debian/pve bookworm pve-enterprise
```

### Update
```bash
apt update && apt full-upgrade -y
```

---

## Step 10 — Create ZFS Datasets

```bash
zpool status
zfs create rpool/containers
zfs create rpool/vms
zfs create rpool/backups
```

---

## Step 11 — Proxmox Storage Configuration

In **Datacenter → Storage**, ensure:

| Name | Type | Target |
|----|----|----|
| local-zfs | ZFS | rpool |
| backups | Directory | /rpool/backups |

---

## Step 12 — Email Notifications

```bash
apt install postfix mailutils -y
```

Select **Internet Site** during setup.

Test:
```bash
echo "HoundLab Proxmox test email" | mail -s "Proxmox Test" you@example.com
```

---

## Step 13 — Backup Job (Placeholder)

Create a scheduled backup:
- Mode: Snapshot
- Compression: ZSTD
- Schedule: Daily
- Target: backups

(External storage added later.)

---

## Step 14 — Firewall Baseline

Enable:
- Datacenter Firewall
- Node Firewall

No rules defined yet.

---

## Validation Checklist

- [ ] Proxmox web UI accessible
- [ ] ZFS mirror healthy
- [ ] Updates complete
- [ ] Community repo active
- [ ] Firewall enabled
- [ ] Storage pools visible

---

## Completion Criteria

Milestone 01 is complete when:
- Proxmox VE is stable and updated
- ZFS mirror is active
- System is ready for networking and storage expansion

---

## Next Milestone

➡ **Milestone 02 — Storage Architecture & Expansion**
