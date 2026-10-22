# Network Reconnaissance and Service Enumeration Lab

## Overview
A hands-on networking and reconnaissance lab covering host discovery, TCP port scanning, HTTP service enumeration, DNS queries, packet capture, IPv4 subnetting, and targeted Nmap checks.

## Objectives
- Discover responsive hosts in an authorized virtual lab network.
- Enumerate HTTP services, methods, and response headers.
- Compare TCP scan results and document observed differences.
- Perform service/version detection and default NSE script checks.
- Attempt OS fingerprinting and document its limitations.
- Perform DNS A and MX record lookups and capture DNS traffic.
- Check DNS availability over UDP port 53.
- Practice IPv4 subnetting.

## Environment
- Platform: Kali Linux virtual machine
- Network interface: eth0
- Lab IP at time of testing: 172.16.19.128/24
- Tools: Nmap, curl, dig, tcpdump, Linux networking utilities

## Evidence
See `scans/` for scan outputs, DNS records, packet capture, subnetting calculations, service/version detection, OS fingerprinting, default NSE script checks, and UDP DNS scanning. The `evidence/` directory contains screenshots of host discovery, HTTP enumeration, and DNS packet capture.

## Report
See [Reconnaissance Report](reports/reconnaissance-report.md).

## Scope and Ethics
Educational testing in a virtual lab. Scan only systems for which you have authorization.

## Limitations
Results reflect the lab configuration at the time of testing. OS fingerprinting was inconclusive, and unresolved scan differences are documented without assuming unverified causes.

## Evidence Screenshots

### Host Discovery
![Host Discovery](evidence/host-discovery.png)

### HTTP Service Enumeration
![HTTP Enumeration](evidence/http-enumeration.png)

### DNS Packet Capture
![DNS Traffic Capture](evidence/dns-traffic.png)
