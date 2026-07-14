# Security Labs

A collection of hands-on cybersecurity labs documenting practical exercises in network scanning, packet analysis, web application security testing, and log analysis.

## Structure

- **`nmap/`** — TCP port scanning and service enumeration using Nmap, including full-port scans, top-port scans, and NSE script output (service/version detection, vulnerability scripts).
- **`wireshark/`** — Packet capture and traffic analysis (TCP, DNS, HTTP) in simulated environments.
- **`burpsuite/`** — Web application security testing, including SQL Injection and XSS testing in safe lab environments, aligned with OWASP Top 10 guidance.
- **`splunk/`** — SIEM log analysis and SPL search queries for investigating suspicious activity in a lab SOC environment.

## Nmap Lab — scanme.nmap.org

Ran a top-100-port service enumeration scan against Nmap's official public test target.

**Command:**
sudo nmap -sV -sC --top-ports 100 scanme.nmap.org -oA scanme_top100
**Key findings:**
- Port 22 — SSH, OpenSSH 6.6.1p1 (Ubuntu)
- Port 80 — HTTP, Apache httpd 2.4.7 (Ubuntu)
- Port 21 — filtered/tcpwrapped

Full output saved in [`nmap/scanme_top100.nmap`](./nmap/scanme_top100.nmap).

## About

This repo is a working log of my hands-on learning in cybersecurity — each folder will be updated as I complete more labs.
