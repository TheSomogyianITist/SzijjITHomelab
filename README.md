# SzijjITHomelab
# 🏠 Homelab Infrastructure

> Multi-node virtualization · NAS storage · Networking · Backup · Off-site cluster  
> *Last updated: June 2026*

---

## Overview

This homelab spans **two physical locations** connected over ISP uplinks. The primary site hosts the main compute, NAS, lab, and backup nodes interconnected via a 25G SFP28 core fabric, a 10G SFP+ data fabric, and a dedicated 1G IPMI management plane. The off-site location provides a secondary Proxmox host and a remote backup NAS connected through a Telekom fiber uplink.

---

## 🗂️ Table of Contents

- [Network Topology](#network-topology)
- [Nodes](#nodes)
- [Storage](#storage)
- [Networking](#networking)
- [Power Protection](#power-protection)
- [Rack Layout](#rack-layout)

---

## Network Topology

| Fabric | Speed | Purpose |
|--------|-------|---------|
| Core SFP28 | 25G | Inter-node primary fabric |
| Data SFP+ | 10G | Outer/data plane |
| IPMI plane | 1G | Out-of-band management |
| Off-site uplink | Telekom fiber | Site-to-site connectivity |

---

## Nodes

### Node1A — Primary Compute (Proxmox VE)

| Category | Specification |
|----------|--------------|
| Board        | Supermicro X11DPi-NT |
| CPU          | 2× Intel Xeon Platinum 8171M |
| RAM          | 512 GB DDR4 ECC |
| Boot         | 2× 512 GB NVMe mirror |
| VM storage   | 4× 8 TB NVMe RAIDZ1 |
| SFTP storage | 2× 1 TB NVMe mirror |
| M.2 Cards    | 2× ASUS Hyper M.2 x16 Card V2 |
| GPU          | RTX A4500 |
| NIC inner    | Mellanox MCX4121A-ACAT 25G |
| NIC outer    | Supermicro AOC-STGN-I2S 10G |
| PSU          | Seasonic PRIME PX-1600 |
| Case         | Fractal Design Meshify 2 XL |

<details>
<summary>Node1A VM table</summary>

| VM ID | Role | vCPU | RAM (GB) | Disk (GB) | GPU |
|-------|------|------|----------|-----------|-----|
| ws-linux-edit     | Linux workstation (video/photo editing) | 16 | 64 | 256 | ✅ |
| win-lab-dc        | Windows Server domain controller lab | 8 | 32 | 256 | ❌ |
| win-lab-client1   | Windows test client 1 | 4 | 16 | 128 | ❌ |
| win-lab-client2   | Windows test client 2 | 4 | 16 | 128 | ❌ |
| know-kiwix        | Kiwix offline content server | 4 | 16 | 64 | ❌ |
| media-jellyfin    | Jellyfin media server | 4 | 16 | 128 | ✅ |
| dl-qbittorrent    | qBittorrent download node | 2 | 32 | — | ❌ |
| arr-tdarr         | Tdarr transcoding node | 4 | 16 | 128 | ✅ |
| arr-bazarr        | Bazarr subtitles manager | 2 | 32 | — | ❌ |
| arr-lidarr        | Lidarr music automation | 2 | 32 | — | ❌ |
| arr-radarr        | Radarr movie automation | 2 | 32 | — | ❌ |
| arr-tunarr        | Tunarr pseudo-live TV | 4 | 16 | 128 | optional |
| photos-immich     | Immich photo/video server | 4 | 16 | 128 | optional |
| game-minecraft    | Minecraft server | 4 | 16 | 128 | ❌ |
| web-apache-blog   | Apache2 blog server | 2 | 32 | — | ❌ |
| web-apache-blog-2 | Apache2 blog server 2 | 2 | 32 | — | ❌ |
| dash-monitor      | Services dashboard | 2 | 32 | — | ❌ |
| net-nginx         | NGINX reverse proxy | 2 | 32 | — | ❌ |
| net-pihole        | Pi-hole DNS | 2 | 32 | — | ❌ |
| net-nextcloud     | Nextcloud | 4 | 16 | 256 | ❌ |
| net-sftp-eve      | SFTP for Eve-NG | 2 | 32 | — | ❌ |
| lab-eve-client    | Eve-NG client tools | 4 | 16 | 256 | ❌ |
| backup-pbs        | Proxmox Backup Server | 2 | 8 | 32 | ❌ |
| mgmt-pdm          | Proxmox Datacenter Manager | 2 | 32 | — | ❌ |
| pve-ftp           | FTP server | 1 | 16 | — | ❌ |
| mon-zabbix        | Zabbix monitoring | 4 | 16 | 256 | ❌ |

</details>

---

### Node1B — Off-site Compute (Proxmox VE)

| Category | Specification |
|----------|--------------|
| Platform | Minisforum MS-A2 |
| CPU | AMD Ryzen 9 9955HX |
| RAM | 64 GB DDR5 SO-DIMM |
| Boot/VM | 2× 1 TB NVMe mirror |
| NIC | Dual 10G SFP + dual 2.5G LAN |
| OS | Proxmox VE |

---

### Node2 — Compute

| Category | Specification |
|----------|--------------|
| CPU | AMD EPYC 7343 |
| Board | ASRock Rack ROMED8-2T |
| RAM | 256 GB ECC DDR4 |
| Storage | 2× 2 TB NVMe mirror |
| Cooler | Arctic Freezer 4U SP3 |
| Case | Fractal Design Torrent |
| PSU | Zalman Watttera 1200W |
| NIC 10G | AOC-STGN-I2S |
| NIC 25G | MCX4121A-ACAT |

---

### Node3 — Primary NAS (TrueNAS SCALE)

| Category | Specification |
|----------|--------------|
| CPU | AMD EPYC 7302P |
| Board | ASRock Rack ROMED8-2T |
| RAM | 128 GB DDR4 ECC |
| Boot | 2× 512 GB NVMe mirror |
| HBA | Broadcom LSI SAS 9300-16i IT mode |
| HDD | 16× 20 TB SAS 12G |
| NIC 10G | AOC-STGN-I2S |
| NIC 25G | MCX4121A-ACAT |
| PSU | Seasonic PRIME PX-1600 |
| Case | Fractal Design Meshify 2 XL |

| Pool | Topology | Capacity | Purpose |
|------|----------|----------|---------|
| Media vdev 1 | RAIDZ3 (16× 20 TB HDD) | ~260 TB usable | Primary media |

---

### Node4 — Secondary NAS / Backup

| Category | Specification |
|----------|--------------|
| CPU | AMD EPYC 7302P |
| Board | ASRock Rack ROMED8-2T |
| RAM | 128 GB ECC DDR4 |
| Boot | 2× 512 GB NVMe mirror |
| Storage | 4× 8 TB HDD + 4× 16 TB HDD + 3× 4 TB WD Red SA500 |
| HBA | LSI 9300-16i |
| NIC 10G | AOC-STGN-I2S |
| NIC 25G | MCX4121A-ACAT |
| PSU | Zalman Watttera 1200W |
| Case | Fractal Design Torrent |

---

### Node5A & Node5B — Backup NAS Pair

| Node | CPU | Board | RAM | Storage | Pool Topology | Usable | Purpose |
|------|-----|-------|-----|---------|--------------|--------|---------|
| Node5A | EPYC 7302P | ASRock Rack ROMED8-2T | 128 GB DDR4 ECC | 8× 20 TB HDD | RAIDZ2 | ~120 TB | On-site backup NAS |
| Node5B | Ryzen 5 5600 | ASRock Rack X570D4U-2L2T | 128 GB DDR4 ECC | 8× 20 TB HDD | RAIDZ2 | ~120 TB | Off-site backup NAS |

---

### NodeFWA & NodeFWB — Firewalls

| | NodeFWA | NodeFWB |
|--|---------|---------|
| Platform | Minisforum MS-A2 | HP ProDesk 800 G4 |
| CPU | AMD Ryzen 9 9955HX | Intel i5-9500T |
| RAM | 64 GB DDR5-4800 | 16 GB DDR4 |
| Boot | 2× 1 TB NVMe mirror | 256 GB SSD |
| OS | OPNsense / pfSense | OPNsense / pfSense |
| WAN | 2× 2.5G RJ45 onboard | Built-in 1G NIC |
| LAN | 10G SFP onboard | Intel PCIe 1G NIC |
| Mgmt switch | Cisco Catalyst 2960-48 | — |

---

### Node6A–D — Lab Nodes

| Category | Specification |
|----------|--------------|
| Platform | HP ProDesk 800 G4 (×4) |
| RAM | 8 GB DDR4 each |
| Storage | 256 GB SSD each |

---

## Storage

| Node | Pool | Topology | Usable | Purpose |
|------|------|----------|--------|---------|
| Node3 | Media vdev 1 | RAIDZ3 (16× 20 TB HDD) | ~260 TB | Primary media |
| Node5A | Backup pool | RAIDZ2 (8× 20 TB HDD) | ~120 TB | On-site backup |
| Node5B | Backup pool | RAIDZ2 (8× 20 TB HDD) | ~120 TB | Off-site backup |

---

## Networking

| Device | Role |
|--------|------|
| 25G SFP28 core switch | Primary inter-node fabric |
| 10G SFP+ switch | Data/outer fabric |
| Cisco Catalyst 2960-48 | 1G IPMI management plane |
| NodeFWA | Primary firewall (OPNsense/pfSense) |
| NodeFWB | Secondary/off-site firewall |

---

## Power Protection

Both racks are protected by **Eaton 93PS 10 kW** UPS units with true **3-phase input / 3-phase output**, feeding **APC Easy Rack PDU EPDU1232SX3620** switched 3-phase 0U vertical PDUs.

### UPS

| Rack | Model | Specs |
|------|-------|-------|
| Rack A | Eaton 93PS 10 kW | 3-phase in/out, 10 kW, 230/400V configurable, scalable to 20 kW |
| Rack B | Eaton 93PS 10 kW | 3-phase in/out, 10 kW, 230/400V configurable, scalable to 20 kW |

### PDU

| Rack | Model | Specs |
|------|-------|-------|
| Rack A | APC Easy Rack PDU EPDU1232SX3620 | Switched, 0U Vertical, 3-Phase, 22 kW, 230V, 32A, 30× C13 + 6× C19 |
| Rack B | APC Easy Rack PDU EPDU1232SX3620 | Switched, 0U Vertical, 3-Phase, 22 kW, 230V, 32A, 30× C13 + 6× C19 |

> UPS shutdown strategy: graceful shutdown only. Systems shut down normally before battery depletion. Integrated with Proxmox / TrueNAS via Eaton Intelligent Power software.

---

## Rack Layout

| | Rack A (42U — Compute) | Rack B (19U — Networking) |
|--|------------------------|--------------------------|
| UPS | Eaton 93PS 10 kW | Eaton 93PS 10 kW |
| PDU | APC EPDU1232SX3620 (0U vertical) | APC EPDU1232SX3620 (0U vertical) |
| Nodes | Node1A, Node2, Node3, Node4 | NodeFWA, NodeFWB, Node6A–D, switches |

---

## License

Personal homelab documentation — not for commercial redistribution.
