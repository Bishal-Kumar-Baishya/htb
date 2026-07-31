# Cap Machine - HTB

## Machine Info
- **Difficulty:** Easy
- **OS:** Linux

## Vulnerabilities
1. **IDOR (Insecure Direct Object Reference)** — Access other users' data via URL parameter manipulation
2. **Plaintext Credentials in PCAP** — Network traffic captured in unencrypted format
3. **Linux Capability Escalation** — Python binary with `cap_setuid` capability

## Attack Chain

### 1. Reconnaissance
- Nmap scan revealed FTP (21), SSH (22), HTTP (80)
- Anonymous FTP login blocked
- HTTP Gunicorn app initially hanging (MTU issue — reduced to 1200)

### 2. IDOR Exploitation
- Logged into web app as `nathan` user
- Found "snapshot" feature at `/data/1`
- Changed URL to `/data/0` → accessed another user's data
- Downloaded pcap file

### 3. Credential Extraction
- Opened pcap in Wireshark
- Analyzed network packets
- Found plaintext FTP credentials

### 4. Lateral Movement
- SSH into machine using extracted credentials
- Gained shell access as `nathan`

### 5. Privilege Escalation
- Ran `getcap -r / 2>/dev/null`
- Found Python3.8 with `cap_setuid` capability
- Wrote Python script using `os.setuid(0)` to become root
- Executed shell as root

### Key Commands
```bash
# Scan
sudo nmap -sC -sV -v 10.129.x.x

# Fix MTU hang
sudo ip link set dev tun0 mtu 1200

# Check capabilities
getcap -r / 2>/dev/null

# Privilege escalation
python3 -c "import os; os.setuid(0); os.system('/bin/bash')"
```

## Lessons Learned
- Always test IDOR vulnerabilities by changing object IDs
- Analyze pcap files for sensitive data leakage
- Check binary capabilities, not just SUID bits
- MTU issues can cause network hangs on VPN tunnels