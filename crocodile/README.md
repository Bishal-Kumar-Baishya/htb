# Crocodile Machine - HTB

## Machine Info
- **Difficulty:** Easy
- **OS:** Linux

## Vulnerabilities
1. **Anonymous FTP Access** — FTP allows anonymous login without credentials
2. **Exposed Credential Files** — Usernames and passwords stored in plain text on FTP
3. **Weak Authentication** — Default admin credentials used for web dashboard
4. **Information Disclosure** — Flag displayed directly on authenticated dashboard

## Attack Chain

### 1. Reconnaissance
```bash
sudo nmap -sC -sV 10.129.1.15
```
**Explanation:** Runs comprehensive port scan with service detection and default scripts. Results: FTP (21) with anonymous access allowed, HTTP (80) Apache web server.

### 2. FTP Anonymous Access
```bash
ftp 10.129.1.15
# Login as: anonymous
# Password: [leave blank or use anonymous]
```
**Explanation:** Connects to FTP server. Anonymous login is allowed (discovered from nmap). Reveals two files: `allowed.userlist` and `allowed.userlist.passwd`.

### 3. Download Credential Files
```bash
get allowed.userlist
get allowed.userlist.passwd
```
**Explanation:** Downloads both files containing usernames and passwords in plain text.

### 4. Read Credentials
```bash
cat allowed.userlist
cat allowed.userlist.passwd
```
**Explanation:** Displays the contents of credential files. Found 4 usernames (aron, pwnmeow, egotisticalsw, admin) and 4 passwords.

### 5. Directory Brute-Force
```bash
gobuster dir -u http://10.129.1.15 -w /usr/share/wordlists/dirb/common.txt
```
**Explanation:** Scans for common directories on web server. Discovers `/dashboard` directory.

### 6. PHP File Enumeration
```bash
gobuster dir -u http://10.129.1.15 -w /usr/share/wordlists/dirb/common.txt -x php
```
**Explanation:** Extends gobuster search to include `.php` files. Finds `login.php` — the authentication entry point.

### 7. Web Authentication
- Navigated to `http://10.129.1.15/dashboard/`
- Prompted for login
- Used username: `admin` with matching password from credential files
- Successfully authenticated

### 8. Flag Extraction
- Dashboard displayed: `c7110277ac44d78b6a9fff2232434d16`
- Flag obtained directly from authenticated dashboard

## Key Learnings
- Always check FTP for anonymous access — often reveals sensitive files
- Use `-x` flag with gobuster to search for specific file extensions
- Credential files stored on servers are critical vulnerabilities
- Never use default credentials; rotate them regularly

## Lessons for Defense
- Disable anonymous FTP access
- Never store credentials in plain text files
- Use strong, unique credentials for all accounts
- Hide sensitive directories behind proper authentication