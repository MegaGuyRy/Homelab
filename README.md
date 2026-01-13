# 🖥️ Homelab Infrastructure

This repository documents my personal **homelab environment**, used to self-host services, experiment with DevOps concepts, and build reliable, secure infrastructure on commodity hardware.

The lab prioritizes:
- Reproducibility (Docker-based deployments)
- Security (VPN-first access, minimal public exposure)
- Observability and maintainability
- Real-world troubleshooting and documentation

---

## 📌 High-Level Overview

**Core stack:**
- Host OS: Ubuntu Server
- Container runtime: Docker + Docker Compose
- Networking: Local LAN + VPN (WireGuard)
- DNS: Pi-hole
- Media services: Jellyfin
- Configuration management: Git + versioned configs

All services are containerized and run on a single server, with persistent volumes stored on local and external storage.

---

## 🧱 Hardware & Host Environment

- **Host OS:** Ubuntu Server (LTS)
- **CPU / RAM:** x86_64 system (details intentionally generalized)
- **Storage:**
  - OS disk (SSD)
  - External SSD for persistent service data and media
- **Networking:**
  - Static LAN IP
  - Port forwarding only for VPN access

---

## 🐳 Containerization Strategy

All services are deployed using **Docker Compose** with the following principles:

- One service = one compose directory
- Persistent data stored in named or bind-mounted volumes
- Secrets stored in `.env` files (never committed)
- Containers restart automatically on failure
- No services exposed publicly unless explicitly intended

---

## 🔐 Services

### 1️⃣ WireGuard — Secure Remote Access VPN

**Purpose:**  
Provides encrypted remote access into the homelab from external networks.

**Why it exists:**  
- Eliminates public exposure of admin interfaces
- Enables secure access to internal services (DNS, media, dashboards)
- Allows split-tunnel or full-tunnel access when off LAN

**How it’s run:**
- Docker container
- UDP port exposed on the host
- Clients authenticate using public/private key pairs
- Internal DNS routed through Pi-hole

**Access model:**
- VPN required for all admin dashboards
- VPN clients receive internal IPs
- Fine-grained routing via `AllowedIPs`

---

### 2️⃣ Pi-hole — Network-wide DNS Filtering

**Purpose:**  
Acts as the primary DNS server for both LAN devices and VPN clients.

**Why it exists:**
- Network-wide ad and tracker blocking
- Visibility into DNS queries
- Centralized DNS control for the homelab

**How it’s run:**
- Docker container
- Persistent volumes for configuration and query logs
- Bound to the host network or specific ports
- Used as:
  - Primary DNS for LAN
  - DNS endpoint pushed to WireGuard clients

**Security notes:**
- Admin dashboard accessible only from LAN or VPN
- No public DNS exposure

---

### 3️⃣ Jellyfin — Self-Hosted Media Server

**Purpose:**  
Provides a private media streaming platform without third-party services.

**Why it exists:**
- Full ownership of media and metadata
- No external accounts or subscriptions
- Hardware-agnostic streaming to multiple devices

**How it’s run:**
- Docker container
- Media library mounted from external storage
- Persistent config and metadata volumes
- Accessible via LAN and VPN

**Features enabled:**
- User profiles
- Library scanning
- Transcoding (when supported by host hardware)

---

## 📁 Data & Storage Layout

