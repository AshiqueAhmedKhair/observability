@@ -1,15 +1,181 @@
## Essential config files for Obsevability
### Download the prometheus config file  

	wget https://raw.githubusercontent.com/prometheus/prometheus/main/documentation/examples/prometheus.yml**
# Observability Stack on AWS EC2 — Complete Step-by-Step Guide

A complete, production-style observability stack running on a single EC2 host. Perfect for learning SRE / DevOps observability fundamentals on a fresh EC2 instance.

### Download Loki Config 
	wget https://raw.githubusercontent.com/grafana/loki/v2.8.0/cmd/loki/loki-local-config.yaml -O loki-config.yaml
**Flask + MySQL app with Prometheus, Grafana, cAdvisor & Node Exporter (Docker Compose)**

## What You Will Build

- 🧩 **Flask Students App** (API)
- 🗄 **MySQL** (application database)
- ⚡ **Redis** (app usage / caching)
- 📈 **Prometheus** (metrics collection)
- 📊 **Grafana** (dashboards & visualization)
- 🐳 **cAdvisor** (container-level metrics)
- 🖥 **Node Exporter** (EC2 host metrics)

All services run via Docker Compose.

## Prerequisites

- AWS account
- SSH key pair (.pem)
- Local machine tools:
  - `ssh`
  - `scp` (optional)
  - `git` (recommended)

## 1) Launch EC2 Instance

### Instance Setup

| Setting | Value |
|---------|-------|
| OS | Ubuntu 22.04 or Amazon Linux 2 |
| Instance Type | t3.small (recommended) or t3.micro (light load) |

### Security Group – Inbound Rules

| Port | Service | Access |
|------|---------|--------|
| 22 | SSH | Your IP only |
| 3000 | Grafana | Your IP / VPN |
| 5000 | Flask App | Your IP |
| 8080 | cAdvisor | Your IP |
| 9090 | Prometheus | Your IP |
| 9100 | Node Exporter | Your IP |
| 3306 | MySQL | ❌ Keep closed |
| 6379 | Redis | ❌ Keep closed |

**Best practice:** Never expose MySQL or Redis publicly.

## 2) SSH into the EC2 Instance

### Ubuntu
`ssh -i /path/to/key.pem ubuntu@ip`

### Amazon Linux
`ssh -i /path/to/key.pem ec2-user@ip`

## 3) Install Docker & Docker Compose

### Ubuntu 22.04
`sudo apt-get update -y`  
`sudo apt-get install -y docker.io docker-compose-plugin`  
`sudo usermod -aG docker $USER`  
`newgrp docker`  

### Amazon Linux 2
`sudo yum update -y`  
`sudo amazon-linux-extras install docker -y`  
`sudo systemctl enable --now docker`  
`sudo usermod -aG docker $USER`  
`newgrp docker`  

### Verify Installation
`docker compose version`

## 4) Get the Project
`git clone`

`cd observability`

If copied manually, just navigate into the folder.

## 5) Configuration Files

Ensure these files exist:
- `docker-compose.yaml`
- `prometheus.yml`
- `loki-config.yaml`
- `promtail-config.yaml`

If needed, download them:
1. `wget https://raw.githubusercontent.com/prometheus/prometheus/main/documentation/examples/prometheus.yml -O prometheus.yml`
2. `wget https://raw.githubusercontent.com/grafana/loki/v2.8.0/cmd/loki/loki-local-config.yaml -O loki-config.yaml`
3. `wget https://raw.githubusercontent.com/grafana/loki/v2.8.0/clients/cmd/promtail/promtail-docker-config.yaml -O promtail-config.yaml`

## 6) Start the Stack
`docker compose up -d`
`docker ps`

### Running Services

| Service | Port |
|---------|------|
| MySQL (db) | 3306 |
| Flask app | 5000 |
| Redis | 6379 |
| cAdvisor | 8080 |
| Node Exporter | 9100 |
| Prometheus | 9090 |
| Grafana | 3000 |

## 7) Access the Services

Replace `<PUBLIC-IP>` with your EC2 public IP.

- **Students App:** http://`<PUBLIC-IP>`:5000
- **Grafana:** http://`<PUBLIC-IP>`:3000 (default: admin / admin)
- **Prometheus:** http://`<PUBLIC-IP>`:9090
- **cAdvisor:** http://`<PUBLIC-IP>`:8080
- **Node Exporter:** http://`<PUBLIC-IP>`:9100/metrics

## 8) Configure Grafana Data Sources

1. Go to **Grafana** → **Connections** → **Data sources**
2. Add **Prometheus**
3. Set URL: `http://prometheus:9090` (Docker internal network)
4. Save & test

Optional: If Loki is added → `http://loki:3100`

## 9) Import Ready-Made Dashboards

In **Grafana** → **Import Dashboard**, use these IDs:

| Dashboard | ID |
|-----------|-----|
| Node Exporter Full | 1860 |
| cAdvisor | 14282 |
| Prometheus 2 Stats | 3662 |

## 10) Operate the Stack

# Stop all services
`docker compose down`

# View logs
`docker compose logs -f <service-name>`

# Update images
`docker compose pull`
`docker compose up -d`

## 11) Production Hardening

- ✅ Restrict Security Groups (IP / VPN only)
- ✅ Do NOT expose MySQL & Redis
- ✅ Use ALB + ACM (TLS) for Grafana/App
- ✅ Enable MySQL backups (EBS snapshots)
- ✅ Prefer private subnet + Bastion or AWS SSM

## 12) Troubleshooting

| Issue | Fix |
|-------|-----|
| Container down | `docker compose ps` |
| Service errors | `docker compose logs -f <service>` |
| Port not accessible | Check SG + EC2 firewall |
| Docker permission denied | Re-login or `newgrp docker` |

## Next Steps

The repo already includes **Loki & Promtail** configs for centralized logging. Add them to `docker-compose.yaml` to enable log aggregation in Grafana.

## License

**Bongodev** || **Happy observing!** 🚀


### Download Promtail Config


	wget https://raw.githubusercontent.com/grafana/loki/v2.8.0/clients/cmd/promtail/promtail-docker-config.yaml -O promtail-config.yaml
