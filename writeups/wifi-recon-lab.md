# Lab Report #002 — Wi-Fi Reconnaissance & DNS Analysis
**Date:** 2026-06-08
**Analyst:** foka8615
**Platform:** Sharp S7-SH | Android 11 | Termux (no-root)
**Target:** Home Wi-Fi Environment + Router DNS Services
**Status:** Completed
**Methodology:** OODA Loop (Observe → Orient → Decide → Act)

---

## 1. Objectives

- Enumerate all visible Wi-Fi networks in the environment
- Analyze router-level DNS behavior and security posture
- Test for DNS hijacking, manipulation, and information leakage
- Document all findings in professional pentest report format
- Identify attack surface across OSI Layers 2, 3, and 7

### Why This Matters
Wi-Fi reconnaissance is Phase 1 of any wireless security assessment.
Understanding what networks exist, their security configurations,
and how DNS behaves reveals exploitable weaknesses before any
active attack is attempted. Every finding here maps directly to
real-world attack vectors used by threat actors.

---

## 2. Environment

### Analyst Device
| Property | Value |
|----------|-------|
| Device | Sharp S7-SH |
| OS | Android 11 |
| Root | No (locked bootloader) |
| Tool Platform | Termux + Termux:API |
| Location | WAT timezone (UTC+1) |

### Tools Used
| Tool | Version | Purpose | Layer |
|------|---------|---------|-------|
| nmap | 7.99 | Port/service scanning | L3/L4 |
| dig (dnsutils) | 9.20.23 | DNS queries + analysis | L7 |
| termux-wifi-connectioninfo | — | Current network info | L2 |
| termux-wifi-scaninfo | — | Wi-Fi scan (blocked by Android 11) | L2 |
| Wi-Fi Analyzer | Latest | Nearby network enumeration | L2 |

---

## 3. Methodology — OODA Loop Applied

### Observe
Collect raw data: run scans, capture output, take screenshots.
No interpretation yet — just evidence gathering.

### Orient
Analyze what the data means in a security context.
Map findings to attack vectors, CVEs, OSI layers.

### Decide
Determine what follow-up actions or tests are needed.
Prioritize by severity and exploitability.

### Act
Execute the next test, apply fixes, or document findings.
Every action produces new observations — the loop repeats.

---

## 4. Phase 1 — Wi-Fi Environment Enumeration

### Why
Identifying all nearby networks reveals:
- Potential rogue access points
- Channel interference and overlap
- Security protocol weaknesses (WEP/WPA/WPA2/WPA3)
- WPS vulnerabilities

### How
Two methods attempted due to Android 11 restrictions:

#### Method A — termux-wifi-scaninfo (Failed)
```bash
termux-wifi-scaninfo | grep -E "ssid|bssid|level|frequency"

# Result: 
[] — empty array
Why it failed: Android 11 throttles third-party Wi-Fi scanning
APIs for privacy reasons. Unfixable without root access.
Lesson: OS-level restrictions are a real constraint in
mobile-based security assessments.

#### Method B — termux-wifi-connectioninfo (Success)
termux-wifi-connectioninfo
Result:
{
  "bssid": "fc:3f:fc:9f:d0:12",
  "frequency_mhz": 2457,
  "ip": "192.168.1.130",
  "link_speed_mbps": 57,
  "mac_address": "02:00:00:00:00:00",
  "network_id": 18,
  "rssi": -67,
  "ssid": "MTN HomeBox_9FD012",
  "ssid_hidden": false,
  "supplicant_state": "COMPLETED"
}

#### Method C — Wi-Fi Analyzer App (Success)
 Used as fallback when Termux API was blocked by Android 11.
Networks Discovered
SSID
BSSID
Signal
Channel
Frequency
Security
Distance
MTN HomeBox_9FD012
fc:3f:fc:9f:d0:12
-64 dBm
10
2457 MHz
WPA2+WPS
~15m
InfinityBox_A0E2
42:36:18:e2:a0:af
-90 dBm
6
2437 MHz
WPA2+WPS
~309m
Current Connection Details
Property
Value
Analysis
SSID
MTN HomeBox_9FD012
Reveals ISP (MTN) + device model
BSSID
fc:3f:fc:9f:d0:12
Router MAC — OUI lookup: TOZED KANGWEI
Frequency
2457 MHz
Channel 10, 2.4GHz band
RSSI
-67 dBm
Moderate signal strength
Link Speed
57 Mbps
Adequate for home use
MAC Address
02:00:00:00:00:00
Android randomized MAC ✅ privacy
IP
192.168.1.130
DHCP assigned
# 5. Phase 2 — Router Service Scanning
Why
Open ports = attack surface. Every open service is a potential
entry point. Identifying services and versions allows:
CVE lookup for known vulnerabilities
Understanding what the router exposes to local network
Tracking changes between scans (hardening verification)
#### Scan 1 — Service Version Detection
nmap -sV --script=http-title 192.168.1.1

# Result: 
Port 80 no longer present — HTTP title script
found nothing. Confirms port 80 closure.
6. Phase 3 — DNS Security Analysis
# Why
DNS is the internet's phonebook. A compromised or
misconfigured DNS server can:
Redirect users to malicious sites (DNS hijacking)
Expose software versions (information disclosure)
Leak internal network information
Facilitate man-in-the-middle attacks at Layer 7
# Test 1 — DNS Version Disclosure
dig @192.168.1.1 version.bind chaos txt

# Result:
ANSWER SECTION:
version.bind. → "dnsmasq-2.86"
Query time: 20ms

# Finding:
 Router responds to CHAOS class queries,
revealing exact DNS software version. Any attacker on
the local network can identify the DNS software and
look up CVEs for that specific version.

# Risk: Medium — version disclosure is a misconfiguration.
Hardened routers block CHAOS queries.

# Test 2 — DNS Resolution Verification
dig google.com @192.168.1.1
dig facebook.com @192.168.1.1

# Results:
| Domain | IP Returned | Query Time | Status |
|--------|-------------|------------|--------|
| google.com | 216.58.223.206 | 10ms | ✅ Normal |
| facebook.com | 57.144.38.1 | 100ms | ✅ Normal |

# Observation:
 facebook.com took 10x longer than google.com.
Router forwarded to upstream MTN DNS servers. Google.com
was cached (10ms), facebook.com was not (100ms = upstream lookup).

# Test 3 — DNS Hijacking Check
dig google.com @192.168.1.1   # Router DNS
dig google.com @8.8.8.8       # Google DNS (ground truth)

# Comparison:
| DNS Server | google.com Result | Match |
|------------|------------------|-------|
| 192.168.1.1 (MTN/Router) | 216.58.223.206 | ✅ |
| 8.8.8.8 (Google) | 216.58.223.206 | ✅ |

# Finding:
 Both return identical IP.
MTN is NOT hijacking or manipulating DNS responses. ✅

## Test 4 — Reverse DNS Lookup
dig -x 192.168.1.1 @192.168.1.1

# Result:
 NXDOMAIN — no PTR record exists.

# Finding:
 Router does not advertise its own identity
via reverse DNS. No information leakage. ✅

## Test 5 — DNS Trace (Iterative Resolution)
dig google.com +trace @192.168.1.1

# Result:
communications error to 192.168.1.1#53: timed out (x3)
no servers could be reached

# Finding:
 Router blocks iterative/trace DNS queries.
This is a security feature — it prevents DNS
reconnaissance techniques that map the full resolution
chain. Well-hardened routers block this intentionally.

## 7. Complete Findings Summary
Vulnerabilities
ID
Finding
Severity
Location
Recommendation
F-01
WPS enabled
🔴 High
Both APs
Disable WPS immediately
F-02
DNS version disclosure
🟡 Medium
Port 53
Block CHAOS queries if possible
F-03
Self-signed CN certificate
🟡 Medium
Port 443
Expected on OEM — note only
F-04
SSID reveals ISP+model
🟡 Medium
Wi-Fi
Rename SSID to neutral name
F-05
Android 11 scan throttling
🔵 Info
Analyst device
Root device for full assessment
Positives (Security Controls Working)
Control
Evidence
Port 80 HTTP closed
Not present in Lab 002 scan
DNS not hijacked
Router and Google return same IPs
No reverse DNS leak
NXDOMAIN on PTR query
DNS trace blocked
Router rejects iterative queries
Android MAC randomized
02:00:00:00:00:00 shown
998 ports filtered
Router blocking most traffic

## 8. OSI Layer Mapping
Layer
Finding
Attack Vector
Status
L7 Application
dnsmasq version exposed
Targeted CVE exploitation
⚠️
L7 Application
DNS not hijacked
DNS spoofing
✅ Clean
L6 Presentation
Self-signed CN TLS cert
SSL stripping possible
⚠️
L5 Session
DNS trace blocked
Session-level recon blocked
✅
L4 Transport
Ports 53+443 only open
Reduced attack surface
✅
L3 Network
Router at 192.168.1.1
Gateway identified
ℹ️
L2 Data Link
WPS enabled on both APs
Reaver brute force
🔴
L1 Physical
ZLT T30 PLUS hardware
Physical access risk
ℹ️

## 9. Limitations of This Assessment
Limitation
Cause
Impact
No packet capture
No root access
Cannot see traffic content
Wi-Fi scan throttled
Android 11 restriction
Only 2 networks visible
No monitor mode
No root + locked bootloader
Cannot capture WPA handshakes
No WPS testing
No root + no aircrack-ng
WPS vulnerability unconfirmed
Passive DNS only
No DNS spoofing tools
Cannot test active attacks

# Recommended upgrade:
 Dedicated rooted Android device
(e.g. Pixel 6 with GrapheneOS or unlocked bootloader)
would remove all limitations above.

## 10. Remediation Actions
Action
Priority
Status
Disable WPS on MTN HomeBox_9FD012
🔴 Immediate
Pending
Rename SSID to neutral name
🟡 Medium
Pending
Disable WPS on InfinityBox_A0E2
🔴 Immediate
Not analyst's network
Block CHAOS DNS queries
🟡 Medium
Router may not support
Upgrade to WPA3 if supported
🟡 Medium
Check router firmware
Update router firmware
🟡 Medium
Pending

## 11. Commands Reference

# Current Wi-Fi connection details
termux-wifi-connectioninfo

# Scan all nearby Wi-Fi networks (blocked on Android 11 no-root)
termux-wifi-scaninfo

# Ping sweep - find all hosts on network
nmap -sn 192.168.1.0/24

# Service version scan
nmap -sV 192.168.1.1

# Aggressive scan (OS, scripts, versions)
nmap -A 192.168.1.1

# HTTP title grab
nmap -sV --script=http-title 192.168.1.1

# Query DNS version
dig @192.168.1.1 version.bind chaos txt

# Resolve domain via router DNS
dig google.com @192.168.1.1

# Resolve domain via Google DNS (comparison)
dig google.com @8.8.8.8

# Reverse DNS lookup
dig -x 192.168.1.1 @192.168.1.1

# DNS trace (blocked by router - expected)
dig google.com +trace @192.168.1.1

## 12. Conclusions
 This assessment demonstrates that meaningful network security
reconnaissance is achievable from a no-root Android device.

# Primary Risk:
 WPS enabled on home router — highest priority fix.

# Key Insight:
 The router showed evidence of partial hardening
between Lab 001 and Lab 002 (port 80 closed). This confirms
that remediation actions from Lab 001 were effective and
verifiable through repeated scanning — a core principle of
security engineering.

# DNS Posture: Clean. No hijacking, no manipulation, trace
queries blocked. MTN upstream DNS is functioning correctly.

# Methodology Validation: OODA loop applied consistently —
each scan result oriented the next decision. Sequential
diagnosis before action prevented wasted effort and
produced clean, reproducible results.

All testing performed on analyst-owned equipment.
No unauthorized systems were accessed.

