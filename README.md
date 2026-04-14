# Penetration Testing Checklist

A structured penetration testing checklist for security professionals and IT teams. This checklist covers the full pen test lifecycle from scoping and rules of engagement through reconnaissance, exploitation, post-exploitation, and reporting. Aligned with the PTES (Penetration Testing Execution Standard), OWASP Testing Guide, and NIST SP 800-115, it provides actionable checklists for network, web application, wireless, cloud, and social engineering assessments.

## Why Use a Penetration Testing Checklist

Penetration testing without a structured methodology leads to inconsistent results, missed attack vectors, and reports that lack the detail needed to drive remediation. Whether you are an internal red team, a consulting firm, or an IT team managing third-party pen tests, a repeatable checklist ensures comprehensive coverage and professional-quality deliverables.

This checklist is organized by assessment type and attack phase. Use it as a starting framework and adapt it to your environment and engagement scope.

---

## Table of Contents

1. [Pre-Engagement](#1-pre-engagement)
2. [Reconnaissance](#2-reconnaissance)
3. [Network Penetration Testing](#3-network-penetration-testing)
4. [Web Application Testing](#4-web-application-testing)
5. [Wireless Network Testing](#5-wireless-network-testing)
6. [Cloud Security Testing](#6-cloud-security-testing)
7. [Social Engineering Testing](#7-social-engineering-testing)
8. [Active Directory / Identity Testing](#8-active-directory--identity-testing)
9. [Post-Exploitation](#9-post-exploitation)
10. [Reporting](#10-reporting)
11. [Tool Reference](#11-tool-reference)
12. [Compliance Requirements](#12-compliance-requirements)

---

## 1. Pre-Engagement

### Scoping

- [ ] Define test type: black box, gray box, or white box
- [ ] Define scope: IP ranges, domains, applications, cloud accounts
- [ ] Document systems explicitly out of scope
- [ ] Identify test type: external, internal, web app, wireless, social engineering, physical
- [ ] Determine testing window (dates, hours, maintenance windows)
- [ ] Identify sensitive/fragile systems requiring special handling
- [ ] Confirm whether denial-of-service testing is permitted
- [ ] Confirm whether social engineering is in scope

### Rules of Engagement (ROE)

- [ ] Get written authorization from system owner (signed letter of engagement)
- [ ] Define acceptable testing techniques and tools
- [ ] Establish emergency contact procedures (if testing causes an issue)
- [ ] Define data handling requirements (how findings are stored, transmitted, destroyed)
- [ ] Agree on communication channels (encrypted email, secure portal)
- [ ] Define rules for third-party hosted systems (cloud providers may require notification)
- [ ] Confirm AWS/Azure/GCP pen test notification requirements (if applicable)

### Credentials and Access (Gray/White Box)

- [ ] Obtain test accounts (standard user, privileged user if applicable)
- [ ] Obtain VPN access or jump box credentials (for internal testing)
- [ ] Obtain network diagrams and architecture documents (white box)
- [ ] Obtain application source code or API documentation (white box)
- [ ] Verify all provided credentials work before the testing window opens

---

## 2. Reconnaissance

### Passive Reconnaissance (OSINT)

- [ ] DNS enumeration (A, AAAA, MX, TXT, NS, SOA, SRV records)
- [ ] Subdomain discovery (subfinder, amass, crt.sh certificate transparency logs)
- [ ] WHOIS and registration information
- [ ] Google dorking (site:target.com filetype:pdf, inurl:admin, etc.)
- [ ] Shodan / Censys / BinaryEdge search for exposed services
- [ ] GitHub / GitLab search for leaked credentials, API keys, internal documentation
- [ ] LinkedIn employee enumeration (for social engineering scope)
- [ ] Wayback Machine for historical content and removed pages
- [ ] Job postings (reveal technology stack)
- [ ] Breached credential databases (Have I Been Pwned API for domains)
- [ ] SSL/TLS certificate analysis (Subject Alternative Names reveal subdomains)
- [ ] ASN and IP range identification

### Active Reconnaissance

- [ ] Port scanning (Nmap: TCP SYN scan, top 1000 ports, then full 65535 if permitted)
- [ ] Service version detection (Nmap -sV)
- [ ] OS fingerprinting (Nmap -O)
- [ ] Banner grabbing (Netcat, curl)
- [ ] Web technology fingerprinting (Wappalyzer, WhatWeb, BuiltWith)
- [ ] Virtual host discovery (Host header enumeration)
- [ ] Web directory and file brute forcing (ffuf, gobuster, dirsearch)
- [ ] API endpoint discovery (checking /swagger, /api-docs, /graphql)
- [ ] Email harvesting (theHarvester, Hunter.io)

---

## 3. Network Penetration Testing

### External Network

- [ ] Identify all internet-facing services
- [ ] Test for default credentials on all discovered services
- [ ] Check for known CVEs on identified service versions
- [ ] Test SSL/TLS configuration (testssl.sh, sslyze)
  - [ ] Expired or self-signed certificates
  - [ ] Weak cipher suites
  - [ ] TLS 1.0/1.1 support
  - [ ] Missing HSTS header
- [ ] Test VPN endpoints for known vulnerabilities
- [ ] Test mail server configuration (open relay, SPF/DKIM/DMARC)
- [ ] Test DNS for zone transfer (dig axfr @ns.target.com target.com)
- [ ] Check for exposed management interfaces (admin panels, consoles)
- [ ] Test for SNMP with default community strings

### Internal Network

- [ ] Network discovery (Nmap, Responder for passive LLMNR/NBT-NS)
- [ ] LLMNR/NBT-NS/mDNS poisoning (Responder)
- [ ] ARP spoofing / MITM (if in scope)
- [ ] SMB enumeration (CrackMapExec, enum4linux-ng)
  - [ ] Null session access
  - [ ] Guest session access
  - [ ] Share enumeration and access
  - [ ] SMB signing status
- [ ] SNMP enumeration (snmpwalk with common community strings)
- [ ] Network share mining for sensitive data (Snaffler)
- [ ] Password spraying against identified services (controlled, account lockout aware)
- [ ] Kerberoasting (GetUserSPNs, Rubeus)
- [ ] AS-REP Roasting (GetNPUsers)
- [ ] NTLM relay attacks (ntlmrelayx, if SMB signing is disabled)
- [ ] IPv6 attacks (mitm6, if IPv6 is enabled but not managed)
- [ ] Exploit identified CVEs (only with approval and in controlled manner)

---

## 4. Web Application Testing

Aligned with the OWASP Testing Guide v4.2 and OWASP Top 10 (2021).

### Information Gathering

- [ ] Fingerprint web server (Apache, Nginx, IIS version)
- [ ] Identify web application framework (Django, .NET, Spring, Express)
- [ ] Map application structure (sitemap, spider)
- [ ] Identify all entry points (forms, parameters, APIs, file uploads)
- [ ] Review robots.txt and sitemap.xml
- [ ] Check for information disclosure in HTTP headers and error messages

### Authentication Testing

- [ ] Test for default credentials
- [ ] Test for username enumeration (different responses for valid vs. invalid users)
- [ ] Test password complexity requirements
- [ ] Test account lockout mechanism
- [ ] Test for brute force protection (rate limiting, CAPTCHA)
- [ ] Test password reset functionality (token predictability, email validation)
- [ ] Test MFA implementation (bypass techniques, missing enforcement)
- [ ] Test session management (session fixation, session ID randomness)
- [ ] Test for credential stuffing protection
- [ ] Check for credentials in URL parameters or JavaScript source

### Authorization Testing

- [ ] Test for horizontal privilege escalation (accessing other users' data)
- [ ] Test for vertical privilege escalation (accessing admin functions as standard user)
- [ ] Test for IDOR (Insecure Direct Object Reference) on all parameter IDs
- [ ] Test API authorization (missing auth on endpoints, token validation)
- [ ] Test for path traversal (../../../etc/passwd)
- [ ] Test for forced browsing (accessing /admin, /backup, /debug)

### Input Validation Testing

- [ ] Test for SQL injection (SQLMap, manual payloads)
  - [ ] Error-based
  - [ ] Union-based
  - [ ] Blind (boolean, time-based)
  - [ ] Second-order
- [ ] Test for Cross-Site Scripting (XSS)
  - [ ] Reflected XSS
  - [ ] Stored XSS
  - [ ] DOM-based XSS
- [ ] Test for Cross-Site Request Forgery (CSRF)
- [ ] Test for Server-Side Request Forgery (SSRF)
- [ ] Test for XML External Entity (XXE) injection
- [ ] Test for Server-Side Template Injection (SSTI)
- [ ] Test for command injection (OS command, LDAP, XPATH)
- [ ] Test for deserialization vulnerabilities
- [ ] Test file upload functionality
  - [ ] Allowed file types (bypass extension filters)
  - [ ] File content validation (magic bytes)
  - [ ] Upload path (can you upload to web-accessible directories?)
  - [ ] Filename sanitization (path traversal in filename)

### Business Logic Testing

- [ ] Test for rate limiting bypass
- [ ] Test for price manipulation (changing prices in requests)
- [ ] Test for quantity manipulation (negative quantities, overflow)
- [ ] Test for workflow bypass (skipping steps in multi-step processes)
- [ ] Test for race conditions (simultaneous requests)
- [ ] Test for coupon/promo code abuse

### API-Specific Testing

- [ ] Test API authentication (missing auth, broken token validation)
- [ ] Test for excessive data exposure (API returning more data than needed)
- [ ] Test for mass assignment (sending unexpected parameters)
- [ ] Test rate limiting on all endpoints
- [ ] Test for BOLA (Broken Object Level Authorization) -- OWASP API Top 10 #1
- [ ] Check for API documentation exposure (/swagger, /graphql introspection)

---

## 5. Wireless Network Testing

- [ ] Identify all SSIDs (including hidden networks)
- [ ] Determine encryption type (WPA3, WPA2, WPA, WEP, Open)
- [ ] Test for WPA2 handshake capture and offline cracking
- [ ] Test for WPS PIN brute force (Reaver/Bully)
- [ ] Test for evil twin / rogue AP attacks
- [ ] Test guest network isolation (can guests reach internal resources?)
- [ ] Test for 802.1X EAP downgrade attacks
- [ ] Test for KARMA/known-beacons attacks on client devices
- [ ] Verify wireless IDS/IPS detection capability
- [ ] Check for management interface access from wireless network

---

## 6. Cloud Security Testing

### AWS

- [ ] Test IAM policy misconfigurations (overly permissive policies)
- [ ] Test for public S3 buckets (list, read, write)
- [ ] Test EC2 metadata service access from applications (SSRF to 169.254.169.254)
- [ ] Test security group rules (overly permissive inbound rules)
- [ ] Test for exposed RDS/Elasticsearch/other managed services
- [ ] Test Lambda function permissions and environment variables
- [ ] Review CloudTrail logging (is it enabled, is it monitored?)
- [ ] Check for access keys in code repositories

### Azure

- [ ] Test Entra ID (Azure AD) configuration
- [ ] Test for overly permissive Azure RBAC roles
- [ ] Test for exposed storage accounts (Blob, Table, Queue)
- [ ] Test NSG rules for excessive permissiveness
- [ ] Test Azure Key Vault access policies
- [ ] Check for Azure Activity Log monitoring
- [ ] Test managed identity permissions

### GCP

- [ ] Test IAM policy bindings
- [ ] Test for public Cloud Storage buckets
- [ ] Test firewall rules
- [ ] Test for exposed BigQuery datasets
- [ ] Check for audit logging configuration
- [ ] Test service account key management

### General Cloud

- [ ] Test for cross-account/cross-tenant access
- [ ] Test for insecure serverless function configurations
- [ ] Check container registry for public access
- [ ] Test Kubernetes/EKS/AKS/GKE RBAC and pod security

---

## 7. Social Engineering Testing

### Phishing Campaign

- [ ] Develop phishing pretext (aligned with engagement scope)
- [ ] Register look-alike domain (if in scope)
- [ ] Set up phishing infrastructure (GoPhish or similar)
- [ ] Create landing page (credential capture or payload delivery)
- [ ] Send phishing emails to target list
- [ ] Track: delivery rate, open rate, click rate, credential submission rate
- [ ] Document timeline and results

### Vishing (Phone-Based)

- [ ] Develop vishing pretext and script
- [ ] Identify targets (front desk, help desk, executives)
- [ ] Conduct calls (record if legally permitted and approved)
- [ ] Track: calls made, information disclosed, access granted
- [ ] Document findings and awareness gaps

### Physical (If In Scope)

- [ ] Attempt tailgating / piggybacking
- [ ] Test badge access controls
- [ ] Test for unlocked workstations
- [ ] Drop USB drives (rubber ducky / HID attack, if approved)
- [ ] Check for sensitive documents left in public areas
- [ ] Test dumpster diving (if approved)
- [ ] Document all physical security findings with photographs

---

## 8. Active Directory / Identity Testing

- [ ] Enumerate domain users, groups, and computers (BloodHound, PowerView)
- [ ] Identify attack paths to Domain Admin (BloodHound)
- [ ] Kerberoasting (request TGS tickets for service accounts, crack offline)
- [ ] AS-REP Roasting (find accounts without pre-authentication)
- [ ] Password spraying (one password against all users, respect lockout policy)
- [ ] Check for unconstrained delegation
- [ ] Check for constrained delegation abuse
- [ ] Test for DCSync permissions (non-DA accounts with replication rights)
- [ ] Test for LAPS deployment and access controls
- [ ] Check for GPP (Group Policy Preferences) passwords
- [ ] Check for certificates misconfigurations (AD CS -- ESC1 through ESC8)
- [ ] Test for Silver Ticket / Golden Ticket feasibility
- [ ] Check for stale/disabled accounts with active permissions
- [ ] Review password policy strength

---

## 9. Post-Exploitation

After gaining access, demonstrate the business impact (only within scope and ROE).

- [ ] Determine current user context and privileges
- [ ] Attempt privilege escalation to local admin / root
- [ ] Attempt lateral movement to additional systems
- [ ] Attempt domain privilege escalation (to Domain Admin)
- [ ] Access sensitive data (PII, PHI, financial data, CUI -- within ROE)
- [ ] Demonstrate data exfiltration (simulate, do not actually exfiltrate real data without approval)
- [ ] Establish persistence (document method, remove during cleanup)
- [ ] Pivot to segmented networks (demonstrate segmentation bypass)
- [ ] Document the full attack chain from initial access to objective

### Cleanup Checklist

- [ ] Remove all tools uploaded to target systems
- [ ] Remove all test accounts created
- [ ] Remove all persistence mechanisms
- [ ] Remove all backdoors, reverse shells, and web shells
- [ ] Restore any modified configurations
- [ ] Provide client with list of all changes made during testing
- [ ] Delete all test data from target systems

---

## 10. Reporting

### Report Structure

A professional pen test report contains these sections:

1. **Executive Summary** -- Business-level overview, risk rating, key findings (1-2 pages)
2. **Scope and Methodology** -- What was tested, how, rules of engagement
3. **Summary of Findings** -- Table/chart of all findings by severity
4. **Detailed Findings** -- Each finding with description, evidence, impact, remediation
5. **Attack Narrative** -- Story of the attack chain from recon to objective (this is what makes pen tests valuable beyond vulnerability scans)
6. **Remediation Roadmap** -- Prioritized remediation plan with quick wins and strategic improvements
7. **Appendix** -- Raw scan output, tool logs, supporting evidence

### Finding Severity Ratings

| Rating | Definition | Business Impact |
|--------|-----------|----------------|
| **Critical** | Immediately exploitable with severe impact | Full system compromise, mass data breach, business disruption |
| **High** | Exploitable with significant impact | Individual system compromise, sensitive data exposure |
| **Medium** | Exploitable with moderate impact or requires chaining | Limited data exposure, requires additional steps to exploit fully |
| **Low** | Minor issue or informational | Best practice violation, minimal direct impact |
| **Informational** | Observation, not directly exploitable | Security improvement opportunity |

### Finding Template

```
FINDING: [Title]
=================
Severity: [Critical / High / Medium / Low]
CVSS Score: [If applicable]
Affected Systems: [List]
CWE: [CWE-XXX if applicable]

Description:
[Technical description of the vulnerability]

Steps to Reproduce:
1. [Step-by-step reproduction instructions]
2.
3.

Evidence:
[Screenshots, terminal output, proof of exploitation]

Impact:
[Business impact -- what could an attacker achieve?]

Remediation:
[Specific remediation steps]

References:
[CVEs, OWASP references, vendor advisories]
```

---

## 11. Tool Reference

### Reconnaissance

| Tool | Purpose | Link |
|------|---------|------|
| Nmap | Port scanning, service detection | nmap.org |
| Amass | Subdomain enumeration | github.com/owasp-amass/amass |
| Subfinder | Fast subdomain discovery | github.com/projectdiscovery/subfinder |
| theHarvester | Email and subdomain harvesting | github.com/laramies/theHarvester |
| Shodan | Internet-connected device search | shodan.io |

### Exploitation

| Tool | Purpose | Link |
|------|---------|------|
| Metasploit | Exploitation framework | metasploit.com |
| CrackMapExec | Windows/AD exploitation suite | github.com/byt3bl33d3r/CrackMapExec |
| Impacket | Network protocol exploitation | github.com/fortra/impacket |
| SQLMap | SQL injection automation | sqlmap.org |
| Burp Suite | Web application testing | portswigger.net |

### Active Directory

| Tool | Purpose | Link |
|------|---------|------|
| BloodHound | AD attack path analysis | github.com/BloodHoundAD/BloodHound |
| Rubeus | Kerberos abuse | github.com/GhostPack/Rubeus |
| Certipy | AD CS exploitation | github.com/ly4k/Certipy |
| PowerView | AD enumeration | github.com/PowerShellMafia/PowerSploit |
| Mimikatz | Credential extraction | github.com/gentilkiwi/mimikatz |

### Wireless

| Tool | Purpose | Link |
|------|---------|------|
| Aircrack-ng | Wireless auditing suite | aircrack-ng.org |
| Bettercap | Network attacks, WiFi | bettercap.org |
| Reaver | WPS brute force | github.com/t6x/reaver-wps-fork-t6x |
| EAPHammer | WPA2-Enterprise attacks | github.com/s0lst1c3/eaphammer |

### Reporting

| Tool | Purpose | Link |
|------|---------|------|
| Ghostwriter | Pen test reporting platform | github.com/GhostManager/Ghostwriter |
| Dradis | Collaborative reporting | dradis.com |
| PlexTrac | Pen test management and reporting | plextrac.com |
| SysReptor | Security report builder | github.com/Syslifters/sysreptor |

---

## 12. Compliance Requirements

### Penetration Testing Requirements by Framework

| Framework | Requirement | Frequency | Key Details |
|-----------|------------|-----------|-------------|
| **PCI DSS 4.0** | Req 11.4: External and internal penetration testing | Annual + after significant changes | Must follow industry-accepted methodology (PTES, OWASP, NIST) |
| **CMMC Level 2** | CA.L2-3.12.1: Periodic security assessments | Periodic (annual recommended) | Must assess all CUI systems |
| **HIPAA** | 164.308(a)(8): Technical evaluation | After environmental changes | Pen testing is a best practice, not explicitly required |
| **SOC 2** | CC4.1/CC7.1: Testing of controls | Annual | Often expected by auditors |
| **ISO 27001** | A.18.2.3: Technical compliance review | As defined in risk treatment | Pen testing is a common implementation of this control |
| **FedRAMP** | RA-5(5): Privileged access analysis | Annual | Third-party assessor required |
| **NIST CSF** | DE.CM-8: Vulnerability scans performed | As defined | Pen testing extends vulnerability scanning |
| **GDPR** | Art. 32(1)(d): Regularly testing security | Regular | Pen testing demonstrates "testing, assessing, and evaluating" |

---

## About

Created by [Petronella Technology Group](https://petronellatech.com), a cybersecurity and managed IT services firm based in Raleigh, NC. We perform penetration tests, vulnerability assessments, and red team engagements for businesses in healthcare, defense, finance, and professional services.

CMMC-RP Certified | BBB A+ Since 2003 | 23+ Years of Cybersecurity Experience

Contact us: **(919) 348-4912** | [petronellatech.com/contact-us](https://petronellatech.com/contact-us/)

### Related Resources

- [CMMC Compliance Guide](https://petronellatech.com/compliance/cmmc-compliance-guide/)
- [Cybersecurity Services](https://petronellatech.com/cyber-security/)
- [Virtual CISO](https://petronellatech.com/solutions/vciso/)
- [Emergency IT Support](https://petronellatech.com/contact-us/emergency-room/)
- [Free Security Assessment](https://petronellatech.com/contact-us/)

## Professional Compliance Services

Need help with penetration testing or security assessments? **Petronella Technology Group** provides comprehensive cybersecurity services:

- [Cybersecurity Services](https://petronellatech.com/cybersecurity/) - Managed security and assessments
- [CMMC Compliance Guide](https://petronellatech.com/compliance/cmmc-compliance-guide/) - Full CMMC Level 2 preparation
- [HIPAA Compliance Services](https://petronellatech.com/compliance/hipaa-compliance/) - Healthcare security assessments
- [AI-Powered Security](https://petronellatech.com/ai/ai-services/) - AI infrastructure with compliance built in

**Petronella Technology Group** is a CMMC-RP certified cybersecurity firm headquartered in Raleigh, NC. Our entire team holds CMMC Registered Practitioner credentials. [Contact us](https://petronellatech.com/contact-us/) or call (919) 348-4912.

## License

MIT License -- see [LICENSE](LICENSE) for details.
