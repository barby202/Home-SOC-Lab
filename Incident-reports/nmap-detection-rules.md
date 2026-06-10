# Incident Report 002 — Nmap Detection Rule

**Date:** 2026-06-10  
**Technique:** MITRE ATT&CK T1046 — Network Service Discovery

## What I Did
Built a custom detection rule in Elastic SIEM 8.17 to detect Nmap port scans from the Kali Linux attacker machine targeting the Windows Server victim.

## Attack
Ran an Nmap scan from Kali (192.168.56.20) against Windows Server (192.168.56.10):
```bash
nmap -sS -A 192.168.56.10
```

## Detection
Updated Sysmon config to capture host-only network traffic, then created
a KQL detection rule in Kibana:
event.code: "3" AND winlog.event_data.SourceIp: "192.168.56.20"

**Rule settings:** Medium severity, runs every 5 minutes, MITRE T1046.

## Result
100 medium-severity alerts fired in Kibana Security Alerts.

## Evidence
- `kibana_capturing320-events-kali.png`
- `Real_time_detection.png`
- `Rule_Alert_Detecte.png`
- `Alert-detail-nmap.png`
- `Rule_Created&Enabled`
