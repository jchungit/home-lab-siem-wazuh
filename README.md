# 🛡️ Home Lab — Wazuh SIEM Deployment & Network Monitoring

A hands-on home lab project deploying the **Wazuh SIEM (Security Information and Event Management)** platform to practice real-world log monitoring, threat detection, and incident response — the same skills used by SOC (Security Operations Center) analysts in the field.

---

## 🎯 Goals

- Deploy and configure a production-grade, open-source SIEM from scratch
- Monitor multiple endpoints (Windows and Linux) from a centralized dashboard
- Generate and detect real attack scenarios (brute force, file tampering)
- Build hands-on experience translating directly to **CompTIA Security+** and SOC analyst roles

---

## 🏗️ Lab Architecture

```
[Home Network — AT&T Fiber / Netgear RAXE300 WiFi 6E / WPA3]

  Windows Desktop  ──┐
                     ├── Wazuh Agent ──► Wazuh Server (Ubuntu VM)
  Kali Linux VM    ──┘                        │
                                         Wazuh Dashboard
                                      (Web UI via browser)
```

**Virtualization:** VirtualBox running on Windows host
**Network:** Bridged Adapter — all VMs on the same subnet as the home network
**Wazuh Version:** 4.7 (All-in-One install)

---

## ⚙️ Setup Steps

### 1. Create Ubuntu VM in VirtualBox
- OS: Ubuntu 22.04 LTS
- RAM: 4GB | Storage: 50GB | Network: Bridged Adapter
- Static IP assigned for consistent access

### 2. Install Wazuh (All-in-One)
```bash
curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh
sudo bash wazuh-install.sh -a
```
This installs the **Wazuh Manager**, **Indexer** (OpenSearch), and **Dashboard** all at once.

### 3. Access the Dashboard
```
https://<ubuntu-vm-ip>
# Default credentials printed at end of install output
```

### 4. Deploy Wazuh Agent on Windows
- Dashboard → Agents → Deploy New Agent
- Generated .msi installer registered to server IP
- Run installer on Windows host

### 5. Deploy Wazuh Agent on Kali Linux
```bash
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --dearmor -o /usr/share/keyrings/wazuh.gpg
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | sudo tee /etc/apt/sources.list.d/wazuh.list
sudo apt update && sudo apt install wazuh-agent
sudo nano /var/ossec/etc/ossec.conf  # Set <address> to Wazuh Server IP
sudo systemctl enable --now wazuh-agent
```

---

## 🔍 Attack Scenarios Practiced

### Brute Force Detection
- Triggered multiple failed SSH login attempts
- Wazuh generated alerts for repeated **Event ID 4625** (failed logon)
- Observed real-time alert in the dashboard

### File Integrity Monitoring (FIM)
- Created and modified files in monitored directories
- Wazuh detected changes immediately and logged hash differences

### EICAR Malware Signature Test
- Dropped EICAR test file (safe, industry-standard fake malware)
- Verified Wazuh's malware detection module fired correctly

---

## 🪟 Windows Event IDs Referenced

| Event ID | Description |
|---|---|
| 4624 | Successful logon |
| 4625 | Failed logon |
| 4648 | Logon with explicit credentials |
| 4663 | File/object access attempted |

---

## 📚 What I Learned

- How a SIEM collects, normalizes, and correlates log data from multiple sources
- How to deploy and register agents on both Windows and Linux endpoints
- How attackers' actions (brute force, file tampering) appear as events in a real SIEM
- Alert triage workflow — distinguishing true positives from noise
- The relationship between SIEM data and real-world SOC analyst responsibilities

---

## 🛠️ Tools & Technologies

`Wazuh 4.7` `Ubuntu 22.04` `VirtualBox` `Kali Linux` `Windows 10` `OpenSearch` `WPA3` `WiFi 6E`
