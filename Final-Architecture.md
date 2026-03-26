# 🧠 Final Architecture (clean + realistic)
```
                                                     Internet
                                                        |
                                                     [Router]
                                                        |
                                                 [TP-Link Switch]
                                                        |
     ┌──────────────────────────────────────────────────┴────────────────────────────────────────────────────┐
     |                                                                                                       |
[Dell Precision]                                                                                     [4-node cluster]
(Proxmox host)                                                                                            (k3s nodes)
     |
 Core VMs + Services
     |
[vm1- Network Core] - pihole, NGINX/Traefik, Cloudflare Tunnel
[vm2] - VPN - WireGuard
[vm3] - Monitoring - Prometheus, Grafana
[vm4] - Utility / Jumpbox - SSH tools, kubectl, ansible(optional)
```
# 🖥️ 1. Dell = Proxmox “Core”

Install Proxmox VE on the Dell.

## 🔧 VM layout (copy this exactly)
### VM 1 — Network Core
```
Name: infra-core
CPU: 2-4
RAM: 2-4GB
```

Runs:

- Pi-hole (DNS)
- NGINX / Traefik
- Cloudflare Tunnel

### VM 2 — VPN
```
Name: vpn
CPU: 1-2
RAM: 1GB
```
Runs:

WireGuard

### VM 3 — Monitoring
```
Name: monitoring
CPU: 2
RAM: 2-4GB
```
Runs:

Prometheus
Grafana
VM 4 — Utility / Jump Box
Name: admin
CPU: 1-2
RAM: 1-2GB

Runs:

SSH tools
kubectl
ansible (optional)
☸️ 2. Kubernetes on your 4 nodes (k3s)

Use lightweight Kubernetes: k3s

🧱 Cluster layout
nugget1 → control plane
nugget2 → worker
nugget3 → worker
nugget4 → worker
⚡ Install (simple + fast)
On nugget1:
curl -sfL https://get.k3s.io | sh -

Get token:

sudo cat /var/lib/rancher/k3s/server/node-token
On other nodes:
curl -sfL https://get.k3s.io | K3S_URL=https://192.168.1.10:6443 K3S_TOKEN=YOUR_TOKEN sh -
🌐 3. Networking (important but simple)

Since your switch is unmanaged:

Static IPs
192.168.1.2   dell-proxmox
192.168.1.3   infra-core VM
192.168.1.4   vpn VM
192.168.1.5   monitoring VM

192.168.1.10  nugget1 (k3s master)
192.168.1.11  nugget2
192.168.1.12  nugget3
192.168.1.13  nugget4
🔐 4. Remote Access (best combo)
WireGuard (full access)

Runs on:

VPN VM

Use for:

SSH
Proxmox UI
Kubernetes admin
☁️ Cloudflare Tunnel (external access)

Use Cloudflare Zero Trust

Expose:

Grafana
Internal dashboards
Apps from Kubernetes

👉 Never expose SSH or Proxmox here

🔁 5. How traffic flows (this is key)
Internal:
Devices → TP-Link switch → nodes / Dell
Remote:
Admin:
Laptop → WireGuard → full lab access
Public apps:
User → Cloudflare → Tunnel → your services
⚙️ 6. Connect Kubernetes to your core services
DNS

Point all nodes to:

192.168.1.3 (Pi-hole VM)
Ingress (for apps)

Install Traefik or NGINX inside k3s

Example flow:

Cloudflare → Tunnel → Traefik → Kubernetes service
🧪 7. What runs where (clear separation)
Dell (stable layer)
DNS
VPN
Monitoring
Reverse proxy
Kubernetes (dynamic layer)
Apps
Containers
Experiments
Dev environments
🧹 8. Physical improvements (quick wins)
Dell
Lift off carpet (seriously do this)
Give rear airflow space
Cluster cables
Shorten Ethernet cables
Route straight down → bundle → back
🚀 9. What you can do with this setup

You now have a real platform, not just a lab:

Run microservices
Simulate production deployments
Learn DevOps pipelines
Practice:
scaling
failover
observability
🏁 Final mental model
Dell = infrastructure (reliable, always-on)
Cluster = compute (flexible, scalable)
WireGuard = private access
Cloudflare = public entry point
