### Node1A — Primary Compute (Proxmox VE)

Node1A is the my primary Proxmox virtualization host. It runs the widest set of VMs, LXCs in the homelab, covering everything from video-audio workloads to lab services, media automation, monitoring, and management.

Configuration:
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

| VM ID | Role | vCPU | RAM (GB) | Disk (GB) | Secondary disk (GB) | GPU |
|-------|------|------|----------|-----------|---------------------|-----|
| ws-linux-edit     | Linux workstation (video/photo editing) | 16 | 64 | 512 | 1024 | ✅ |
| win-lab-dc        | Windows Server domain controller lab | 8 | 32 | 512 | 2x 512 | ❌ |
| win-lab-client1   | Windows test client 1 | 4 | 16 | 128 | - | ❌ |
| win-lab-client2   | Windows test client 2 | 4 | 16 | 128 | - | ❌ |
| know-kiwix        | Kiwix offline content server | 4 | 16 | 256 | - | ❌ |
| media-jellyfin    | Jellyfin media server | 4 | 16 | 128 | 256 | ✅ |
| dl-qbittorrent    | qBittorrent download node | 2 | 8 | 64 | - | ❌ |
| arr-tdarr         | Tdarr transcoding node | 4 | 16 | 128 | - | ✅ |
| arr-bazarr        | Bazarr subtitles manager | 2 | 4 | 32 | - | ❌ |
| arr-lidarr        | Lidarr music automation | 2 | 4 | 32 | - | ❌ |
| arr-radarr        | Radarr movie automation | 2 | 4 | 32 | - | ❌ |
| arr-tunarr        | Tunarr pseudo-live TV | 4 | 16 | 128 | - | optional |
| photos-immich     | Immich photo/video server | 4 | 16 | 128 | - | optional |
| game-minecraft    | Minecraft server | 4 | 16 | 128 | 512 | ❌ |
| web-apache-blog   | Apache2 blog server | 2 | 4 | 32 | - | ❌ |
| web-apache-blog-2 | Apache2 blog server 2 | 2 | 4 | 32 | - | ❌ |
| dash-monitor      | Services dashboard | 2 | 4 | 32 | - | ❌ |
| net-nginx         | NGINX reverse proxy | 2 | 4 | 32 | - | ❌ |
| net-pihole        | Pi-hole DNS | 2 | 4 | 32 | - | ❌ |
| net-nextcloud     | Nextcloud | 4 | 16 | 256 | 1024 | ❌ |
| net-sftp-eve      | SFTP for Eve-NG | 2 | 4 | 32 | 2x 1024 | ❌ |
| lab-eve-client    | Eve-NG client tools | 4 | 16 | 256 | - | ❌ |
| backup-pbs        | Proxmox Backup Server | 2 | 8 | 32 | - | ❌ |
| mgmt-pdm          | Proxmox Datacenter Manager | 2 | 4 | 32 | - | ❌ |
| mon-zabbix        | Zabbix monitoring | 4 | 16 | 256 | - | ❌ |

## Workstation & Lab

### ws-linux-edit
A high-resource Linux workstation VM designed for video and photo editing and rendering. It receives a GPU passthrough share of the RTX A4500 and a large vCPU and RAM allocation to handle demanding creative workloads. Project files live on a secondary NVMe volume to keep system and data separate.

### win-lab-dc
A Windows Server 2019/2022 virtual machine acting as a domain controller for the lab environment. It provides Active Directory, DNS, and DHCP for the Windows lab network. Lab file shares are hosted on HDD/ZFS storage, and a secondary pair of NVMe disks handles additional storage for the domain environment.

### win-lab-client1 / win-lab-client2
Two lightweight Windows test client VMs used for testing domain-joined behaviour, Group Policy, application compatibility, and Windows networking within the lab. They are kept deliberately small so they can be spun up and down without impacting the host significantly.

---

## Knowledge & Media

### know-kiwix
A Debian-based VM running Kiwix, which serves ZIM-format compressed snapshots of websites and reference content entirely without internet access. Useful for air-gapped research, offline Wikipedia access. (ZIM files live on main system SSD)

### media-jellyfin
The Jellyfin media server VM responsible for serving the homelab media library. It has GPU access for hardware-accelerated transcoding and uses NVMe for the transcode cache while media files themselves are streamed from the HDD/ZFS pool on Node3.

### dl-qbittorrent
A dedicated download VM running qBittorrent, isolated from the rest of the media stack for network policy and resource reasons. Downloads land on HDD/ZFS storage and an optional SSD temp area is available for faster staging.

### arr-tdarr
The Tdarr transcoding node, responsible for automated media format conversion and health scanning. It has GPU access for hardware encoding and mounts media read-write to modify files in place. The Tdarr processing cache runs on NVMe for performance.

### arr-bazarr
A subtitle management VM running Bazarr, which automatically fetches and synchronises subtitles for media in the library. It mounts the media library read-only to search and attach subtitle files without writing to the media itself.

### arr-lidarr
Lidarr music library automation VM. It monitors and manages the music collection, automating downloads and keeping the library organised. Music paths are shared with Jellyfin so new content is immediately visible to the media server.

### arr-radarr
Radarr movie library automation VM. It manages the movie collection, handles quality upgrades, and coordinates with the download client. Movie paths are shared with Jellyfin for seamless library updates.

### arr-tunarr
A Tunarr VM that creates pseudo-live TV channels from the media library, simulating a broadcast schedule. It can optionally use FFmpeg with GPU acceleration if many simultaneous channels are active.

### photos-immich
The Immich photo and video server VM, providing a self-hosted photo management platform. Original files live on HDD/ZFS while the database and cache run on SSD or NVMe for performance. Optionally GPU-accelerated for ML-based photo features.

---

## Gaming

### game-minecraft
A Minecraft server VM running under Discopanel for management. World data is kept on SSD for low-latency disk access. This VM is sized for a small multiplayer environment.

---

## Web & Networking

### web-apache-blog / web-apache-blog-2
Two separate Apache2 web server VMs hosting blog content. They run independently so one can be updated or tested without affecting the other. Blog data lives alongside the system disk with snapshot-based backups.

### net-nginx
The NGINX reverse proxy VM that fronts all internal web UIs and services. It handles TLS termination, routing, and load distribution across the homelab's web-facing services.

### net-pihole
A Pi-hole DNS VM providing network-wide ad blocking and DNS filtering. It has a deliberately small footprint since it runs continuously and must stay highly available for the whole network.

### net-nextcloud
The Nextcloud file sync and collaboration server. User files are stored on HDD/ZFS for capacity, while the database and cache live on SSD or NVMe for responsive performance.

### net-sftp-eve
An SFTP server dedicated to serving files to the Eve-NG network emulation environment. SFTP data lives on a dedicated 2× 1TB NVMe mirror for fast and reliable access.

### lab-eve-client
A VM with Eve-NG client helper tooling for interacting with the network emulation lab. It provides supporting utilities and connectivity to the Eve-NG environment without being the Eve-NG server itself.

---

## Monitoring & Management

### backup-pbs
The Proxmox Backup Server VM, responsible for receiving and deduplicating VM backups from the Proxmox cluster. It uses a separate ZFS dataset on dedicated disk as its backup datastore so backup data stays isolated from VM storage.

### mgmt-pdm
The Proxmox Datacenter Manager VM, providing a central management UI for the entire Proxmox cluster. It aggregates cluster state, alerts, and configuration across all nodes in one interface.

### mon-zabbix
The Zabbix monitoring server VM. It collects metrics, triggers alerts, and maintains dashboards for hardware and service health across the homelab. It stores monitoring data on its own 256GB disk for historical retention.
