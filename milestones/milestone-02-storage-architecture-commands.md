# Milestone 02 — Storage Architecture & Expansion (Command Guide)

> **Aligned with:** `milestone-02-storage-architecture.md`  
> **Project:** HoundLab Homelab  
> **Focus:** ZFS + External USB DAS (6-bay enclosure)

---

## Objective
Implement a **scalable, fault-tolerant storage architecture** using:
- Internal NVMe ZFS mirror for performance-critical workloads
- External 6-bay USB DAS for media, backups, and archives
- Clean separation between compute and bulk storage

This milestone prepares HoundLab for **future disk expansion** without re-architecting.

---

## Storage Overview

### Internal (Already Complete)
- Pool: `rpool`
- Type: ZFS mirror
- Usage: OS, VMs, LXCs

### External (This Milestone)
- Device: Cennmate 6-bay USB 3.x enclosure
- Drives: 20TB Seagate IronWolf Pro
- Usage:
  - Media
  - Backups
  - Archives

---

## Step 1 — Verify USB Enclosure Detection

Plug in enclosure and power on.

```bash
lsusb
lsblk -o NAME,SIZE,MODEL,TRAN
```

Confirm drives appear as `/dev/sdX` with `TRAN=usb`.

---

## Step 2 — Install Required Tools

```bash
apt update
apt install -y zfsutils-linux smartmontools
```

---

## Step 3 — Identify Drives Safely

```bash
ls -l /dev/disk/by-id/ | grep usb
```

📌 **Always use `/dev/disk/by-id/` paths for USB ZFS pools.**

Example:
```
usb-Seagate_ST20000NT001-xxxx
```

---

## Step 4 — Decide Pool Layout

### Recommended Initial Layout
- **ZFS RAIDZ1**
- Tolerates 1 disk failure
- Best balance of capacity and protection

📌 Can be expanded later by **adding another vdev**.

---

## Step 5 — Create External ZFS Pool

⚠️ **THIS WILL ERASE ALL DATA ON SELECTED DISKS**

Example (3-disk RAIDZ1):

```bash
zpool create -o ashift=12 media-pool raidz1 /dev/disk/by-id/usb-disk1 /dev/disk/by-id/usb-disk2 /dev/disk/by-id/usb-disk3
```

Verify:
```bash
zpool status
zpool list
```

---

## Step 6 — Set ZFS Pool Properties

```bash
zfs set compression=lz4 media-pool
zfs set atime=off media-pool
zfs set xattr=sa media-pool
```

---

## Step 7 — Create Datasets

```bash
zfs create media-pool/media
zfs create media-pool/backups
zfs create media-pool/archives
```

Optional tuning:
```bash
zfs set recordsize=1M media-pool/media
zfs set recordsize=128K media-pool/backups
```

---

## Step 8 — Enable SMART Monitoring

```bash
smartctl -a /dev/sdX
```

Enable periodic checks:
```bash
nano /etc/smartd.conf
```

Example:
```
/dev/sdX -a -o on -S on -s (S/../.././02)
```

Restart:
```bash
systemctl restart smartd
```

---

## Step 9 — Register Storage in Proxmox

### Media
Datacenter → Storage → Add → ZFS
- ID: `media-pool`
- Pool: `media-pool`
- Content: Disk image, Container, ISO, Backup

---

## Step 10 — Backup Integration

Assign:
- Backup jobs → target `media-pool/backups`
- Retention rules per workload

---

## Step 11 — Expansion Strategy (Future-Proof)

### Add New Disks Later
You **cannot expand RAIDZ width**, but you *can* add vdevs.

Example:
```bash
zpool add media-pool raidz1 /dev/disk/by-id/usb-newdisk1 /dev/disk/by-id/usb-newdisk2 /dev/disk/by-id/usb-newdisk3
```

---

## Step 12 — USB Stability Hardening

Create udev rule:
```bash
nano /etc/udev/rules.d/99-usb-zfs.rules
```

Add:
```
ACTION=="add", SUBSYSTEM=="block", ENV{ID_BUS}=="usb", RUN+="/sbin/zpool import -a"
```

Reload:
```bash
udevadm control --reload
```

---

## Validation Checklist

- [ ] USB enclosure visible after reboot
- [ ] ZFS pool imports automatically
- [ ] SMART reports clean
- [ ] Datasets accessible
- [ ] Proxmox recognizes storage

---

## Completion Criteria

Milestone 02 is complete when:
- External ZFS pool is active
- Data is separated by function
- Pool survives reboot
- Expansion path is documented

---

## Next Milestone

➡ **Milestone 03 — Networking, DNS & Pi-hole**
