# Milestone 04 — File Server (SMB + NFS) Command Guide

> **Aligned with:** `milestone-04-file-server.md`  
> **Project:** HoundLab Homelab  
> **Focus:** Centralized storage access for Linux, Windows, containers, and VMs  
> **License:** Free & Open Source Only

---

## Objective
Deploy a **dedicated file server container** providing:
- SMB (Windows / general use)
- NFS (Linux / Proxmox / containers)
- Backed by ZFS datasets on the external USB pool
- Secure, permission-controlled access

---

## Design Decisions

| Item | Choice |
|----|----|
| Deployment | Proxmox LXC |
| OS | Debian 12 |
| Storage Backend | ZFS datasets |
| Protocols | SMB + NFS |
| Authentication | Local users |
| Network | Internal only |

---

## Network Assumptions

| Item | Value |
|----|----|
| File Server IP | 192.168.10.30 |
| Hostname | fileserver.houndlab.local |
| ZFS Pool | media-pool |

---

## Step 1 — Create File Server LXC

### Download Debian Template (if not present)
```bash
pveam update
pveam available | grep debian
pveam download local debian-12-standard_*.tar.zst
```

### Create Container
```bash
pct create 130 local:vztmpl/debian-12-standard_*.tar.zst \
  --hostname fileserver \
  --cores 4 \
  --memory 4096 \
  --swap 1024 \
  --net0 name=eth0,bridge=vmbr0,ip=192.168.10.30/24,gw=192.168.10.1 \
  --rootfs local-zfs:8 \
  --unprivileged 1 \
  --features nesting=1,keyctl=1
```

Start container:
```bash
pct start 130
pct enter 130
```

---

## Step 2 — Install File Server Packages

```bash
apt update && apt full-upgrade -y
apt install -y samba nfs-kernel-server acl sudo
```

---

## Step 3 — Mount ZFS Datasets into Container

### On Proxmox Host
Bind-mount datasets:
```bash
pct set 130 -mp0 /media-pool/media,mp=/srv/media
pct set 130 -mp1 /media-pool/backups,mp=/srv/backups
pct set 130 -mp2 /media-pool/archives,mp=/srv/archives
```

Restart container:
```bash
pct restart 130
pct enter 130
```

---

## Step 4 — Create Shared Directories & Permissions

```bash
mkdir -p /srv/{media,backups,archives}
groupadd fileshare
```

Create users:
```bash
useradd -m dennis
passwd dennis
usermod -aG fileshare dennis
```

Set permissions:
```bash
chown -R root:fileshare /srv
chmod -R 2775 /srv
setfacl -R -m g:fileshare:rwx /srv
```

---

## Step 5 — Configure SMB (Samba)

### Create Samba Password
```bash
smbpasswd -a dennis
```

### Edit Samba Config
```bash
nano /etc/samba/smb.conf
```

Append:
```ini
[media]
  path = /srv/media
  browseable = yes
  read only = no
  valid users = @fileshare

[backups]
  path = /srv/backups
  browseable = no
  read only = no
  valid users = @fileshare

[archives]
  path = /srv/archives
  browseable = yes
  read only = yes
  valid users = @fileshare
```

Restart Samba:
```bash
systemctl restart smbd nmbd
```

---

## Step 6 — Configure NFS

Edit exports:
```bash
nano /etc/exports
```

Add:
```text
/srv/media    192.168.10.0/24(rw,sync,no_subtree_check)
/srv/backups  192.168.10.0/24(rw,sync,no_subtree_check)
/srv/archives 192.168.10.0/24(ro,sync,no_subtree_check)
```

Apply:
```bash
exportfs -ra
systemctl restart nfs-server
```

---

## Step 7 — Firewall Rules (Container)

Allow internal traffic:
```bash
ufw allow from 192.168.10.0/24
ufw enable
```

---

## Step 8 — Test Access

### SMB (from client)
```
\\fileserver.houndlab.local\media
```

### NFS (Linux client)
```bash
mount -t nfs fileserver.houndlab.local:/srv/media /mnt/test
```

---

## Step 9 — Backup & Snapshot Strategy

On Proxmox:
- Snapshot container before major changes
- ZFS snapshots handled at pool level

---

## Validation Checklist

- [ ] Container running
- [ ] ZFS datasets mounted
- [ ] SMB access works
- [ ] NFS mounts succeed
- [ ] Permissions enforced
- [ ] No WAN exposure

---

## Completion Criteria

Milestone 04 is complete when:
- Central file server is operational
- SMB + NFS both functional
- Storage backed by ZFS
- Ready for media, web, and game services

---

## Next Milestone

➡ **Milestone 05 — Web Server**
