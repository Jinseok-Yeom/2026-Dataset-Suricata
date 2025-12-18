# ELK Stack 7.17.x Installation (Docker Compose)

This document describes how to install and run the ELK Stack
(Elasticsearch, Logstash, Kibana, Filebeat) for Suricata eve.json ingestion.

---

## Environment
- OS: Ubuntu 22.04
- Suricata: 8.x (Port Mirroring based IDS)
- Docker Engine: Docker CE (APT official)
- Docker Compose: v2 plugin
- ELK Version: 7.17.x (LTS, stable)

---

## 1. Prerequisites

### 1.1 Kernel parameter for Elasticsearch
Elasticsearch requires vm.max_map_count >= 262144.

Apply the setting temporarily:
sudo sysctl -w vm.max_map_count=262144

Apply the setting persistently:
echo "vm.max_map_count=262144" | sudo tee /etc/sysctl.d/99-elasticsearch.conf
sudo sysctl --system

---

## 2. Start ELK Stack

Move to the ELK stack directory:
cd 04-elk-stack

Start containers using Docker Compose:
docker compose -p suricata-elk up -d

---

## 3. Verification

### 3.1 Elasticsearch
Verify Elasticsearch is running:
curl http://localhost:9200

Expected result: JSON response with cluster_name and version 7.17.x.

### 3.2 Kibana
Access Kibana via web browser:
http://<SURICATA_SERVER_IP>:5601

Kibana may take 1–2 minutes to become available after first startup.

---

## 4. Notes
- This ELK stack is dedicated to Suricata eve.json ingestion.
- Packet capture and Suricata execution are handled outside Docker.
- No conflict occurs between system-installed Suricata and Docker-based ELK.
- This setup is intended for dataset collection and machine learning research.

---

## 5. Directory Structure (Example)

04-elk-stack/
├── docker-compose.yml
├── logstash/
│   └── pipeline/
│       └── suricata.conf
└── README.md

