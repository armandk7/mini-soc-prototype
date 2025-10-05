# 🛡️ Mini SOC Prototype

**Mini Security Operations Center (SOC)** — prototype untuk belajar deteksi & respon insiden pada satu laptop menggunakan VMware.

**Tujuan:** simulasi alur end-to-end: *honeypot → SIEM → SOAR → automated response*, supaya bisa dipakai sebagai artefak portfolio.

---

## 🔎 Ringkasan singkat
- SIEM: **Wazuh Manager + Kibana**  
- Honeypot (endpoint): **Cowrie** (merekam serangan SSH)  
- SOAR: **TheHive + Cortex** (otomatisasi pembuatan case & enrichment)  
- Attacker: **Kali Linux** (simulasi brute-force / scanning)

---

## 🧩 Arsitektur & Topologi
`architecture.png` menjelaskan topologi VM (host = laptop), IP contoh, dan port penting:
- Kibana (Wazuh UI): `5601`
- TheHive: `9000`
- SSH Honeypot (Cowrie): `22` (di VM2)
- Wazuh manager <-> Wazuh agents (VM2)

*(Taruhkan diagram `architecture.png` di root repo)*

---

## ⚙️ Spesifikasi Minimum (Host)
- CPU: 4 cores  
- RAM: 16 GB (direkomendasikan) — alokasi VM hemat: 3 + 2 + 3 + 3 GB  
- Storage: 100 GB free  
- VMware Workstation / Player  
- Virtualization (VT-x/AMD-V) aktif

## 🖥️ VM (contoh alokasi)
| VM | Fungsi | OS | vCPU | RAM |
|---|--------|----|------|-----|
| VM1 | SOC-SIEM | Ubuntu Server 22.04 | 2 | 3 GB |
| VM2 | SOC-Client (honeypot) | Ubuntu Desktop 22.04 | 1 | 2 GB |
| VM3 | SOC-SOAR | Ubuntu Server 22.04 | 2 | 3 GB |
| VM4 | SOC-Attacker | Kali Linux | 2 | 3 GB |

Network mode: **Host-Only** atau **NAT** (isolated lab).

---

## 🚀 Quickstart — cara demo (paling ringkas)
1. Import / start semua VM.  
2. Pastikan Wazuh Manager (VM1) berjalan → cek `5601`.  
3. Nyalakan VM2 (Cowrie), pastikan Wazuh Agent terdaftar di Manager.  
4. Aktifkan VM4 (Kali) → lakukan:
   ```bash
   nmap -sS <IP_VM2>
   hydra -l root -P /usr/share/wordlists/rockyou.txt ssh://<IP_VM2>
