# Home-SOC-Lab
A personal cybersecurity home lab built to simulate an enterprise attack and detection scenarios.

## Environment
- Hypervisor: VirtualBox 7
- VM 1: Windows Server 2022 (victim + Active Directory)
- VM 2: Kali Linux (attacker)
- SIEM: Elastic Stack (installed on Windows Server)

## Tools
Sysmon · Elastic SIEM 8.17 · Nmap · MITRE ATT&CK

## Contents
- `/evidence/screenshots` — Lab setup and attack simulation screenshots
- `/incident-reports` — Documented attack simulations

## Incident Response Documentation

I've documented two major attack simulations with full detection engineering:

### 1. Network Reconnaissance Detection
**File:** `incident-reports/nmap-detection-rule.md`
- Simulated Nmap port scanning from attacker machine
- Built KQL detection rule in Kibana
- Generated 100+ medium-severity alerts
- Mapped to MITRE ATT&CK T1046 (Network Service Discovery)

### 2. Post-Exploitation Credential Dumping
**File:** `incident-reports/credential-dumping-metasploit.md`
- Exploited Windows Server using Metasploit psexec
- Dumped NTLM hashes using Mimikatz
- Extracted Administrator credentials from LSASS memory
- Built high-severity detection rule for LSASS access
- Mapped to MITRE ATT&CK T1003 (OS Credential Dumping)
  
## Evidence  
    Screenshots in /evidence/screenshots/
    Incident report in /incident-reports/

- `/configs` - sysmon and Suricata configuration files

## Attack Simulations
- Nmap reconnaissance scan against Windows Server 2022
- Detected by Sysmon Event ID 3
- MITRE ATT&CK: T1046 - Network Service Discovery

## Certifications
- CompTIA Security+ ---------- Active
- AWS Certified Architect ---- Active 

## Lab Status:
🔨 In progress
