# Nmap — Host Discovery (The Basics)

**Goal:** find which hosts are _online_ (responding) on a network using Nmap.

---

## Quick summary / cheat‑sheet

- `nmap -sn <target>`  
  Ping‑scan / host discovery only (no port/service scan). Formerly `-sP`.
- `nmap -sL <target>`  
  List targets only (no probes). Good to verify target expansion.
- Run **as root** (or with `sudo`) for best discovery results — Nmap can use ARP, raw packets and privileged probes.
- Common discovery probes:
  - ARP (used automatically on directly connected Ethernet/Wi‑LAN — fastest & most reliable for local subnets)
  - ICMP echo (ping)
  - TCP SYN to common ports (e.g. 80, 443)
  - TCP ACK to common ports
  - UDP to common ports
- Control probe types explicitly:
  - `-PS[ports]` TCP SYN discovery (e.g. `-PS80,443`)
  - `-PA[ports]` TCP ACK discovery
  - `-PU[ports]` UDP discovery
  - `-PP` ICMP timestamp, `-PE` ICMP echo (can mix and match)

---

## Target specification examples

- Single IP: `nmap -sn 10.0.0.5`
- Range: `nmap -sn 192.168.1.1-50`
- CIDR: `nmap -sn 192.168.1.0/24`
- Hostname: `nmap -sn example.local`

---

## Local vs Remote scanning behaviour

### Scanning a **local** directly‑connected subnet
- Nmap will prefer **ARP** probes on Ethernet/Wi‑Fi.  
  ARP requests are answered by devices on the same L2 segment even if IP stack is partially down → very reliable.
- Example:
```bash
sudo nmap -sn 192.168.66.0/24
```
Output includes MAC addresses (because ARP is used).

### Scanning a **remote** subnet (one or more routers away)
- ARP can't be used, so Nmap uses other probes (ICMP, TCP/UDP).
- Some hosts or routers may drop ICMP or certain TCP probes. Nmap will try multiple probe types unless you constrain it.
- Example:
```bash
sudo nmap -sn 192.168.11.0/24
```

---

## Typical Nmap output explained (excerpt)
```
Nmap scan report for MyRouter (192.168.66.1)
Host is up (0.0069s latency).
MAC Address: 44:DF:65:D8:FE:6C (Vendor)
```
- `Host is up` → host responded to one of the discovery probes.
- `MAC Address` present only when scanning L2-local network (ARP responses).

---

## Useful tips & gotchas

- Use `-n` to skip DNS resolution and speed up output when scanning many hosts.
- `-Pn` disables host discovery (assume hosts up) — useful when discovery is blocked but you still want to attempt scans.
- `-v` or `-vv` increases verbosity to help debug what probe types Nmap used.
- Firewalls often block ICMP and some TCP probes → try different probe types (`-PS`, `-PA`, `-PU`) or scan specific ports you expect open.
- Use `--reason` to see why Nmap decided a host is up (which packet/response triggered it).
- Use packet capture (Wireshark/tcpdump) while running a scan to learn which probes and responses are happening:
  - Local subnet: look for ARP requests/replies.
  - Remote subnet: look for ICMP echo/replies, TCP SYN/ACK or RST, ICMP unreachable messages.

---

## Quick examples

Discover live hosts in /24 (local):
```bash
sudo nmap -sn -n 192.168.1.0/24
```

List targets only:
```bash
nmap -sL 10.0.0.0/24
```

SYN probe discovery to ports 80 and 443 only:
```bash
sudo nmap -sn -PS80,443 192.168.11.0/24
```

Show why each host is considered up:
```bash
sudo nmap -sn --reason 192.168.11.0/24
```

Assume hosts are up (skip discovery) and directly scan ports:
```bash
sudo nmap -Pn 192.168.11.5
```

---

## Short walkthrough: interpret a noisy result
If `nmap -sn` reports `Host is up` but your traceroute/tcpdump shows ICMP unreachable from an intermediate router:
- Nmap may have used TCP probes that elicited responses from an intermediate device, or the router generated ICMP responses which Nmap interpreted.
- Use `--reason` and `-vv` + packet capture to confirm true endpoint responses vs intermediate devices.

---

## Further reading
- `man nmap` → full options
- Nmap book / official docs: https://nmap.org/book/  
- pcap analysis with `tcpdump` / `wireshark` to observe actual probe traffic.

---

*Generated: concise Nmap host discovery guide (markdown) — ready for study or inclusion in notes.*
