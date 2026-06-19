# Incident Report 003 — Credential Dumping via Metasploit & Mimikatz

**Date:** 2026-06-19  
**Technique:** MITRE ATT&CK T1003 — OS Credential Dumping

## Summary
Simulated a post-exploitation credential dumping attack using Metasploit's psexec module to gain remote code execution, then deployed Mimikatz to extract NTLM password hashes from the Windows Server's memory (LSASS process). Successfully extracted Administrator credentials and built a Kibana detection rule for future LSASS access attempts.

## Attack Chain

### Stage 1 — Initial Exploitation
Used Metasploit's psexec exploit to authenticate as the 'hacker' local admin account and establish a Meterpreter reverse shell:
- **Target:** Windows Server 2022 (192.168.56.10)
- **Attacker:** Kali Linux (192.168.56.20)
- **Module:** exploit/windows/smb/psexec
- **Result:** Meterpreter session established with SYSTEM privileges

### Stage 2 — Credential Dumping
Executed credential dumping through the Meterpreter shell:

**Command:**
lsa_dump_sam

**Credentials Extracted:**
- Domain: WIN-LRE50V92S7B
- Administrator NTLM Hash: 2383b4398dd9fa046881ddcee5d21416
- Guest account (disabled)
- DefaultAccount (disabled)
- WDAGUtilityAccount (disabled)

## Detection Engineering

### Problem
Standard Sysmon logging wasn't capturing LSASS access events in a way that easily distinguished credential dumping from normal system activity.

### Solution
Modified Sysmon configuration to enable Process Access (Event ID 10) monitoring specifically for LSASS:

```xml
<ProcessAccess onmatch="include">
  <TargetImage condition="is">C:\Windows\system32\lsass.exe</TargetImage>
</ProcessAccess>
```

### Detection Rule Built in Kibana

| Field | Value |
|---|---|
| Rule Name | LSASS Credential Dumping Detected |
| Rule Type | Custom KQL Query |
| Query | `event.code: "10" AND winlog.event_data.TargetImage: "lsass.exe"` |
| Severity | High |
| MITRE Tactic | Credential Access |
| MITRE Technique | T1003 — OS Credential Dumping |
| Schedule | Every 5 minutes |

## Key Findings

1. **Post-exploitation framework access is critical** — once an attacker has a shell, they can dump credentials undetected if proper monitoring isn't in place
2. **Privilege escalation to SYSTEM** is necessary for successful credential dumping
3. **LSASS access monitoring** is essential for detecting tools like Mimikatz, Procdump, and other credential thieves
4. **Defense in depth matters** — Windows Defender caught the initial Metasploit payload and had to be disabled

## Remediation Recommendations

- Enable and enforce Windows Defender real-time protection
- Monitor for LSASS access from unexpected processes
- Implement credential guard to encrypt cached credentials
- Use multi-factor authentication to reduce impact of stolen hashes
- Monitor for SMB lateral movement (port 445)

## Evidence

- Meterpreter session screenshot showing credential dump output
- Kibana detection rule configuration
- Sysmon Event ID 10 logs for LSASS access
- Incident detection rule enabled and monitoring
