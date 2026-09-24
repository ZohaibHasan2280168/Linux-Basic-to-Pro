<div align="center">

# 🌐 Module 03 — Networking & Diagnostics

### Understand, Monitor and Secure Linux Networking

[![Back to Advance](https://img.shields.io/badge/⬅️%20Back-Advance%20Branch-yellow)](../README.md)

</div>

---

## 📌 Table of Contents

- [Connectivity Testing](#connectivity-testing)
- [Network Interfaces](#network-interfaces)
- [Connection Analysis](#connection-analysis)
- [DNS Lookup](#dns-lookup)
- [Downloading & API Requests](#downloading--api-requests)
- [Network Scanning](#network-scanning)
- [Firewall with iptables](#firewall-with-iptables)
- [Quick Reference Cheatsheet](#quick-reference-cheatsheet)
- [Practice Exercises](#practice-exercises)

---

## Connectivity Testing

### `ping` — Test Network Connectivity

```bash
# Basic ping (runs forever, Ctrl+C to stop)
ping google.com
ping 8.8.8.8

# Ping specific number of times
ping -c 4 google.com

# Ping with interval (2 seconds between pings)
ping -c 5 -i 2 google.com

# Test localhost (loopback — your own machine)
ping 127.0.0.1
ping localhost

# Show statistics summary
ping -c 10 google.com
# Output shows: min/avg/max/mdev response times
```

**Understanding ping output:**
```
PING google.com (142.250.80.46) 56 bytes of data.
64 bytes from 142.250.80.46: icmp_seq=1 ttl=117 time=12.3 ms

# time=12.3ms → round trip time (lower is better)
# ttl=117     → Time to Live (hops remaining)
# 0% packet loss → perfect connectivity
```

---

### `traceroute` / `tracepath` — Trace Network Path

```bash
# Trace route to destination (shows all hops)
traceroute google.com
traceroute 8.8.8.8

# Simpler alternative (no root needed)
tracepath google.com

# Trace localhost path
traceroute localhost

# Use ICMP (like ping)
sudo traceroute -I google.com
```

> 💡 **Use traceroute when ping succeeds but a website is slow** — find where the bottleneck is!

---

## Network Interfaces

### `ifconfig` — Network Interface Config (Legacy)

```bash
# Show all network interfaces
ifconfig

# Show specific interface
ifconfig eth0

# Bring interface up/down
sudo ifconfig eth0 up
sudo ifconfig eth0 down

# Set IP address temporarily
sudo ifconfig eth0 192.168.1.100 netmask 255.255.255.0
```

> ⚠️ `ifconfig` is **deprecated** — use `ip` instead!

---

### `ip` — Modern Network Tool

```bash
# Show all interfaces and IP addresses
ip addr show
ip a              # short form

# Show specific interface
ip addr show eth0

# Show routes (routing table)
ip route show
ip r

# Add IP address
sudo ip addr add 192.168.1.100/24 dev eth0

# Remove IP address
sudo ip addr del 192.168.1.100/24 dev eth0

# Bring interface up/down
sudo ip link set eth0 up
sudo ip link set eth0 down

# Show network statistics
ip -s link

# Add default gateway
sudo ip route add default via 192.168.1.1
```

---

## Connection Analysis

### `netstat` — Network Statistics (Legacy)

```bash
# Show all connections
netstat

# Show all listening ports
netstat -l

# Show TCP/UDP connections with port numbers
netstat -tuln

# Show with process names
netstat -tulnp

# Show established connections only
netstat -t

# Show routing table
netstat -r

# Flags:
# -t = TCP connections
# -u = UDP connections
# -l = listening sockets only
# -n = numeric (show IPs, not hostnames)
# -p = show process name and PID
```

---

### `ss` — Socket Statistics (Modern Replacement)

```bash
# Show all listening TCP/UDP ports
ss -tuln

# Show all connections with process info
ss -tulnp

# Show established connections only
ss -t state established

# Find what is using port 80
ss -tulnp | grep :80

# Show summary
ss -s

# Watch connections in real-time
watch -n 1 ss -tuln
```

> 💡 **`ss` is the modern replacement for `netstat`** — faster and more feature-rich!

---

## DNS Lookup

### `nslookup` — Query DNS Records

```bash
# Basic DNS lookup
nslookup google.com

# Look up specific DNS server
nslookup google.com 8.8.8.8

# Look up MX records (mail servers)
nslookup -type=mx gmail.com

# Reverse lookup (IP to hostname)
nslookup 8.8.8.8

# Test localhost
nslookup localhost
```

---

### `dig` — Advanced DNS Lookup Tool

```bash
# Basic lookup
dig google.com

# Short answer only
dig google.com +short

# Look up specific record types
dig google.com A           # IPv4 address
dig google.com AAAA        # IPv6 address
dig google.com MX          # Mail servers
dig google.com NS          # Name servers
dig google.com TXT         # Text records (SPF, DKIM)
dig google.com CNAME       # Canonical name

# Use specific DNS server
dig @8.8.8.8 google.com

# Reverse lookup
dig -x 8.8.8.8

# Test localhost
dig localhost
```

---

## Downloading & API Requests

### `wget` — Download Files

```bash
# Download a file
wget https://example.com/file.zip

# Download and save with different name
wget -O myfile.zip https://example.com/file.zip

# Download in background
wget -b https://example.com/large_file.iso

# Resume interrupted download
wget -c https://example.com/large_file.iso

# Download multiple URLs from a file
wget -i urls.txt

# Limit download speed
wget --limit-rate=1m https://example.com/file.zip

# Download entire website (recursive)
wget -r -np -k https://example.com/
```

---

### `curl` — Transfer Data / API Requests

```bash
# Basic GET request (show response body)
curl https://api.github.com

# Check HTTP headers only
curl -I https://github.com

# Download and save file
curl -o file.txt https://example.com/file.txt

# Follow redirects
curl -L https://example.com

# POST request with data
curl -X POST -d "name=zohaib&age=25" https://api.example.com/users

# POST with JSON
curl -X POST \
  -H "Content-Type: application/json" \
  -d '{"name":"zohaib","role":"devops"}' \
  https://api.example.com/users

# Authentication
curl -u username:password https://api.example.com

# With API Bearer token
curl -H "Authorization: Bearer YOUR_TOKEN" https://api.example.com

# Show request + response headers
curl -v https://google.com

# Silent (no progress bar)
curl -s https://api.ipify.org

# Check your public IP
curl ifconfig.me
curl api.ipify.org
```

> 💡 **`curl` is essential for DevOps** — testing APIs, webhooks, and health checks!

---

## Network Scanning

### `nmap` — Network Exploration & Port Scanner

```bash
# Install nmap
sudo apt install nmap

# Scan a single host (most common ports)
nmap 192.168.1.1

# Scan localhost
nmap 127.0.0.1
nmap localhost

# TCP Connect scan (full connection)
nmap -sT 127.0.0.1

# Scan specific port
nmap -p 80 192.168.1.1
nmap -p 22,80,443 192.168.1.1

# Scan port range
nmap -p 1-1000 192.168.1.1

# Scan all 65535 ports
nmap -p- 192.168.1.1

# Detect OS and service versions
sudo nmap -O -sV 192.168.1.1

# Scan entire subnet
nmap 192.168.1.0/24

# Quick ping scan (find live hosts)
nmap -sn 192.168.1.0/24

# Aggressive scan (OS, version, scripts)
sudo nmap -A 192.168.1.1
```

> ⚠️ **Only scan networks you own or have permission to scan!**

---

## Firewall with iptables

### `iptables` — Configure Firewall Rules

```bash
# View current rules
sudo iptables -L
sudo iptables -L -v    # verbose (with packet/byte counts)
sudo iptables -L -n    # numeric (no DNS lookup, faster)
sudo iptables -L -v -n --line-numbers   # with line numbers

# Allow incoming SSH (port 22)
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT

# Allow incoming HTTP (port 80)
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT

# Allow incoming HTTPS (port 443)
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT

# Block specific IP address
sudo iptables -A INPUT -s 192.168.1.50 -j DROP

# Allow established connections
sudo iptables -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

# Drop all other incoming (default deny)
sudo iptables -A INPUT -j DROP

# Delete a specific rule (by line number)
sudo iptables -D INPUT 3

# Flush all rules (reset firewall)
sudo iptables -F

# Save rules permanently (Ubuntu/Debian)
sudo apt install iptables-persistent
sudo netfilter-persistent save
```

> 💡 **Modern alternative:** `ufw` (Uncomplicated Firewall) is easier for beginners!

```bash
# UFW (Uncomplicated Firewall) — easier alternative
sudo ufw status
sudo ufw enable
sudo ufw allow ssh
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw deny 23/tcp
sudo ufw status verbose
```

---

## Quick Reference Cheatsheet

| Command | What It Does | Example |
|---|---|---|
| `ping -c 4` | Test connectivity | `ping -c 4 google.com` |
| `traceroute` | Trace network path | `traceroute google.com` |
| `ip addr show` | Show interfaces/IPs | `ip a` |
| `ifconfig` | Show interfaces (legacy) | `ifconfig eth0` |
| `ss -tuln` | Show listening ports | `ss -tuln` |
| `netstat -tulnp` | Show ports + processes | `netstat -tulnp` |
| `nslookup` | DNS lookup (simple) | `nslookup google.com` |
| `dig +short` | DNS lookup (detailed) | `dig google.com +short` |
| `wget -c` | Download (resume) | `wget -c https://url` |
| `curl -I` | HTTP headers | `curl -I https://github.com` |
| `curl -X POST` | POST request | `curl -X POST -d '{}'` |
| `nmap -sT` | Port scan | `nmap -sT 127.0.0.1` |
| `iptables -L -v` | View firewall | `iptables -L -v` |
| `ufw status` | UFW firewall status | `ufw status verbose` |

---

## Practice Exercises

```bash
# 1. Test connectivity
ping -c 4 google.com
ping -c 4 8.8.8.8
traceroute google.com

# 2. Check your network interfaces
ip addr show
ip route show

# 3. Check listening ports
ss -tuln
# Look for commonly used ports: 22 (SSH), 80 (HTTP), 443 (HTTPS)

# 4. DNS lookups
dig google.com +short
nslookup github.com 8.8.8.8

# 5. Make API requests
curl -s https://api.ipify.org    # your public IP
curl -I https://github.com        # HTTP headers
curl -s https://api.github.com/users/Zohaib1195

# 6. Port scan localhost
nmap localhost

# 7. Check firewall rules
sudo iptables -L -n
```

---

> ⬅️ [Back: Users & Permissions](../02-users-permissions/README.md) | ➡️ Next: [Go to Pro Branch](../../../tree/pro)
