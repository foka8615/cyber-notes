
# OSI Model — Cybersecurity Notes
## Rule
Every layer = attack surface + defense 
point.
## Layer 7 — Application
- Protocols: HTTP, HTTPS, DNS, FTP, 
SMTP, SSH - Does: Formats data for 
user-facing apps - Attack: SQLi, XSS, 
phishing, API abuse - Defense: WAF, 
input validation
## Layer 6 — Presentation
- Protocols: TLS/SSL, JPEG, ASCII - 
Does: Translates, encrypts, compresses - 
Attack: SSL stripping - Defense: Enforce 
TLS 1.3, certificate pinning
## Layer 5 — Session
- Protocols: SMB, RPC, NetBIOS - Does: 
Opens/manages/closes sessions - Attack: 
Session hijacking, cookie theft - 
Defense: HttpOnly flags, short timeouts
## Layer 4 — Transport
- Protocols: TCP, UDP - Does: Segments 
data, ensures delivery - Attack: SYN 
flood, port scanning - Defense: IDS/IPS, 
rate limiting
## Layer 3 — Network
- Protocols: IP, ICMP, IPSec, BGP - 
Does: Logical addressing + routing - 
Attack: IP spoofing, BGP hijacking - 
Defense: Firewall ACLs, IPSec
## Layer 2 — Data Link
- Protocols: ARP, Ethernet, 802.11 - 
Does: MAC addressing, local framing - 
Attack: ARP poisoning, VLAN hopping - 
Defense: DAI, 802.1X, port security
## Layer 1 — Physical
- Standards: RJ45, fiber, Bluetooth - 
Does: Raw bit transmission - Attack: 
Wiretapping, hardware implants - 
Defense: Physical locks, disable unused 
ports
## Quick Reference
| Layer | Name | Protocols | Attack | 
| Defense |
|-------|------|-----------|--------|---------| 
| 7 | Application | HTTP, DNS, SSH | 
| SQLi, XSS | WAF | 6 | Presentation | 
| TLS, SSL | SSL stripping | TLS 1.3 | 5 
| | Session | SMB, RPC | Session hijack 
| | Secure cookies |
| 4 | Transport | TCP, UDP | SYN flood | 
| IPS | 3 | Network | IP, ICMP | IP 
| spoofing | ACLs | 2 | Data Link | ARP, 
| Ethernet | ARP poison | DAI | 1 | 
| Physical | — | Hardware tap | Physical 
| locks |
## Memory Aid
All People Seem To Need Data Processing (7→1)

# The OSI Model — Cybersecurity Edition

**One rule: every layer = a place attackers can strike and defenders can monitor.**

---

## Layer 7 — Application
**Protocols:** HTTP, HTTPS, DNS, FTP, SMTP, SSH, IMAP
**Does:** Formats data for user-facing apps.
**Example:** Banking app sends HTTPS request.
**Attack:** Phishing, SQLi, XSS, API abuse | **Defense:** WAF, input validation

## Layer 6 — Presentation
**Protocols:** TLS/SSL, JPEG, ASCII, UTF-8
**Does:** Translates, encrypts, compresses data.
**Example:** Browser negotiates TLS before loading your bank's site.
**Attack:** SSL stripping | **Defense:** Enforce TLS 1.3+, certificate pinning

## Layer 5 — Session
**Protocols:** SMB, RPC, NetBIOS, SOCKS
**Does:** Opens, manages, closes sessions between devices.
**Example:** Gmail session token keeps you logged in across clicks.
**Attack:** Session hijacking, cookie theft | **Defense:** Short timeouts, HttpOnly cookie flags

## Layer 4 — Transport
**Protocols:** TCP (reliable), UDP (fast)
**Does:** Segments data, ensures delivery, error checking.
**Example:** File download = TCP. Video stream = UDP.
**Attack:** SYN flood, port scanning | **Defense:** Rate limiting, IDS/IPS, firewall rules

## Layer 3 — Network
**Protocols:** IP, ICMP, IPSec, BGP, OSPF
**Does:** Logical addressing + routing between networks.
**Example:** Packet hops through 12 routers to reach Singapore.
**Attack:** IP spoofing, BGP hijacking, ping flood | **Defense:** Firewall ACLs, IPSec

## Layer 2 — Data Link
**Protocols:** Ethernet, Wi-Fi (802.11), ARP, MAC, VLAN
**Does:** Physical addressing (MAC), frames data for local network.
**Example:** Laptop uses MAC address to reach home router.
**Attack:** ARP poisoning, MAC flooding, VLAN hopping | **Defense:** DAI, port security, 802.1X

## Layer 1 — Physical
**Standards:** RJ45, USB, Bluetooth, fiber
**Does:** Raw bit transmission — cables, signals, radio waves.
**Example:** Rogue USB plugged into a data center server.
**Attack:** Wiretapping, hardware implants, RF jamming | **Defense:** Physical locks, disable unused ports

---

## Quick Reference Table

| Layer | Name | Key Protocol(s) | Primary Attack | Primary Defense |
|-------|------|----------------|---------------|-----------------|
| 7 | Application | HTTP, DNS, SSH | SQLi, XSS, phishing | WAF, input validation |
| 6 | Presentation | TLS, SSL | SSL stripping | Enforce TLS 1.3 |
| 5 | Session | SMB, RPC | Session hijacking | Secure cookies, timeouts |
| 4 | Transport | TCP, UDP | SYN flood, port scan | IPS, rate limiting |
| 3 | Network | IP, ICMP | IP spoofing, DDoS | Firewall ACLs, IPSec |
| 2 | Data Link | ARP, Ethernet | ARP poisoning | DAI, 802.1X |
| 1 | Physical | — | Hardware tap, jamming | Physical locks, disable ports |

---

**Memory:** *"All People Seem To Need Data Processing"* (7→1)

**Phone Resources:** TryHackMe "Pre-Security" path · Professor Messer OSI on YouTube · Cisco OSI diagrams (Google Images)

