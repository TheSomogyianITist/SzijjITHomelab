## Node1A
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
