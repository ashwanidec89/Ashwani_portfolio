# Network Reconnaissance and Service Enumeration Report

## 1. Objective
Discover responsive hosts, identify TCP services, and inspect HTTP behavior in an authorized virtual lab.

## 2. Scope
- Lab subnet: `172.16.19.0/24`
- Test host: `172.16.19.128` (Kali Linux VM)
- HTTP test port: `8000/tcp`
- TCP port-range scan: `1–1000/tcp`
- Tools: Nmap 7.99 and curl

## 3. Methodology
1. Discover responsive hosts on the lab subnet.
2. Identify the HTTP service on the test host.
3. Enumerate HTTP methods.
4. Inspect HTTP response headers.
5. Scan TCP ports 1–1000 and record the observed result.
6. Query DNS A and MX records and document the responses.
7. Capture DNS request and response traffic for inspection.
8. Document the active IPv4 network and calculate /26 subnets.
9. Perform service/version detection, OS fingerprinting, default NSE script checks, and a targeted UDP DNS-port scan.

## 4. Results

### 4.1 Host Discovery
Four hosts responded during discovery of `172.16.19.0/24`. Their ownership and purpose were not independently verified.

### 4.2 HTTP Service Enumeration
- Target: `172.16.19.128`
- Port: `8000/tcp`
- Observed state: Open
- Service identification: Python SimpleHTTPServer
- Reported Python version: `3.13.12`
- HTTP title: `Security Research Lab`

### 4.3 HTTP Methods
Nmap's HTTP-methods script reported:
- `GET`
- `HEAD`

### 4.4 HTTP Response
The server returned `HTTP/1.0 200 OK` and disclosed its server software through the `Server` header. The inspected response did not include `Content-Security-Policy` or `X-Content-Type-Options`.

### 4.5 TCP Port Scan
- Target: `172.16.19.128`
- Ports scanned: `1–1000/tcp`
- Observed result: Nmap reported all 1,000 ports as `filtered (no-response)`.
- Scan duration: approximately 202 seconds.

This result is recorded as observed. The reason for the filtered responses has not yet been established.

### 4.6 Targeted TCP Connect Scan
A TCP connect scan (`-sT`) checked ports 22, 80, 443, and 8000 on `172.16.19.128`.

- `22/tcp`: Closed
- `80/tcp`: Closed
- `443/tcp`: Closed
- `8000/tcp`: Open

The scan completed in approximately 0.53 seconds. This confirms that the test host's HTTP service was reachable on TCP port 8000 during this check.

The earlier SYN scan reported ports 1–1000 as filtered. The difference has been documented; its cause has not been established.


## 5. Security Observations

### Server Version Disclosure
The HTTP `Server` header reveals server software and version details.

**Recommendation:** For production deployments, review whether server-version details need to be exposed.

### Security Header Review
The inspected response did not include `Content-Security-Policy` or `X-Content-Type-Options`.

**Recommendation:** Assess suitable security headers according to the application, its functionality, and its threat model. Their absence alone does not prove an exploitable vulnerability.

### Filtered TCP Ports
Nmap received no response for TCP ports 1–1000 during the recorded scan.

**Recommendation:** Verify the scan behavior and network configuration before drawing conclusions about port exposure.

## 6. Limitations
- Testing was limited to the specified virtual lab and selected checks.
- The purpose of other discovered hosts was not verified.
- The cause of the filtered TCP-port results remains undetermined.
- The Python HTTP server was used for lab testing, not as a production deployment recommendation.

## 7. Conclusion
The exercise covered host discovery, TCP port scanning, HTTP service identification and method enumeration, response-header inspection, DNS A and MX lookups, DNS packet capture, and IPv4 subnetting. Findings are documented as observations, with unresolved results identified for follow-up.

## 8. Evidence Files
- `scans/host-discovery.txt`
- `scans/http-enumeration.txt`
- `scans/http-methods.txt`
- `scans/tcp-port-scan.txt`
- `scans/tcp-targeted-check.txt`
- `scans/dns-enumeration.txt`
- `scans/dns-traffic.pcap`
- `scans/subnetting-basics.txt`


## 9. DNS Enumeration

- Domain tested: `example.com`
- Configured resolver: `172.16.19.2`
- A-record query with EDNS disabled: `104.20.23.154`
- MX-record response: `0 .`

The initial A-record query returned a malformed-message warning. Repeating the query with EDNS disabled returned an A record successfully. The exact cause of the initial warning was not established.

The MX response `0 .` indicates that the domain publishes a Null MX record, meaning it does not accept email.

Evidence: `scans/dns-enumeration.txt`

## 10. DNS Packet Capture

A packet capture was performed on the Kali VM's `eth0` interface while querying `example.com`.

Observed exchange:
- DNS client: `172.16.19.128`
- DNS resolver: `172.16.19.2`
- Query: `A example.com`
- Response: `104.20.23.154`
- Packets captured: 2
- Packets dropped: 0

The capture confirms that the DNS query and corresponding response were visible in the lab traffic capture.

Evidence:
- `scans/dns-traffic.pcap` — packet capture
- `scans/dns-enumeration.txt` — DNS record and resolver output


## 11. Subnetting Fundamentals

### Objective
Document the current virtual network configuration and demonstrate IPv4 subnetting calculations.

### Observed Network Configuration
- Interface: eth0
- IPv4 address: 172.16.19.128/24
- Network address: 172.16.19.0
- Subnet mask: 255.255.255.0
- Broadcast address: 172.16.19.255
- Default gateway: 172.16.19.2
- Total addresses in /24: 256
- Usable host addresses: 254

### /26 Subnet Calculation
The /24 network can be divided into four /26 subnets. Each /26 subnet contains 64 total addresses and normally 62 usable host addresses.

| Subnet | Usable Host Range | Broadcast |
|---|---|---|
| 172.16.19.0/26 | 172.16.19.1–172.16.19.62 | 172.16.19.63 |
| 172.16.19.64/26 | 172.16.19.65–172.16.19.126 | 172.16.19.127 |
| 172.16.19.128/26 | 172.16.19.129–172.16.19.190 | 172.16.19.191 |
| 172.16.19.192/26 | 172.16.19.193–172.16.19.254 | 172.16.19.255 |

### Evidence
- `scans/subnetting-basics.txt`

### Notes
These are subnetting calculations for learning and documentation. The active VMware network configuration was not changed.


## 12. Advanced Nmap Enumeration

### Service and Version Detection
A targeted scan of `172.16.19.128` on TCP port 8000 identified `SimpleHTTPServer 0.6 (Python 3.13.12)`.

Evidence: `scans/service-version-detection.txt`

### OS Detection
Nmap returned possible Linux OS guesses but warned that fingerprinting may be unreliable because it could not find both an open and a closed TCP port. The operating system was not conclusively identified.

Evidence: `scans/os-detection.txt`

### Default NSE Script Checks
The targeted `-sC -sV -p 8000` scan identified the HTTP title as `Security Research Lab` and the server header as `SimpleHTTP/0.6 Python/3.13.12`. These observations do not, by themselves, establish a vulnerability.

Evidence: `scans/default-scripts.txt`

### UDP DNS Port Scan
A targeted UDP scan of port 53 on the lab resolver `172.16.19.2` reported `53/udp open domain`. This confirms DNS service reachability on that UDP port during the check.

Evidence: `scans/udp-dns-scan.txt`
