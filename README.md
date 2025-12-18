# Suricata Raw Dataset Collection Guide (2026)

This document describes how to collect, verify, and preserve
raw Suricata network event logs for machine learning research.

---

## 1. Environment

- OS: Ubuntu 22.04 LTS
- IDS: Suricata 8.x
- Capture: Port Mirroring (SPAN)
- Output Format: eve.json (JSON lines)

---

## 2. Network Configuration

- NIC 1: Mirror input (no IP assigned, promiscuous mode)
- NIC 2: Management network (192.168.1.x)

Suricata listens only on the mirror interface.

---

## 3. Suricata Log Output

Suricata outputs all events to:

/var/log/suricata/eve.json

Event types include:
- alert
- flow
- dns
- tls
- http

This file is the primary raw dataset source.

---

## 4. Raw Data Preservation (Immutable)

To ensure reproducibility and dataset integrity,
raw logs are copied to a separate directory.

### Directory Structure

/data/suricata/raw
/data/suricata/processed

### Create Directories

sudo mkdir -p /data/suricata/raw
sudo mkdir -p /data/suricata/processed
sudo chown -R $USER:$USER /data/suricata

---

## 5. Initial Raw Log Copy

sudo rsync -av --append-verify \
  /var/log/suricata/eve.json \
  /data/suricata/raw/eve.json

---

## 6. Automated Raw Log Append (cron)

To continuously preserve raw logs:

sudo crontab -e

Add the following line:

*/5 * * * * rsync -a --append-verify /var/log/suricata/eve.json /data/suricata/raw/eve.json

---

## 7. Verification

Check file size growth:

ls -lh /data/suricata/raw/eve.json

Check line count:

wc -l /var/log/suricata/eve.json
wc -l /data/suricata/raw/eve.json

Check recent events:

tail -n 5 /data/suricata/raw/eve.json

---

## 8. Notes

- Raw data is never modified
- All preprocessing and labeling are performed on copies
- ELK Stack is used only for exploration and validation
- Machine learning training uses exported datasets

This design ensures reproducibility and research-grade data quality.

