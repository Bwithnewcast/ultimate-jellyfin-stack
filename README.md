# 🎬 Personal Media Server Stack

Welcome to your ultimate personal media server stack!  
This setup uses Docker Compose to orchestrate a suite of media management, VPN, and proxy services — all containerized and customizable.

---

## 🚀 Overview

This stack includes:

- **Media servers & managers**: Jellyfin, Radarr, Sonarr, Readarr, Lidarr, Bazarr, Tdarr, etc.
- **Torrent & downloader tools**: qBittorrent, Kapowarr, Cross-seed, Autobrr, Unpackerr
- **VPN routing**: Gluetun (Wireguard ProtonVPN)
- **Proxy & reverse proxy**: Currently Nginx Proxy Manager (planned migration to Nginx + Authelia for SSO)
- **Notifications & monitoring**: Notifiarr, Uptime Kuma, Whisparr (planned)
- **Other utilities**: Dozzle logs, Jellystat, Wizarr, Cabernet
- **Blog server**: Planned Ghost blog on your domain

---

## 📝 TODO List

| Task                                             | Status |
|--------------------------------------------------|--------|
| Split Docker network initialization to a separate file | ⬜      |
| Replace Nginx Proxy Manager with Nginx + Authelia for SSO | ⬜      |
| Add Cloudflare configuration guide                | ⬜      |
| Integrate Notifiarr, Whisparr, and Uptime Kuma    | ⬜      |
| Add blog server (Ghost) at domain.com              | ⬜      |

---

## 🖼️ Architecture Diagram

```mermaid
graph LR
  style proxy fill:#f9f,stroke:#333,stroke-width:2px
  style starr fill:#bbf,stroke:#333,stroke-width:2px
  style gluetun_network fill:#bfb,stroke:#333,stroke-width:2px

  subgraph Proxy Network
    proxy[Nginx Proxy Manager]
    notifiarr[Notifiarr]
    jellyfin[Jellyfin]
    tdarr[Tdarr]
    wizarr[Wizarr]
    cabernet[Cabernet]
    bazarr[Bazarr]
    jellyseerr[Jellyseerr]
    unpackerr[Unpackerr]
  end

  subgraph Starr Network
    radarr[Radarr]
    sonarr[Sonarr]
    readarr[Readarr]
    lidarr[Lidarr]
    bazarr
    wizarr
  end

  subgraph Gluetun VPN Network
    gluetun[Gluetun VPN]
    qbittorrent[qBittorrent]
    prowlarr[Prowlarr]
    kapowarr[Kapowarr]
    autobrr[Autobrr]
    crossseed[Cross-seed]
    flaresolverr[Flaresolverr]
  end

  gluetun -->|VPN Tunnel| Internet[Internet]

  qbittorrent --> gluetun
  prowlarr --> gluetun
  kapowarr --> gluetun
  autobrr --> gluetun
  crossseed --> gluetun
  flaresolverr --> gluetun

  jellyfin --> proxy
  radarr --> starr & proxy & gluetun_network
  sonarr --> starr & proxy & gluetun_network
  readarr --> starr & proxy
  lidarr --> starr & proxy
  bazarr --> starr & proxy

  tdarr --> proxy
  wizarr --> starr & proxy
  cabernet --> proxy
  unpackerr --> proxy

🔧 Setup Instructions
1️⃣ Initialize Docker Networks

Create and run the network initialization script:

#!/bin/bash
set -e

echo "Creating Docker external networks..."

docker network create proxy || echo "Network 'proxy' already exists"
docker network create starr || echo "Network 'starr' already exists"
docker network create jellystat || echo "Network 'jellystat' already exists"
docker network create gluetun_network || echo "Network 'gluetun_network' already exists"

echo "Docker networks initialized!"

Save this as docker-networks.sh, make executable with chmod +x docker-networks.sh, then run ./docker-networks.sh once.
2️⃣ Environment Variables

Create a .env file with variables like:

PUID=1000
PGID=1000
TZ=Europe/London

BASE_PATH=/path/to/appdata
MEDIA_SHARE=/path/to/media
WIREGUARD_KEY=your_wireguard_private_key
WIREGUARD_ADD=10.0.0.2/24
VPN_SERVER_COUNTRIES=US,CA
RADARR_IPV4=172.18.0.10
RADARR_KEY=your_radarr_api_key
SONARR_KEY=your_sonarr_api_key
NOTIFIARR_API_KEY=your_notifiarr_api_key
JELLYFIN_URL=http://yourdomain.com/jellyfin
WIZARR_URL=http://yourdomain.com/wizarr
PUBLICIP_API=https://api.ipify.org
PUBLICIP_TOKEN=your_api_token
FREE_ONLY=false

3️⃣ Replace Nginx Proxy Manager with Nginx + Authelia (SSO)

Plan:

    Deploy Nginx as your reverse proxy (replace current Nginx Proxy Manager).

    Use Authelia for secure Single Sign-On with MFA.

    Configure Nginx to authenticate via Authelia for all services.

    Integrate with Cloudflare for DNS and enhanced security.

Example Nginx + Authelia flow:

graph TD
  Client --> Nginx
  Nginx --> Authelia
  Authelia --> UserDB[User Database]
  Authelia --> Nginx
  Nginx --> BackendServices[Your Media Services]

4️⃣ Configure Cloudflare

    Point your domain/subdomains to your server IP.

    Enable Cloudflare proxy (orange cloud) for subdomains.

    Use Full (strict) SSL/TLS mode.

    Set Firewall rules to protect services.

    Optionally configure Cloudflare Access for extra security.

5️⃣ Additional Services

    Notifiarr: Cross-service notifications.

    Whisparr: Media requests and approvals.

    Uptime Kuma: Service uptime monitoring.

    Ghost: Blogging platform at blog.yourdomain.com.

📚 Resources & References

    Gluetun Docker

    LinuxServer.io Docker Images

    Authelia SSO

    Cloudflare Docs

    Ghost Blog Platform

🎨 Contribution & Ideas

Open issues or PRs to:

    Help with migration to Nginx + Authelia.

    Add Cloudflare or Ghost configuration guides.

    Improve Docker Compose setups.

docker-networks.sh

#!/bin/bash
set -e

echo "Creating Docker external networks..."

docker network create proxy || echo "Network 'proxy' already exists"
docker network create starr || echo "Network 'starr' already exists"
docker network create jellystat || echo "Network 'jellystat' already exists"
docker network create gluetun_network || echo "Network 'gluetun_network' already exists"

echo "Docker networks initialized!"

