# Cybersecurity Fundamentals: Interview Preparation Guide

A structured question-and-answer reference covering the core topics asked in security analyst, security engineer, SOC, GRC, and IAM interviews at product companies, service companies, and MNCs.

---

## Table of Contents

1. [CIA Triad: Confidentiality, Integrity and Availability](#1-cia-triad-confidentiality-integrity-and-availability)
2. [Authentication and Authorization](#2-authentication-and-authorization)
3. [IAM (Identity and Access Management)](#3-iam-identity-and-access-management)
4. [Role-Based Access Control (RBAC)](#4-role-based-access-control-rbac)
5. [Network Security Fundamentals](#5-network-security-fundamentals)
6. [Firewalls and VPN](#6-firewalls-and-vpn)
7. [Vulnerability Assessment](#7-vulnerability-assessment)
8. [Threat Detection and Monitoring](#8-threat-detection-and-monitoring)
9. [Security Auditing](#9-security-auditing)
10. [Incident Response](#10-incident-response)
11. [Scenario-Based Questions](#11-scenario-based-questions)
12. [Quick Revision Cheat Sheet](#12-quick-revision-cheat-sheet)

---

## 1. CIA Triad: Confidentiality, Integrity and Availability

### Q1.1. What is the CIA Triad?
The CIA Triad is the foundational model of information security:

| Pillar | Goal | Typical Controls |
|--------|------|------------------|
| **Confidentiality** | Only authorized parties can access data | Encryption, access control, MFA, data classification |
| **Integrity** | Data remains accurate and unaltered | Hashing, digital signatures, checksums, version control, change management |
| **Availability** | Systems and data are accessible when needed | Redundancy, backups, load balancing, DDoS protection, disaster recovery |

### Q1.2. Give a real-world example of a violation of each pillar.
- **Confidentiality:** a database leak exposes customer records.
- **Integrity:** an attacker modifies a bank transfer amount in transit, or a file is tampered with.
- **Availability:** a ransomware attack or DDoS attack takes a service offline.

### Q1.3. How do the pillars sometimes conflict?
Strong confidentiality controls (heavy encryption, strict access rules, MFA prompts) can reduce availability and usability. Maximum availability (open access, many replicas) can increase exposure. Security design is about balancing the three according to the business risk and the data's classification.

### Q1.4. What is non-repudiation?
Assurance that a party cannot deny having performed an action (sending a message, approving a transaction). It is achieved with **digital signatures**, **audit logs**, and **timestamps**. Some models extend the triad with authenticity and non-repudiation.

### Q1.5. What is the difference between a threat, a vulnerability, and a risk?
- **Threat:** a potential cause of harm (attacker, malware, natural disaster).
- **Vulnerability:** a weakness that can be exploited (unpatched software, weak password).
- **Risk:** the likelihood and impact of a threat exploiting a vulnerability.

*Conceptually:* `Risk = Threat × Vulnerability × Impact`. Risk can be mitigated, transferred, accepted, or avoided.

### Q1.6. What is defense in depth?
A layered security strategy in which multiple independent controls protect an asset, so failure of one layer does not lead to compromise. Layers include physical security, network, host, application, data, and user awareness, as well as policies and procedures.

### Q1.7. What is the difference between symmetric and asymmetric encryption?

| Feature | Symmetric | Asymmetric |
|---------|-----------|------------|
| Keys | One shared secret key | Public/private key pair |
| Speed | Fast | Slower |
| Use | Bulk data encryption | Key exchange, digital signatures |
| Examples | AES, ChaCha20 | RSA, ECC, Diffie-Hellman |
| Challenge | Secure key distribution | Computational cost |

In practice (e.g., TLS), asymmetric cryptography establishes a session key, and symmetric encryption protects the data.

### Q1.8. What is the difference between hashing, encryption, and encoding?
- **Hashing:** one-way function producing a fixed-length digest (SHA-256, bcrypt). Used for integrity and password storage.
- **Encryption:** reversible with a key; protects confidentiality (AES, RSA).
- **Encoding:** reversible without a key; for data representation (Base64, URL encoding). It provides **no** security.

### Q1.9. How does TLS provide the CIA properties?
During the TLS handshake the client verifies the server's certificate (authentication), negotiates a shared session key (via ECDHE or similar), and then encrypts traffic (confidentiality) using authenticated encryption such as AES-GCM, which also detects tampering (integrity). TLS 1.3 reduces the handshake to one round trip and removes legacy, insecure algorithms.

### Q1.10. What are the types of security controls?
- **By function:** preventive (firewall, MFA), detective (IDS, log monitoring), corrective (patching, restoring backups), deterrent (warning banners), compensating (alternate control when primary is not feasible), recovery.
- **By nature:** administrative (policies, training), technical (encryption, ACLs), physical (locks, CCTV).

---

## 2. Authentication and Authorization

### Q2.1. What is the difference between authentication and authorization?
- **Authentication (AuthN):** verifying *who you are* (password, biometrics, token).
- **Authorization (AuthZ):** determining *what you are allowed to do* after identity is established.
- **Accounting/Auditing:** recording what you did. Together they form **AAA**.

### Q2.2. What are the authentication factors?
1. **Something you know:** password, PIN.
2. **Something you have:** hardware token, authenticator app, smart card.
3. **Something you are:** fingerprint, face, iris.
4. Additional context: **somewhere you are** (location) and **something you do** (behavior).

**MFA** requires two or more *different* factor categories. A password plus a security question is *not* MFA.

### Q2.3. How should passwords be stored securely?
- Never store plaintext or reversibly encrypted passwords.
- Use a slow, salted, adaptive hash: **Argon2id**, **bcrypt**, **scrypt**, or **PBKDF2**.
- A unique random **salt** per password defeats rainbow tables; a **pepper** (secret stored outside the database) adds another layer.
- Fast hashes such as MD5 or plain SHA-256 are unsuitable for passwords.

### Q2.4. What is SSO and how does it work?
**Single Sign-On** lets a user authenticate once with an identity provider (IdP) and access multiple applications (service providers) without logging in again. It is implemented through federation protocols such as SAML, OpenID Connect, and Kerberos. Benefits: fewer passwords, centralized policy and logging. Risk: the IdP becomes a high-value target, so it needs strong MFA and monitoring.

### Q2.5. Compare SAML, OAuth 2.0, and OpenID Connect.

| Protocol | Purpose | Format | Typical Use |
|----------|---------|--------|-------------|
| **SAML 2.0** | Authentication (SSO) and attribute exchange | XML assertions | Enterprise web SSO |
| **OAuth 2.0** | **Authorization** (delegated access) | Access tokens | API access, "Allow app to read your calendar" |
| **OpenID Connect (OIDC)** | Authentication layer on top of OAuth 2.0 | JWT ID token | Modern web/mobile login |

OAuth 2.0 alone is not an authentication protocol.

### Q2.6. How does Kerberos work at a high level?
A ticket-based protocol used in Active Directory:
1. The client authenticates to the **KDC** (Key Distribution Center) and receives a **TGT** (Ticket Granting Ticket).
2. The client presents the TGT to the KDC to request a **service ticket** for a specific service.
3. The client presents the service ticket to the service.

Passwords are never sent over the network. Known attacks include Kerberoasting, Golden Ticket, and Pass-the-Ticket, which is why strong service account passwords and monitoring are essential.

### Q2.7. What is a JWT and what are the security considerations?
A **JSON Web Token** is a signed (and optionally encrypted) token with a header, payload (claims), and signature.

Best practices:
- Verify the signature and explicitly allow only expected algorithms (reject `alg: none`).
- Validate `exp`, `iss`, and `aud` claims.
- Keep lifetimes short and use refresh tokens with rotation.
- Do not store sensitive data in the payload (it is only Base64-encoded unless encrypted).
- Prefer secure, `HttpOnly` cookies over `localStorage` to reduce theft through XSS.

### Q2.8. How should sessions be managed securely?
- Generate long, random, unpredictable session IDs.
- Regenerate the session ID after login to prevent fixation.
- Set cookie flags: `Secure`, `HttpOnly`, `SameSite`.
- Apply idle and absolute timeouts and invalidate sessions server-side on logout.
- Protect against CSRF with anti-forgery tokens or `SameSite` cookies.

### Q2.9. How do you defend against brute force and credential stuffing?
- Rate limiting, progressive delays, and CAPTCHA after repeated failures.
- Account lockout or step-up authentication (with care to avoid denial of service).
- MFA, which neutralizes most stolen-password attacks.
- Checking passwords against breached-password lists.
- Monitoring for distributed login attempts and unusual geographies.
- Bot detection and device fingerprinting.

### Q2.10. What is passwordless authentication and FIDO2/WebAuthn?
Authentication without shared secrets. **FIDO2/WebAuthn** uses public-key cryptography: the device holds a private key (in a security key, TPM, or secure enclave), and the server stores only the public key. Credentials are bound to the origin, which makes them **phishing-resistant**. Passkeys are a user-friendly implementation of this approach.

---

## 3. IAM (Identity and Access Management)

### Q3.1. What is IAM?
A framework of policies, processes, and technologies ensuring that the **right identities** (people, services, devices) have the **right access** to the **right resources** at the **right time**, and for the right reasons. Core functions: identity lifecycle, authentication, authorization, governance, and auditing.

### Q3.2. What are the main components of an IAM system?
- Identity repository / directory (Active Directory, LDAP, cloud directories)
- Authentication services (SSO, MFA)
- Authorization engine (RBAC/ABAC policies)
- Identity governance and administration (IGA): provisioning, access reviews
- Privileged Access Management (PAM)
- Audit, logging, and reporting

### Q3.3. Explain the Joiner-Mover-Leaver (JML) process.
- **Joiner:** provision accounts and baseline access according to role on day one.
- **Mover:** adjust access when a user changes role; **remove** old entitlements to prevent privilege creep.
- **Leaver:** promptly disable accounts, revoke tokens and sessions, recover assets, and archive data.

Automating JML from the HR system (the source of truth) reduces orphaned accounts and delays.

### Q3.4. What is the principle of least privilege?
Users, applications, and services should receive only the minimum permissions required to perform their tasks, for the minimum time required. It limits the blast radius of compromised credentials and insider misuse. Implementation methods include role design, just-in-time (JIT) access, and regular access reviews.

### Q3.5. What is separation of duties (SoD)?
Splitting critical tasks across multiple people so no single individual can complete a sensitive process alone, for example, the person who creates a vendor should not approve payments to that vendor. SoD reduces fraud and error risk and is commonly tested in audits (SOX).

### Q3.6. What is PAM (Privileged Access Management)?
Controls for high-risk accounts (administrators, root, service accounts). Capabilities include:
- Credential vaulting and automatic rotation
- Session recording and monitoring
- Just-in-time and time-bound elevation
- Approval workflows and break-glass accounts
- Removal of standing privileges

### Q3.7. What is an access review (recertification)?
A periodic process in which managers or resource owners verify that each user's access is still appropriate. It detects privilege creep, orphaned accounts, and SoD violations. It is a standard audit requirement under frameworks such as SOX, ISO 27001, and SOC 2.

### Q3.8. What is identity federation?
A trust relationship between identity domains so users from one organization (IdP) can access resources in another (SP) using their home credentials, via SAML or OIDC. It removes the need to create separate accounts per partner or application.

### Q3.9. How are IAM concepts applied in the cloud?
- **Identities:** users, groups, roles, and service principals / workload identities.
- **Policies:** JSON-based permission documents (AWS IAM policies, Azure RBAC, GCP IAM).
- Use **roles with temporary credentials** instead of long-lived access keys.
- Enforce MFA on privileged users, avoid use of the root account, and apply permission boundaries and service control policies.
- Evaluate **policy evaluation logic**: explicit deny overrides allow; default is deny.

### Q3.10. How do you manage service accounts and secrets?
- Inventory and assign an owner to every non-human identity.
- Store secrets in a vault (HashiCorp Vault, AWS Secrets Manager, Azure Key Vault), never in code or repositories.
- Rotate credentials automatically and prefer short-lived tokens or workload identity federation.
- Scope permissions narrowly and monitor usage.
- Scan repositories for leaked secrets.

### Q3.11. What is Zero Trust?
A security model based on "**never trust, always verify**." No user or device is trusted by default based on network location. Core principles: verify explicitly (identity, device health, context), enforce least privilege, and assume breach. Implementation involves strong IAM, MFA, micro-segmentation, continuous monitoring, and device posture checks.

### Q3.12. What are LDAP and Active Directory?
- **LDAP:** a protocol for querying and modifying directory services (hierarchical data such as users and groups).
- **Active Directory (AD):** Microsoft's directory service providing authentication (Kerberos/NTLM), authorization, group policy, and LDAP access in Windows environments. AD is a frequent attack target (e.g., Kerberoasting, DCSync), so hardening and tiered administration are critical.

---

## 4. Role-Based Access Control (RBAC)

### Q4.1. What is RBAC?
An access control model in which permissions are assigned to **roles**, and users are assigned to roles, rather than permissions being granted to individuals directly. Example: the `Payroll Analyst` role has read access to payroll data; any user in that role inherits it.

### Q4.2. What are the benefits of RBAC?
- Simplified administration and onboarding
- Consistent enforcement of least privilege
- Easier audits and access reviews
- Reduced human error
- Scales with organizational growth

### Q4.3. Compare RBAC with DAC, MAC, ABAC, and ReBAC.

| Model | Decision Basis | Typical Use |
|-------|---------------|-------------|
| **DAC** (Discretionary) | Resource owner decides who gets access | File systems (Unix permissions) |
| **MAC** (Mandatory) | System-enforced labels/clearance levels | Military, SELinux |
| **RBAC** | User's role | Enterprise applications |
| **ABAC** (Attribute-Based) | Attributes of user, resource, action, environment | Fine-grained, context-aware policies |
| **ReBAC** (Relationship-Based) | Relationships between entities | Collaboration apps, social graphs |

### Q4.4. What is role explosion and how do you prevent it?
When too many highly specific roles are created, role management becomes unmanageable. Prevention:
- Use role hierarchies and inheritance.
- Combine RBAC with ABAC for contextual conditions (department, location, time).
- Perform role mining and periodic role rationalization.
- Define clear role ownership and naming standards.

### Q4.5. How would you design and implement RBAC in an organization?
1. Inventory applications, resources, and permissions.
2. Perform **role engineering**: top-down (analyze business functions) and bottom-up (role mining from existing access).
3. Define roles, permissions, and hierarchies; document owners.
4. Map SoD constraints.
5. Pilot, implement, and automate assignments via JML workflows.
6. Review regularly and monitor exceptions.

### Q4.6. What are role hierarchies and constraints in RBAC?
- **Hierarchy:** senior roles inherit permissions of junior roles (e.g., `Senior Engineer` inherits `Engineer`).
- **Static SoD:** a user cannot hold two conflicting roles.
- **Dynamic SoD:** a user cannot activate conflicting roles in the same session.

### Q4.7. How does RBAC work in Kubernetes and in the cloud?
- **Kubernetes:** `Role`/`ClusterRole` define permissions (verbs on resources); `RoleBinding`/`ClusterRoleBinding` attach them to users, groups, or service accounts. Avoid binding `cluster-admin` broadly.
- **AWS/Azure/GCP:** roles contain policies; identities assume roles. Scope assignments to the narrowest level (resource, resource group, project).

### Q4.8. What is privilege escalation, and how can RBAC reduce the risk?
- **Vertical escalation:** gaining higher privileges (user to admin).
- **Horizontal escalation:** accessing another user's data at the same privilege level (IDOR).

Mitigation: least-privilege roles, server-side authorization checks on every request, regular reviews, patching, restricted admin roles, and monitoring for role changes.

### Q4.9. What are common RBAC mistakes?
- Assigning permissions directly to users, bypassing roles
- Over-broad "admin" or "power user" roles
- No ownership or review of roles
- Failing to remove access on role change
- Enforcing authorization only on the client side
- Ignoring service accounts and non-human identities

---

## 5. Network Security Fundamentals

### Q5.1. Explain the OSI model and give an attack example for each layer.

| Layer | Name | Example Threats |
|-------|------|-----------------|
| 7 | Application | SQL injection, XSS, HTTP floods |
| 6 | Presentation | SSL stripping, weak encryption |
| 5 | Session | Session hijacking |
| 4 | Transport | SYN flood, port scanning |
| 3 | Network | IP spoofing, ICMP attacks, routing attacks |
| 2 | Data Link | ARP spoofing, MAC flooding, VLAN hopping |
| 1 | Physical | Cable tapping, device theft |

### Q5.2. Explain the TCP three-way handshake and the SYN flood attack.
1. Client sends **SYN**.
2. Server responds **SYN-ACK**.
3. Client sends **ACK**; connection established.

In a **SYN flood**, the attacker sends many SYNs without completing the handshake, exhausting the server's half-open connection table. Mitigations: SYN cookies, rate limiting, shorter timeouts, and upstream DDoS protection.

### Q5.3. What is the difference between TCP and UDP from a security view?
TCP is connection-oriented and reliable, so it supports stateful inspection. UDP is connectionless and easier to spoof, making it commonly abused for **amplification DDoS** (DNS, NTP, memcached). UDP firewalling needs state tracking or tight rules.

### Q5.4. List common ports and protocols to know.

| Port | Protocol | Note |
|------|----------|------|
| 22 | SSH | Secure remote administration |
| 23 | Telnet | Insecure; avoid |
| 25 / 587 | SMTP | Mail transfer / submission |
| 53 | DNS | UDP and TCP |
| 80 / 443 | HTTP / HTTPS | Web |
| 110 / 143 | POP3 / IMAP | Plaintext mail (use TLS variants) |
| 389 / 636 | LDAP / LDAPS | Directory |
| 445 | SMB | Frequently targeted (ransomware, lateral movement) |
| 3306 / 5432 / 1433 | MySQL / PostgreSQL / MSSQL | Databases |
| 3389 | RDP | Never expose directly to the internet |

### Q5.5. What is ARP spoofing and how do you prevent it?
An attacker sends forged ARP replies to associate their MAC with another host's IP (often the gateway), enabling man-in-the-middle or denial of service on a local network.

**Prevention:** Dynamic ARP Inspection (DAI), DHCP snooping, port security, static ARP entries for critical hosts, network segmentation, and encrypted protocols (TLS/SSH) to limit the impact.

### Q5.6. What is a man-in-the-middle (MITM) attack and how is it mitigated?
The attacker secretly intercepts and possibly alters communication between two parties. Mitigations: TLS with proper certificate validation, HSTS, certificate pinning where appropriate, mutual TLS, VPNs on untrusted networks, and protection against ARP/DNS spoofing.

### Q5.7. What is a DoS/DDoS attack and how is it mitigated?
**Denial of Service** overwhelms a target's resources; **DDoS** does so from many distributed sources.
- **Volumetric:** saturate bandwidth (UDP floods, amplification).
- **Protocol:** exhaust state tables (SYN floods).
- **Application layer:** exhaust app resources (HTTP floods).

Mitigation: CDN and scrubbing services, anycast, rate limiting, WAF, autoscaling, blackhole/sinkhole routing, and an incident playbook with the ISP.

### Q5.8. What are DNS security threats?
- **DNS spoofing / cache poisoning:** injecting false records.
- **DNS tunneling:** exfiltrating data through DNS queries.
- **DNS hijacking:** altering registrar or resolver settings.
- **Amplification attacks.**

Defenses: **DNSSEC** (integrity and authenticity of records), DNS filtering, monitoring query anomalies, restricting recursion, and DoH/DoT where appropriate.

### Q5.9. What are network segmentation, VLANs, and a DMZ?
- **Segmentation:** dividing a network into zones to limit lateral movement and contain breaches.
- **VLAN:** logical Layer 2 segmentation on shared switches.
- **DMZ:** a buffer zone hosting public-facing services (web, mail), isolated from the internal network by firewalls.
- **Micro-segmentation:** workload-level policies, common in data centers and cloud.

### Q5.10. What is NAT and does it provide security?
Network Address Translation maps private addresses to public ones. It conceals internal addressing and incidentally blocks unsolicited inbound connections, but it is **not** a security control by itself. A real firewall policy is still required.

### Q5.11. What are the wireless security standards?
- **WEP:** broken; never use.
- **WPA/WPA2 (TKIP/AES):** WPA2-PSK is vulnerable to offline dictionary attacks when weak passphrases are used.
- **WPA3:** SAE handshake resists offline cracking, with stronger encryption.
- **WPA2/WPA3-Enterprise (802.1X/RADIUS):** per-user authentication; recommended for organizations.

Also address rogue access points, evil-twin attacks, and guest-network isolation.

### Q5.12. What is the difference between IDS/IPS and a firewall at the network level?
A firewall enforces access rules based on addresses, ports, and state. An **IDS** inspects traffic for malicious patterns and alerts; an **IPS** sits inline and can block. They complement each other (see [Section 8](#8-threat-detection-and-monitoring)).

---

## 6. Firewalls and VPN

### Q6.1. What is a firewall and what are its types?
A firewall controls traffic between networks according to security rules.
- **Packet-filtering (stateless):** inspects headers (IP, port, protocol) per packet.
- **Stateful inspection:** tracks connection state and allows return traffic of established sessions.
- **Proxy/application gateway:** terminates and inspects connections at the application layer.
- **Next-Generation Firewall (NGFW):** adds deep packet inspection, application awareness, user identity, IPS, and threat intelligence.
- **WAF:** protects web applications (HTTP/S).
- **Host-based vs network-based; hardware vs software vs cloud security groups.**

### Q6.2. What is the difference between stateless and stateful firewalls?
A stateless firewall evaluates each packet in isolation against static rules, so return traffic needs explicit rules. A stateful firewall maintains a **connection table**, automatically permitting responses to allowed outbound requests, and can detect out-of-state packets. It is more secure and easier to administer, at some cost in memory and processing.

### Q6.3. What are firewall rule best practices?
- **Default deny** (implicit deny all), then explicitly allow required traffic.
- Rules are processed **top-down; first match wins**, so order matters.
- Apply least privilege on sources, destinations, and ports.
- Use object groups and clear naming/comments for each rule.
- Log denied and critical allowed traffic.
- Review rules regularly and remove unused or shadowed rules.
- Control changes through formal change management.

### Q6.4. What is a WAF and how does it differ from a network firewall?
A **Web Application Firewall** inspects HTTP/S traffic (Layer 7) to block attacks such as SQL injection, XSS, and malicious bots, using signature, behavioral, and positive-security rules. A network firewall works at Layers 3–4 and does not understand application payloads. A WAF complements, not replaces, secure coding.

### Q6.5. What is a VPN and what types exist?
A **Virtual Private Network** creates an encrypted tunnel over an untrusted network.
- **Remote-access VPN:** individual users connect to the corporate network.
- **Site-to-site VPN:** connects entire networks (branch offices, cloud VPCs).
- **Client-based vs clientless (browser-based)** VPNs.

### Q6.6. Compare IPsec and SSL/TLS VPNs.

| Feature | IPsec | SSL/TLS VPN |
|---------|-------|-------------|
| Layer | Network (Layer 3) | Transport/Application |
| Typical use | Site-to-site, full network access | Remote access, per-application access |
| Firewall/NAT friendliness | Needs UDP 500/4500, ESP | Uses TCP/UDP 443 – traverses easily |
| Client | Usually dedicated client | Client or browser |

IPsec uses IKE for key exchange, with **AH** (integrity) and **ESP** (confidentiality and integrity), in **transport** or **tunnel** mode.

### Q6.7. What is split tunneling and what are its risks?
Split tunneling routes only corporate-bound traffic through the VPN while other traffic goes directly to the internet. It saves bandwidth but bypasses corporate inspection and creates a path where a compromised endpoint can bridge the internet and the internal network. Full tunneling is safer for high-security environments.

### Q6.8. What VPN protocols are commonly used?
IPsec/IKEv2, OpenVPN, WireGuard (modern, lightweight, small codebase), and SSTP. **PPTP** and **L2TP without IPsec** are considered weak or obsolete.

### Q6.9. What is the difference between a forward proxy and a reverse proxy?
- **Forward proxy:** sits in front of clients, controlling and filtering outbound traffic (content filtering, anonymity).
- **Reverse proxy:** sits in front of servers, handling inbound requests (load balancing, TLS termination, caching, WAF, hiding backends).

### Q6.10. How do VPNs compare with Zero Trust Network Access (ZTNA)?
A traditional VPN grants broad network-level access once connected. **ZTNA** grants per-application access based on user identity, device posture, and context, with continuous verification, reducing lateral movement. Many organizations are migrating from VPN to ZTNA/SASE.

### Q6.11. What is a bastion host (jump server)?
A hardened, monitored host that is the single controlled entry point for administrative access to a private network. It centralizes authentication, MFA, session logging, and restrictions, instead of exposing every server's SSH/RDP port.

---

## 7. Vulnerability Assessment

### Q7.1. What is the difference between a vulnerability assessment and a penetration test?

| Aspect | Vulnerability Assessment | Penetration Test |
|--------|--------------------------|------------------|
| Goal | Identify and rank weaknesses (breadth) | Exploit weaknesses to prove impact (depth) |
| Approach | Mostly automated scanning plus validation | Manual, goal-driven, simulates an attacker |
| Frequency | Continuous / regular | Periodic (annual or after major changes) |
| Output | Prioritized list of findings | Narrative of attack paths and business impact |

### Q7.2. Describe the vulnerability management lifecycle.
1. **Asset discovery and inventory**
2. **Scanning / assessment**
3. **Analysis and prioritization** (severity, exploitability, asset criticality)
4. **Remediation** (patch, configuration change, compensating control)
5. **Verification** (rescan)
6. **Reporting and continuous improvement**

### Q7.3. What are CVE, CWE, and CVSS?
- **CVE (Common Vulnerabilities and Exposures):** unique identifier for a publicly known vulnerability (e.g., CVE-2021-44228).
- **CWE (Common Weakness Enumeration):** category of weakness types (e.g., CWE-79 XSS).
- **CVSS (Common Vulnerability Scoring System):** severity score from 0.0 to 10.0 (None, Low, Medium, High, Critical), based on exploitability and impact metrics.

### Q7.4. How do you prioritize vulnerabilities beyond the CVSS score?
Combine CVSS with context:
- Is it **actively exploited** (CISA KEV catalog) or likely to be (**EPSS** probability)?
- Is a public exploit available?
- Asset criticality and data sensitivity
- Internet exposure and network location
- Existing compensating controls
- Business impact and regulatory obligations

A medium-scored flaw on an internet-facing crown-jewel system may outrank a critical one on an isolated test box.

### Q7.5. What is the difference between authenticated and unauthenticated scans?
- **Unauthenticated:** the external attacker's view; finds exposed services, but with limited depth.
- **Authenticated (credentialed):** logs in to inspect patches, configuration, and installed software; yields more accurate results with fewer false positives. Use least-privilege scan accounts and protect the credentials.

### Q7.6. What tools are used for vulnerability assessment?
- **Network/host:** Nessus, Qualys, OpenVAS (Greenbone), Rapid7 InsightVM
- **Port/service discovery:** Nmap
- **Web applications:** OWASP ZAP, Burp Suite
- **Containers and IaC:** Trivy, Grype, Checkov
- **Dependencies (SCA):** Snyk, Dependabot, OWASP Dependency-Check

Use these only on systems you are authorized to test.

### Q7.7. How do you handle false positives and false negatives?
- **False positive:** reported but not real. Validate manually, tune scanner policies, and document exceptions.
- **False negative:** a real issue missed. Reduce through credentialed scans, multiple tools, updated plugins, and periodic penetration tests.

### Q7.8. What is the OWASP Top 10?
A regularly updated awareness list of the most critical web application security risks. Categories in the 2021 edition: Broken Access Control, Cryptographic Failures, Injection, Insecure Design, Security Misconfiguration, Vulnerable and Outdated Components, Identification and Authentication Failures, Software and Data Integrity Failures, Security Logging and Monitoring Failures, and Server-Side Request Forgery (SSRF). Check owasp.org for the current edition.

### Q7.9. What are SAST, DAST, IAST, and SCA?
- **SAST:** analyzes source code statically (early in development).
- **DAST:** tests the running application from the outside.
- **IAST:** instruments the running app during tests for runtime-aware findings.
- **SCA:** identifies vulnerable open-source dependencies and licensing issues.

Together they support a **shift-left** DevSecOps strategy.

### Q7.10. What is patch management and what are the best practices?
The process of identifying, testing, deploying, and verifying software updates.
- Maintain an accurate asset and software inventory.
- Define SLAs by severity (e.g., critical within days).
- Test in a staging environment; use phased rollouts.
- Have rollback plans and emergency patch procedures.
- Use virtual patching (WAF/IPS) or isolation when immediate patching is not possible.

### Q7.11. What is a zero-day vulnerability?
A flaw unknown to the vendor (no patch exists) that may be actively exploited. Mitigations: defense in depth, application allowlisting, network segmentation, EDR with behavioral detection, rapid threat-intel consumption, and virtual patching.

### Q7.12. What is the difference between SQL injection and XSS, and how are they prevented?
- **SQL injection:** untrusted input alters a database query. Prevent with **parameterized queries**, least-privilege DB accounts, and input validation.
- **XSS:** untrusted input is executed as script in a victim's browser. Prevent with **context-aware output encoding**, Content Security Policy, and sanitization.

---

## 8. Threat Detection and Monitoring

### Q8.1. What is a SIEM and how does it work?
**Security Information and Event Management** collects, normalizes, correlates, and analyzes logs from many sources (servers, firewalls, endpoints, cloud, applications) to detect threats, support investigations, and meet compliance requirements.

Key functions: log aggregation, correlation rules, alerting, dashboards, retention, and case management. Examples: Splunk, Microsoft Sentinel, IBM QRadar, Elastic Security.

### Q8.2. What is the difference between IDS and IPS?

| Feature | IDS | IPS |
|---------|-----|-----|
| Placement | Out of band (monitors a copy of traffic) | Inline |
| Action | Detects and alerts | Detects and blocks |
| Risk | Missed response time | False positives may block legitimate traffic |
| Examples | Snort, Suricata, Zeek (IDS mode) | Snort/Suricata inline, NGFW IPS |

Detection methods: **signature-based** (known patterns), **anomaly-based** (deviation from baseline), and **heuristic/behavioral**.

### Q8.3. What is EDR/XDR?
- **EDR (Endpoint Detection and Response):** continuously records endpoint activity (processes, files, network) to detect, investigate, and contain threats (isolate host, kill process).
- **XDR:** extends detection and response across endpoints, network, email, identity, and cloud with unified correlation.
- **NDR** focuses on network traffic analytics.

### Q8.4. What log sources are essential for security monitoring?
- Authentication logs (AD, IdP, VPN)
- Firewall, proxy, and DNS logs
- Endpoint/EDR and OS logs (Windows Event IDs 4624/4625 logon, 4688 process creation, 4672 privileged logon)
- Web server and application logs
- Cloud audit logs (CloudTrail, Azure Activity Log, GCP Audit Logs)
- Email security and database audit logs

### Q8.5. What are IOC and IOA?
- **Indicator of Compromise (IOC):** evidence a compromise occurred (malicious hash, IP, domain, registry key).
- **Indicator of Attack (IOA):** behavior suggesting an attack in progress (credential dumping, unusual PowerShell, lateral movement). IOAs are harder for attackers to evade than static IOCs.

### Q8.6. What is MITRE ATT&CK?
A knowledge base of adversary **tactics** (the "why", e.g., Initial Access, Persistence, Lateral Movement, Exfiltration) and **techniques** (the "how"), based on real-world observations. It is used for detection engineering, threat hunting, gap analysis, red/purple teaming, and common reporting language.

### Q8.7. Explain the Cyber Kill Chain.
Lockheed Martin's seven stages: Reconnaissance → Weaponization → Delivery → Exploitation → Installation → Command and Control → Actions on Objectives. Disrupting any stage can stop the attack. ATT&CK provides finer, more modern granularity.

### Q8.8. What is a SOC and what are its tiers?
A **Security Operations Center** is the team and function that monitors, detects, analyzes, and responds to security incidents 24x7.
- **Tier 1:** alert triage and initial classification.
- **Tier 2:** deeper investigation and incident handling.
- **Tier 3:** threat hunting, forensics, detection engineering, and advanced analysis.
- **SOC manager / threat intel / engineering** roles support the operation.

### Q8.9. What is threat hunting?
A proactive, hypothesis-driven search for threats that evaded automated detection. Example: "An attacker may be using living-off-the-land binaries for persistence," followed by querying telemetry for unusual `schtasks`, `wmic`, or `mshta` usage. Outcomes feed new detections and hardening actions.

### Q8.10. What is UEBA?
**User and Entity Behavior Analytics** builds baselines of normal behavior for users, hosts, and services, and flags anomalies such as impossible travel, unusual data downloads, or off-hours privileged access. It helps detect insider threats and compromised accounts.

### Q8.11. How do you reduce alert fatigue and false positives?
- Tune and retire noisy rules; add whitelisting for known-good activity.
- Enrich alerts with asset, identity, and threat-intel context.
- Prioritize with risk scoring.
- Automate repetitive triage using SOAR playbooks.
- Measure rule quality (precision, true-positive rate) and review regularly.

### Q8.12. How would you detect a phishing attack, malware, or ransomware?
- **Phishing:** email gateway analysis, URL sandboxing, user reporting, detection of suspicious sign-ins and mailbox rules.
- **Malware:** EDR behavioral detection, hash/YARA matching, abnormal process trees, outbound beaconing patterns.
- **Ransomware:** mass file modifications/renames, shadow copy deletion (`vssadmin delete shadows`), SMB lateral movement, backup tampering, and sudden high-entropy file writes.

### Q8.13. What are MTTD and MTTR?
- **Mean Time to Detect (MTTD):** average time from compromise to detection.
- **Mean Time to Respond/Recover (MTTR):** average time from detection to containment or recovery.

Lower values indicate a more effective detection and response capability.

---

## 9. Security Auditing

### Q9.1. What is a security audit?
A systematic, independent evaluation of an organization's information systems, policies, and controls against defined criteria (standards, regulations, internal policy) to verify effectiveness and identify gaps.

### Q9.2. What is the difference between internal and external audits?
- **Internal:** conducted by the organization's own audit or compliance team to prepare for external review and drive improvements.
- **External:** conducted by an independent third party for certification or regulatory attestation (e.g., ISO 27001 certification body, SOC 2 CPA firm).

### Q9.3. Which frameworks and standards are commonly referenced?

| Framework | Focus |
|-----------|-------|
| **ISO/IEC 27001** | Information Security Management System (ISMS), certifiable |
| **NIST CSF** | Identify, Protect, Detect, Respond, Recover (CSF 2.0 adds Govern) |
| **NIST SP 800-53** | Detailed security and privacy controls |
| **SOC 2** | Trust Services Criteria for service organizations |
| **PCI DSS** | Cardholder data protection |
| **HIPAA** | Health information (US) |
| **GDPR** | Personal data protection (EU) |
| **SOX** | Financial reporting controls (IT general controls) |
| **CIS Controls / Benchmarks** | Prioritized practices and hardening baselines |

### Q9.4. Describe the typical security audit process.
1. **Planning and scoping:** objectives, systems, criteria.
2. **Information gathering:** policies, diagrams, inventories.
3. **Control testing:** interviews, observation, configuration review, sampling, technical testing.
4. **Evidence collection and documentation.**
5. **Analysis and findings:** classify by risk and root cause.
6. **Reporting:** with recommendations.
7. **Remediation tracking and follow-up audit.**

### Q9.5. What is the difference between compliance and security?
Compliance means meeting the requirements of a standard or regulation at a point in time. Security is the continuous practice of managing real risk. An organization can be compliant yet insecure (checkbox mentality), so compliance should be treated as a baseline, not the finish line.

### Q9.6. What is an audit trail and why is it important?
A chronological record of system activity showing who did what, when, and from where. It supports accountability, non-repudiation, incident investigation, and compliance. Good practice: synchronized time (NTP), centralized collection, tamper protection (write-once or hashed logs), and defined retention periods.

### Q9.7. What is a risk assessment?
A process to identify assets, threats, and vulnerabilities, estimate likelihood and impact, and decide on treatment.
- **Qualitative:** ratings such as High/Medium/Low.
- **Quantitative:** monetary values, e.g., **SLE × ARO = ALE** (Single Loss Expectancy × Annual Rate of Occurrence = Annualized Loss Expectancy).

Treatment options: mitigate, transfer (insurance), accept, avoid.

### Q9.8. What is a gap analysis?
A comparison of the current state of controls against a target standard or framework to identify missing or weak controls, followed by a prioritized remediation roadmap.

### Q9.9. What is the difference between audit findings, observations, and non-conformities?
- **Non-conformity (major/minor):** a failure to meet a specified requirement.
- **Finding:** a documented deficiency with evidence, criteria, cause, and effect.
- **Observation / opportunity for improvement:** not a violation, but a recommended enhancement.

### Q9.10. What are the main types of audit evidence and how do you ensure its reliability?
Documents, system configuration screenshots/exports, logs, interviews, observation, and re-performance. Reliability improves when evidence is obtained directly from the system by the auditor, comes from independent sources, is timestamped, and is sufficient and relevant to the control.

### Q9.11. What are CIS Benchmarks and why do auditors use them?
Consensus-based secure configuration guidelines for operating systems, cloud platforms, databases, and applications. They provide objective, measurable baselines for hardening checks, with tools such as CIS-CAT automating assessment.

### Q9.12. How do you audit user access?
Obtain the user list and role/permission data from the system, compare with HR records and approvals, test for terminated users with active accounts, dormant accounts, excessive privileges, shared accounts, SoD conflicts, and verify that periodic access reviews were performed and documented.

---

## 10. Incident Response

### Q10.1. What is incident response and what are its phases?
The organized approach to preparing for, detecting, containing, and recovering from security incidents. **NIST SP 800-61** lifecycle:
1. **Preparation**
2. **Detection and Analysis**
3. **Containment, Eradication, and Recovery**
4. **Post-Incident Activity** (lessons learned)

The SANS model lists six steps: Preparation, Identification, Containment, Eradication, Recovery, Lessons Learned.

### Q10.2. What should be included in an incident response plan?
- Purpose, scope, and authority
- Roles and responsibilities (CSIRT, legal, HR, PR, executives)
- Incident classification and severity criteria
- Communication and escalation procedures, including contact lists
- Playbooks for common incidents (ransomware, phishing, data breach)
- Evidence handling procedures
- Regulatory notification requirements
- Testing schedule (tabletop exercises and simulations)

### Q10.3. What is the difference between an event, an alert, and an incident?
- **Event:** any observable occurrence in a system.
- **Alert:** a notification that an event may be security-relevant.
- **Incident:** an event (or series) that actually violates or threatens security policy, with adverse impact.

### Q10.4. How do you classify incident severity?
Based on business impact, data sensitivity, scope (number of systems/users), and operational disruption. A common scale is P1 (critical) to P4 (low), each with defined response times and escalation paths. Example: confirmed ransomware on production servers is P1; a single blocked phishing email is P4.

### Q10.5. What are containment strategies?
- **Short-term:** isolate the host from the network, disable compromised accounts, block IPs/domains, revoke sessions and tokens.
- **Long-term:** apply temporary fixes, rebuild segments, and increase monitoring while the root cause is addressed.

The choice balances stopping spread against preserving evidence and business continuity. Avoid powering off a system if volatile memory evidence is required.

### Q10.6. What is digital forensics and the order of volatility?
Collecting and analyzing digital evidence in a forensically sound way. Collect the most volatile evidence first:
1. CPU registers and cache
2. Memory (RAM)
3. Network state and connections
4. Running processes
5. Disk contents
6. Remote logging and monitoring data
7. Backups and archival media

### Q10.7. What is chain of custody?
Documented, unbroken record of who handled evidence, when, and why, from collection to presentation. It preserves admissibility and integrity. Use hashing (e.g., SHA-256) of images, write blockers, tamper-evident storage, and signed handoff logs.

### Q10.8. How would you respond to a ransomware incident?
1. **Isolate** affected systems immediately (network disconnect, disable shares); do not simply power off if memory evidence is needed.
2. Activate the IR plan and notify leadership, legal, and relevant authorities.
3. **Identify** the variant, entry point, scope, and whether data was exfiltrated.
4. **Preserve** evidence (memory, logs, ransom note, sample).
5. **Eradicate:** remove persistence, reset credentials (including KRBTGT where AD is compromised), patch the entry vector.
6. **Recover** from clean, verified, offline/immutable backups; rebuild where necessary.
7. Monitor for reinfection.
8. Conduct lessons learned. Payment decisions involve legal, executive, and law-enforcement consultation and offer no guarantee of recovery.

### Q10.9. How would you handle a reported phishing email?
1. Obtain the original message (with headers) rather than a screenshot.
2. Analyze sender, headers (SPF/DKIM/DMARC results), URLs, and attachments in a sandbox.
3. Search for other recipients and remove the message from all mailboxes.
4. Block malicious domains, URLs, and sender addresses.
5. Check whether anyone clicked or submitted credentials; if so, reset credentials, revoke sessions, and review sign-in and mailbox-rule activity.
6. Provide feedback and awareness training.

### Q10.10. What is a post-incident review (lessons learned)?
A blameless meeting held shortly after resolution to document the timeline, root cause, what worked, what failed, and action items with owners and deadlines. Outputs include improved detection rules, updated playbooks, and training updates.

### Q10.11. What is the difference between BCP, DRP, RTO, and RPO?
- **BCP (Business Continuity Plan):** keeps critical business functions running during disruption.
- **DRP (Disaster Recovery Plan):** restores IT systems and data after a disaster.
- **RTO (Recovery Time Objective):** maximum tolerable downtime.
- **RPO (Recovery Point Objective):** maximum tolerable data loss, measured in time.
- **BIA (Business Impact Analysis)** determines these values.

### Q10.12. What are the notification obligations after a data breach?
They depend on jurisdiction and data type. For example, **GDPR** requires notifying the supervisory authority within **72 hours** of becoming aware of a personal data breach (where risk to individuals exists) and affected individuals when risk is high; sector rules such as HIPAA, PCI DSS, and various national and state laws have their own timelines. Always involve legal and compliance teams early.

### Q10.13. What is SOAR and how does it help incident response?
**Security Orchestration, Automation and Response** integrates tools and automates repetitive tasks through playbooks (enrichment, ticketing, blocking, user notification), reducing response time and analyst workload while preserving human approval for high-impact actions.

---

## 11. Scenario-Based Questions

### S1. You see multiple failed logins followed by a successful login from a foreign IP for a privileged account. What do you do?
Treat as potential account compromise: confirm with the user and check for impossible travel, review the session's activity and any new MFA devices or mailbox rules, disable the account and revoke active sessions/tokens, reset credentials, hunt for lateral movement and persistence, block the source IP, document the timeline, and escalate according to severity.

### S2. A critical vulnerability is announced affecting software used in your environment. How do you respond?
Identify affected assets via inventory and scanning, check exposure (internet-facing, data sensitivity), look for exploitation indicators in logs, apply vendor patch or mitigations (virtual patch, configuration workaround, isolation), prioritize by risk, verify with rescans, and communicate status to stakeholders.

### S3. An employee reports their laptop is behaving strangely. What are your first steps?
Collect details (symptoms, recent downloads or emails), isolate the device through EDR or by disconnecting the network, preserve volatile data if possible, triage with EDR telemetry and a malware scan, check for lateral movement or credential theft, and reimage if compromise is confirmed after evidence is captured.

### S4. How would you secure a new cloud application before launch?
Threat model the design; enforce least-privilege IAM and MFA; segment networks and restrict public exposure; encrypt data in transit and at rest with managed keys; store secrets in a vault; enable logging and monitoring (cloud audit logs to SIEM); run SAST/DAST/SCA and IaC scans; harden configurations against CIS benchmarks; set up backups and test recovery; document an incident response path.

### S5. How do you explain a security risk to a non-technical executive?
Frame it in business terms: what asset is at risk, the plausible impact (financial, legal, reputational, operational), likelihood, existing controls, options with costs and trade-offs, and your recommendation with a timeline. Avoid jargon and use a concrete comparison or example.

### S6. Your organization suspects an insider is exfiltrating data. What is the approach?
Coordinate with HR and legal before acting; preserve logs (DLP, proxy, cloud storage, email, endpoint); monitor discreetly using existing authorized tools; review access and volume anomalies; limit scope of access if justified; follow chain of custody for any evidence; and keep the circle of knowledge small.

---

## 12. Quick Revision Cheat Sheet

| Topic | Remember |
|-------|----------|
| CIA | Confidentiality, Integrity, Availability; plus non-repudiation |
| AAA | Authentication, Authorization, Accounting |
| MFA | Different *categories* of factors, not just two passwords |
| Password storage | Argon2id / bcrypt + salt; never fast hashes |
| SAML / OAuth / OIDC | SSO (XML) / delegated authorization / authentication on OAuth |
| Least privilege | Minimum access, minimum time (JIT) |
| RBAC vs ABAC | Role-based vs attribute/context-based |
| Firewall | Default deny, first match wins, stateful tracking |
| IDS vs IPS | Detect and alert vs inline block |
| VPN | IPsec (network layer) vs TLS (application/port 443); prefer ZTNA for app-level access |
| CVSS | 0–10 severity; combine with EPSS, KEV, and asset criticality |
| SIEM | Collect, normalize, correlate, alert |
| MITRE ATT&CK | Tactics and techniques of real adversaries |
| NIST IR phases | Prepare → Detect/Analyze → Contain/Eradicate/Recover → Post-incident |
| Order of volatility | RAM before disk |
| RTO / RPO | Downtime tolerance / data-loss tolerance |

---

## Recommended Practice

- Build a home lab (virtual machines with Kali, Windows, a SIEM such as Wazuh or Elastic).
- Practice on legal platforms: TryHackMe, Hack The Box, Blue Team Labs, CyberDefenders, OWASP Juice Shop.
- Review certifications' syllabi for structure: CompTIA Security+, CySA+, CEH, CISSP, SC-200, AWS Security Specialty.
- Read incident reports and the MITRE ATT&CK matrix regularly.

> **Disclaimer:** This material is for education and interview preparation. Apply security testing techniques only on systems you own or have explicit written authorization to test.

## Contributing

Contributions are welcome. Fork the repository, create a feature branch, and open a pull request with a clear description of the change.

## License

Released under the MIT License. See `LICENSE` for details.
