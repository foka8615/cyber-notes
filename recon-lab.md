# Lab Report #001 — Home Network 
# Reconnaissance
**Date:** 2026-06-07 **Analyst:** 
foka8615 **Target:** Home Network 
(192.168.1.0/24) **Device:** Sharp 
S7-SH | Android 11 | Termux (no-root) 
**Status:** Completed ---
## 1. Objectives
- Perform network reconnaissance on 
home network - Identify live hosts and 
open services - Assess router security 
posture - Document findings 
professionally ---
## 2. Environment Setup
### Device
| Property | Value | 
|----------|-------|
| Phone | Sharp S7-SH | OS | Android 
| 11 | Root | No | Tool Platform | 
| Termux |
### Tools Installed
| Tool | Version | Purpose | 
|------|---------|---------|
| nmap | 7.99 | Network scanner | 
| dnsutils | 9.20.23 | DNS queries | 
| net-tools | 2.10.0 | ifconfig, 
| netstat | tracepath | 20250605 | 
| Route tracing | git | 2.54.0 | 
| Version control |
### Issues Resolved During Setup
| Issue | Cause | Fix | 
|-------|-------|-----|
| `pkg update &` backgrounded | 
| Accidental `&` operator | Removed 
| `&`, used `&&` | dpkg interrupted | 
| Previous session crash | `dpkg 
| --configure -a` | openssl.cnf 
| conflict | Package update conflict | 
| Kept current version (N) | Broken 
| mirror | textcord.xyz mirror down | 
| `termux-change-repo` | nano backup 
| error | Wrong nanorc option | `rm 
| ~/.nanorc` | Git push SSH blocked | 
| Carrier blocks port 22 | Switched to 
| HTTPS | GitHub token 403 | Missing 
| repo scope | Regenerated with repo 
| scope |
---
## 3. Reconnaissance Phase
### Phase 1 — Host Discovery
**Command:** ```bash
nmap -sn 192.168.1.0/24

## Rédaction du Rapport Complet: 
# Reconnaissance
**Date:** 2026-06-07 **Analyst:** 
foka8615 **Target:** Home Network 
(192.168.1.0/24) **Device:** Sharp 
S7-SH | Android 11 | Termux (no-root) 
**Status:** Completed ---
## 1. Objectives
- Perform network reconnaissance on 
home network - Identify live hosts and 
open services - Assess router security 
posture - Document findings 
professionally ---
## 2. Environment Setup
### Device
| Property | Value | 
|----------|-------|
| Phone | Sharp S7-SH | OS | Android 
| 11 | Root | No | Tool Platform | 
| Termux |
### Tools Installed
| Tool | Version | Purpose | 
|------|---------|---------|
| nmap | 7.99 | Network scanner | 
| dnsutils | 9.20.23 | DNS queries | 
| net-tools | 2.10.0 | ifconfig, 
| netstat | tracepath | 20250605 | 
| Route tracing | git | 2.54.0 | 
| Version control |
### Issues Resolved During Setup
| Issue | Cause | Fix | 
|-------|-------|-----|
| `pkg update &` backgrounded | 
| Accidental `&` operator | Removed 
| `&`, used `&&` | dpkg interrupted | 
| Previous session crash | `dpkg 
| --configure -a` | openssl.cnf 
| conflict | Package update conflict | 
| Kept current version (N) | Broken 
| mirror | textcord.xyz mirror down | 
| `termux-change-repo` | nano backup 
| error | Wrong nanorc option | `rm 
| ~/.nanorc` | Git push SSH blocked | 
| Carrier blocks port 22 | Switched to 
| HTTPS | GitHub token 403 | Missing 
| repo scope | Regenerated with repo 
| scope |
---
## 3. Reconnaissance Phase
### Phase 1 — Host Discovery
**Command:** ```bash nmap -sn 
192.168.1.0/24 ``` **Results:**
| Host | Latency | Identity | 
|------|---------|----------|
| 192.168.1.1 | 72ms | Router 
| (gateway) | 192.168.1.130 | 20ms | 
| Phone (analyst device) |
**Observation:** Only 2 hosts active. 
Clean network. ---
### Phase 2 — Service Version Scan
**Command:** ```bash nmap -sV 
192.168.1.1 ``` **Results:**
| Port | State | Service | Version | 
|------|-------|---------|---------|
| 53/tcp | Open | DNS | dnsmasq 2.86 | 
| 80/tcp | Open | HTTP | Demo-Webs | 
| 443/tcp | Open | HTTPS | Unknown | 
| 997 ports | Filtered | — | No 
| response |
---
### Phase 3 — Aggressive Scan
**Command:** ```bash nmap -A 
192.168.1.1 ``` **Additional 
Findings:**
| Finding | Detail | 
|---------|--------|
| SSL Certificate CN | TZKWYF | 
| Organization | TZKW | Country | CN 
| (China) | Valid From | 2024-05-15 | 
| Valid Until | 2034-05-13 | Cert Type 
| | Self-signed |
---
### Phase 4 — Admin Panel Access
**Method:** Browser → 
http://192.168.1.1 **Finding:** Router 
identified as **ZLT T30 PLUS** - Login 
panel accessible over unencrypted HTTP 
- Login attempt counter visible (4 
remaining on discovery) - Interface 
language: French ---
## 4. Vulnerability Assessment
| Finding | Severity | Description | 
|---------|----------|-------------|
| HTTP admin panel open | 🔴 High | 
| Credentials transmitted unencrypted 
| |
| Port 80 exposed | 🔴 High | MITM 
| attack possible on local network | 
| Chinese OEM firmware | 🟡 Medium | 
| Unknown supply chain security | 
| Self-signed certificate | 🟡 Medium 
| | No trusted CA verification |
| dnsmasq exposed | 🟡 Medium | DNS 
| hijacking possible if compromised | 
| WPS likely enabled | 🔴 High | Brute 
| force attack vector | Default 
| credentials risk | 🔴 High | OEM 
| defaults often unchanged |
---
## 5. OSI Model Mapping of Findings
| Layer | Finding | Attack Vector | 
|-------|---------|--------------|
| L7 Application | HTTP admin panel | 
| Credential interception | L6 
| Presentation | Self-signed TLS cert 
| | SSL stripping possible |
| L5 Session | Login attempt counter | 
| Session brute force | L4 Transport | 
| Ports 53, 80, 443 open | Port-based 
| attacks | L3 Network | Router at 
| 192.168.1.1 | Routing manipulation | 
| L2 Data Link | ARP visible on LAN | 
| ARP poisoning possible | L1 Physical 
| | ZLT T30 PLUS hardware | Physical 
| access risk |
---
## 6. Remediation Actions Taken
| Action | Status | --------|--------| 
| Changed admin password | ✅ 
| Recommended | Disable HTTP (port 80) 
| | ✅ Recommended |
| Change default SSID | ✅ Recommended 
| |
| Disable WPS | ✅ Recommended | Check 
| connected devices | ✅ Recommended | 
| Update firmware | ✅ Recommended | 
| Change admin username | ✅ 
| Recommended |
---
## 7. Tools Reference
### Commands Used
```bash
# Network interface info
ifconfig
# Find gateway/router IP
ip route | grep default
# Ping sweep — discover live hosts
nmap -sn 192.168.1.0/24
# Service version scan
nmap -sV 192.168.1.1
# Aggressive scan (OS, versions, 
# scripts)
nmap -A 192.168.1.1
# DNS query test
dig google.com @192.168.1.1
# Connectivity test
ping -c 3 github.com ``` ---
## 8. Conclusions
This reconnaissance exercise 
demonstrated that a no-root Android 
device running Termux is a viable 
platform for network security 
assessment. **Key Takeaways:** - Home 
routers frequently expose admin panels 
over unencrypted HTTP - OEM firmware 
(especially Chinese manufacturers) 
carries supply chain risk - Full 
network recon is achievable with nmap 
alone - Every OSI layer had at least 
one observable attack surface **Next 
Steps:** - [ ] Complete router 
hardening - [ ] Install termux-api for 
Wi-Fi scanning - [ ] Start TryHackMe 
Pre-Security path - [ ] Perform DNS 
recon with dig - [ ] Document next lab 
in Lab Report #002 ---
## 9. Methodology Reference
**Framework used:** OODA Loop Observe 
→ Orient → Decide → Act Applied at 
each step: screenshot evidence 
collected, diagnosis performed before 
action, sequential fixes applied, 
results verified before proceeding. 
--- *Report generated from Termux on 
Android.* *All testing performed on 
analyst-owned equipment.* *No 
unauthorized systems were accessed.* 
``` ---
