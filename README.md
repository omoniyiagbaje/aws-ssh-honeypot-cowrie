# aws-ssh-honeypot-cowrie

# Cloud SSH Honeypot Lab – AWS + Cowrie

## Overview

This project documents the deployment of a cloud-based SSH honeypot using AWS EC2 and Cowrie to simulate and monitor real-world brute-force attacks.

The objective of this lab was to:

- Deploy a secure cloud-hosted honeypot
- Capture live attacker telemetry
- Analyse SSH brute-force attempts
- Extract Indicators of Compromise (IOCs)
- Perform threat intelligence enrichment
- Demonstrate SOC-style investigation workflow

---

## Architecture

Internet
↓
AWS EC2 (Ubuntu Server)
↓
Cowrie SSH Honeypot (Port 2222)
↓
JSON Log Collection
↓
Log Analysis & Threat Enrichment

---

## Environment Details

- **Cloud Provider:** AWS  
- **Instance Type:** t2.micro (Free Tier)  
- **OS:** Ubuntu Server 22.04 LTS  
- **Honeypot Software:** Cowrie  
- **Exposed Port:** 2222 (SSH Honeypot)  
- **Real SSH (22):** Restricted to My IP only  

---

##  Security Configuration

Security Group Rules:

| Port | Purpose | Source |
|------|---------|--------|
| 22   | Administrative SSH | My IP only |
| 2222 | Honeypot SSH | 0.0.0.0/0 |

This ensured:
- Administrative access was protected
- Honeypot traffic was publicly accessible
- No unnecessary services were exposed

---

## Honeypot Deployment Steps

1. Launched AWS EC2 instance
2. Installed system dependencies
3. Installed Cowrie in a Python virtual environment
4. Configured Cowrie to listen on port 2222
5. Started honeypot service
6. Monitored logs in real-time

Cowrie log location:

/home/cowrie/cowrie/var/log/cowrie/

---

## Log Analysis

### Extracted Login Attempts

cat var/log/cowrie/cowrie.log | grep "login attempt"

Observed:

- Username: `root`
- Passwords: `0601`, `7858`
- 3 successful (simulated) login events

---

### Extracted Unique Source IPs

cat var/log/cowrie/cowrie.json | jq -r '.src_ip' | sort | uniq


Observed IPs:

- 81.104.146.152
- 107.20.35.87

---

### Commands Executed

cat var/log/cowrie/cowrie.log | grep "CMD:"

Observed:

- `exit`

---

### Session Duration

Observed session durations:

- 23.2 seconds  
- 59.7 seconds  

This indicates interactive SSH sessions rather than automated instant disconnect scans.

---

## Threat Intelligence Enrichment

Used IP enrichment:

curl ipinfo.io/81.104.146.152

Results:

- ISP: Virgin Media  
- City: Leeds  
- Country: GB  
- ASN: AS5089  

 Note: Residential IP space does not confirm attacker identity. It may indicate:
- Compromised host
- VPN exit node
- Botnet activity

---

## Key Findings

- Public SSH services are constantly scanned.
- Root account is a primary brute-force target.
- Weak numeric passwords are commonly attempted.
- Honeypots provide safe attacker interaction telemetry.
- Residential IP ranges may be used for scanning activity.

---

## Security Insights

If this were a production system, recommended mitigations:

- Disable root SSH login
- Implement key-based authentication
- Enforce strong password policies
- Use fail2ban or rate-limiting
- Monitor login attempts via SIEM

---

## Skills Demonstrated

- AWS EC2 deployment
- Linux system administration
- Firewall / Security Group configuration
- Python virtual environment management
- Honeypot configuration
- Log parsing (grep, jq, awk)
- JSON analysis
- IOC extraction
- Threat intelligence enrichment
- SOC-style investigation reporting

---

## Future Enhancements

- Forward logs to SIEM (Splunk / OpenSearch)
- Create detection rules
- Build attack dashboards
- Automate enrichment pipeline
- Deploy multiple honeypots for comparison

---

## Conclusion

This lab successfully demonstrated the deployment and analysis of a cloud-hosted SSH honeypot.

The exercise simulated real-world attack telemetry collection and investigation workflows typically performed by SOC analysts.

It provided hands-on experience in:

- Cloud security configuration
- Attack surface exposure
- Log-based detection
- Threat enrichment
- Incident-style reporting

---

## Disclaimer

This honeypot was deployed in a controlled lab environment.  
No production systems were exposed.  
All activity was monitored passively for research and educational purposes.


