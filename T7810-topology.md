Steve
  - control plane only

nugget1
  - OpenClaw

nugget2
  - LiteLLM
  - Redis

nugget3
  - Qdrant
  - n8n

T7810
  - Ollama
  - Open WebUI
  - future GPU workloads


                                        Internet
                                         │
                                 DNS / Cloudflare
                                         │
                           ┌─────────────────────────┐
                           │ Ingress / Reverse Proxy │
                           │   Traefik / Nginx       │
                           └───────────┬─────────────┘
                                       │
                    ┌──────────────────┴──────────────────┐
                    │                                     │
               Web / API                            Bots / Chat
         Open WebUI / Browser                Telegram / Discord / Slack
                    │                                     │
                    └──────────────────┬──────────────────┘
                                       │
                         ┌─────────────▼─────────────┐
                         │      k3s Control Plane    │
                         │         Steve (Pi)        │
                         │ API / Scheduler / etcd    │
                         └─────────────┬─────────────┘
                                       │
         ┌─────────────────────────────┼─────────────────────────────┐
         │                             │                             │
         │                             │                             │
┌────────▼────────┐         ┌──────────▼─────────┐        ┌──────────▼─────────┐
│ nugget1 (Pi)    │         │ nugget2 (Pi)       │        │ nugget3 (Pi)       │
│ App Worker      │         │ App Worker         │        │ Data / Automation   │
│                 │         │                     │        │                     │
│ - OpenClaw      │         │ - LiteLLM          │        │ - Qdrant            │
│ - tool runners  │         │ - Redis            │        │ - n8n               │
│ - webhooks      │         │ - model routing    │        │ - Flowise           │
└────────┬────────┘         └──────────┬─────────┘        └──────────┬─────────┘
         │                             │                             │
         └─────────────────────────────┼─────────────────────────────┘
                                       │
                           ┌───────────▼───────────┐
                           │   T7810 (x86 worker)  │
                           │   AI / Heavy Compute  │
                           │                       │
                           │ - Ollama / llama.cpp  │
                           │ - embeddings          │
                           │ - Open WebUI (opt.)   │
                           │ - future GPU runtime  │
                           └───────────┬───────────┘
                                       │
                         ┌─────────────▼─────────────┐
                         │   Future GPU Expansion    │
                         │  Tesla P40 / more GPUs    │
                         └─────────────┬─────────────┘
                                       │
                   ┌───────────────────▼───────────────────┐
                   │           Storage / Persistence       │
                   │                                       │
                   │ - local SSD on T7810 (models/cache)  │
                   │ - PVC on k3s                         │
                   │ - optional NAS / NFS / MinIO         │
                   │ - backups / snapshots                │
                   └───────────────────┬───────────────────┘
                                       │
                         ┌─────────────▼─────────────┐
                         │  Monitoring / Operations  │
                         │                           │
                         │ - Prometheus              │
                         │ - Grafana                 │
                         │ - Loki / logs             │
                         │ - uptime / alerts         │
                         └───────────────────────────┘