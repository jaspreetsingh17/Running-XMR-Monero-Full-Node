# Requirements for Monero Full Node Deployment

This document lists the requirements for deploying a Monero (XMR) full node.  
Each section can be expanded for details.

---

## 1. Operating System

- Ubuntu 20.04 LTS or Ubuntu 22.04 LTS

---

## 2. Hardware

- **CPU**: 2+ cores  
- **RAM**: 4 GB minimum (8 GB recommended)  
- **Disk**:  
  - Minimum: 160 GB free space (pruned node)  
  - Recommended: 400+ GB free space (full node)  
  - Mounted at `/data`  
- **Network**: Reliable broadband connection


---

## 3. User & Permissions


- Default user: `root`  
- Recommended: dedicated user `monero` (for better security)



---

## 4. Software Dependencies


Install the following packages before deployment:

```bash
apt update && apt install -y   curl   wget   gnupg   bzip2   tar   ufw
```


---

## 5. Files Included for Manual Deployment (or Automation)


This repository contains both the **manual deployment documentation** and supporting files.  

- `Deploy_Manually.md` → Step-by-step manual deployment guide  
- `deploy.sh` → Automated deployment script  
- `config/monerod.conf` → Example Monero config file  
- `config/systemd/monerod.service` → Example systemd service file  



---

## 6. Network & Ports


- **18080/tcp** → P2P (sync with Monero network)  
- **18089/tcp** → Restricted RPC, bound on `0.0.0.0` (external access)  
- **18081/tcp** → Unrestricted RPC, bound on `127.0.0.1` (local only)  


---

## 7. Monitoring (Optional)

- **Prometheus + Grafana** for dashboards  
- **Node Exporter** → system metrics  
- **Custom Monero exporter** → blockchain height, peer count, etc.  
