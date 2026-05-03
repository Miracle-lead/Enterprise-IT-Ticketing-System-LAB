# 🎫 Enterprise IT Ticketing System Lab
### Zammad · Postfix · Dovecot · Ubuntu 22.04

![Platform](https://img.shields.io/badge/Platform-Ubuntu%2022.04-E95420?style=for-the-badge&logo=ubuntu&logoColor=white)
![Zammad](https://img.shields.io/badge/Zammad-6.x-009EE3?style=for-the-badge&logoColor=white)
![Postfix](https://img.shields.io/badge/Postfix-MTA-FF6600?style=for-the-badge)
![Dovecot](https://img.shields.io/badge/Dovecot-IMAP-2C5F8A?style=for-the-badge)
![ITIL](https://img.shields.io/badge/ITIL-v4%20Aligned-6B21A8?style=for-the-badge)
![DSGVO](https://img.shields.io/badge/DSGVO-Compliant-16A34A?style=for-the-badge)
![BSI](https://img.shields.io/badge/BSI-Grundschutz-DC2626?style=for-the-badge)
![NIST](https://img.shields.io/badge/NIST-CSF%20Aligned-1D4ED8?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)

> **A production-realistic homelab simulating how IT/Sysadmin teams in German companies manage helpdesk operations using open-source tools.**
> Built, configured, and operated end-to-end as a hands-on demonstration of real-world IT/Sysadmin skills aligned with German enterprise standards.

---

## 📑 Table of Contents

- [Overview](#-overview)
- [Architecture](#-architecture)
- [Skills Demonstrated](#-skills-demonstrated)
- [Repository Structure](#-repository-structure)
- [Lab Phases](#-lab-phases)
  - [Phase 1 — Setup & Email Integration](#phase-1--setup--email-integration)
  - [Phase 2 — Agents, Groups & Permissions](#phase-2--agents-groups--permissions)
  - [Phase 3 — Automation, SLAs & Triggers](#phase-3--automation-slas--triggers)
  - [Phase 4 — REST API Integration & Monitoring](#phase-4--rest-api-integration--monitoring)
  - [Phase 5 — Security Hardening & Backup](#phase-5--security-hardening--backup)
- [Live IT Administration](#-live-it-administration)
- [IT Governance, Risk & Compliance](#-it-governance-risk--compliance)
- [Threat Modelling — STRIDE + LINDDUN](#-threat-modelling--stride--linddun)
- [Cyber Resilience — NIST CSF](#-cyber-resilience--nist-csf)
- [How to Reproduce This Lab](#-how-to-reproduce-this-lab)
- [Scripts Reference](#-scripts-reference)
- [Author](#-author)

---

## 🧭 Overview

This lab replicates the day-to-day IT operations of a mid-sized German company — **TechCorp Berlin** — using entirely open-source tools. It covers the full stack:

- 📬 Mail server configuration (Postfix MTA + Dovecot IMAP)
- 🎫 Ticket automation, SLA enforcement, and escalation workflows
- 🔌 REST API integration and programmatic ticket management
- 🔒 Security hardening with TLS, Fail2ban, and RBAC
- 📋 Full DSGVO, BSI Grundschutz, and ITIL v4 compliance

The system was not just configured — it was **operated as a Senior IT Administrator**, running live daily operations including ticket triage, incident response, escalation, and resolution.

---

## 🏗️ Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      TechCorp Berlin                        │
│                                                             │
│  ┌──────────┐    SMTP    ┌──────────┐   IMAP   ┌─────────┐ │
│  │ Postfix  │──────────▶│ Dovecot  │─────────▶│  Zammad │ │
│  │  (MTA)   │           │  (IMAP)  │          │Ticketing│ │
│  └──────────┘           └──────────┘          └────┬────┘ │
│       │                      │                      │      │
│    TLS/SMTP              TLS/IMAP            REST API      │
│    Port 25/587           Port 993            Port 3000     │
│                                                    │       │
│                   ┌────────────────────┐           │       │
│                   │  L1 / L2 / L3      │◀──────────┘       │
│                   │  Support Tiers     │                   │
│                   └────────────────────┘                   │
│                                                             │
│  ┌─────────────────────────────────────────────────────┐   │
│  │                  PostgreSQL Database                 │   │
│  └─────────────────────────────────────────────────────┘   │
└─────────────────────────────────────────────────────────────┘
```

**Trust Boundaries:**
- External internet → Postfix (SMTP boundary)
- Postfix → Dovecot (internal mail boundary)
- Dovecot → Zammad (IMAP polling boundary)
- Zammad → PostgreSQL (application-database boundary)
- Admin browser → Zammad REST API (authentication boundary)

---

## 🛠️ Skills Demonstrated

| Domain | Skills |
|--------|--------|
| **Mail Server Administration** | Postfix MTA, Dovecot IMAP, virtual mailboxes, mail routing, TLS encryption |
| **ITSM / ITIL v4** | L1/L2/L3 support tiers, SLA configuration, escalation management, incident lifecycle |
| **Ticketing System Administration** | Zammad setup, group/agent management, trigger automation, scheduler jobs |
| **REST API Integration** | Token-based auth, programmatic ticket creation, SLA breach monitoring via API |
| **Security Hardening** | TLS for SMTP/IMAP, Fail2ban brute-force protection, DSGVO-compliant data retention |
| **Automation & Scripting** | Bash scripts for monitoring, backup, and retention; cron scheduling |
| **IT Governance** | ITIL v4 service management, change management, continual improvement |
| **Risk Assessment** | Risk register, likelihood/impact analysis, mitigation controls |
| **Compliance** | DSGVO/GDPR, BSI Grundschutz baseline controls, audit trail management |
| **Threat Modelling** | STRIDE per component, LINDDUN for personal data flows — 37 threats identified |
| **Cyber Resilience** | NIST CSF alignment across all five functions; RTO/RPO defined |

---

## 📁 Repository Structure

```
enterprise-it-ticketing-lab/
├── README.md
├── scripts/
│   ├── morning-check.sh        # Daily system health check
│   ├── zammad-monitor.sh       # SLA breach monitoring via API
│   ├── zammad-backup.sh        # DSGVO-compliant backup script
│   └── zammad-retention.sh     # 90-day data retention policy
├── config/
│   ├── postfix/                # Postfix virtual mailbox config
│   ├── dovecot/                # Dovecot IMAP + TLS config
│   └── fail2ban/               # Fail2ban jail configuration
└── screenshots/                # Lab evidence screenshots
```

---

## 🔬 Lab Phases

### Phase 1 — Setup & Email Integration

Configured a complete mail stack from scratch on Ubuntu 22.04. Postfix was set up as the MTA with virtual mailbox support for `techcorp-berlin.de`. Dovecot was configured for IMAP authentication using a `passwd-file` backend, delivering mail to `/var/mail/vhosts/`. Zammad was then connected to the helpdesk IMAP mailbox, verified end-to-end email flow, and confirmed automatic ticket creation from inbound email.

**Key configuration fixes applied:**

| Item | Resolution |
|------|-----------|
| Postfix `mydestination` conflict | Removed domain from `mydestination`, moved to virtual |
| Postfix `virtual_mailbox_limit` | Set to `0` to allow unrestricted mailbox size |
| Dovecot system auth | Disabled in favour of `passwd-file` auth |
| Dovecot `userdb` | Configured as static `vmail` delivery |
| Zammad IMAP auth | Fixed username format to full email address |
| Stuck mail queue | Cleared using `postsuper -d ALL deferred` |

**Postfix TLS configuration:**
```ini
smtpd_tls_cert_file = /etc/ssl/certs/ssl-cert-snakeoil.pem
smtpd_tls_key_file  = /etc/ssl/private/ssl-cert-snakeoil.key
smtpd_use_tls = yes
smtpd_tls_security_level = may
smtpd_tls_protocols = !SSLv2, !SSLv3, !TLSv1, !TLSv1.1
```

**TLS verified on Dovecot port 993:**
```bash
openssl s_client -connect localhost:993 -quiet
# Result: CN=ubuntu.fritz.box — TLS handshake successful
```

---

### Phase 2 — Agents, Groups & Permissions

Built the complete L1/L2/L3 support structure inside Zammad, reflecting how German IT departments are typically organised.

**Support Structure:**
```
TechCorp Berlin IT Support
├── L1-Helpdesk   → Agent: Anna Müller   (First contact, general issues)
├── L2-IT-Support → Agent: Ben Schmidt   (Escalated technical issues)
└── L3-Sysadmin   → Agent: Clara Weber   (Infrastructure, critical issues)
```

Agents were created programmatically via the Zammad Rails runner and assigned full group access. Auto-routing triggers were configured to direct incoming tickets to the correct tier based on subject keywords.

**Auto-Routing Logic:**

| Keyword in Subject | Routes To |
|-------------------|-----------|
| `VPN`, `network` | L2-IT-Support |
| `critical`, `database`, `backup` | L3-Sysadmin |
| Everything else | L1-Helpdesk |

**Custom Roles beyond standard Agent/Admin:**

| Role | Purpose |
|------|---------|
| `IT-Supervisor` | Oversees IT-Helpdesk, access to reporting |
| `Team-Lead` | Can assign/reassign tickets, view overviews |
| `Read-Only-Auditor` | Management view — read only across all groups |
| `Security-Analyst` | Restricted to Security-Team tickets only |

---

### Phase 3 — Automation, SLAs & Triggers

Configured ITIL v4-aligned SLAs with escalation timers per support tier, enforced automatically by Zammad on every incoming ticket.

**SLA Configuration:**

| Tier | First Response | Solution Time |
|------|---------------|---------------|
| L1-Helpdesk | 1 hour | 8 hours |
| L2-IT-Support | 2 hours | 24 hours |
| L3-Sysadmin | 4 hours | 72 hours |

**Automation Triggers:**

| Trigger | Function |
|---------|---------|
| `Auto-Reply-On-Ticket-Open` | Sends confirmation email to customer on ticket creation |
| `Escalate-L1-to-L2-Notification` | Notifies L2 and reassigns when L1 SLA is breached |
| `Route-to-L1/L2/L3` | Auto-routes tickets by keyword matching |
| `Tag-VPN-Tickets` | Tags VPN tickets for reporting |
| `Tag-Database-Tickets` | Tags database tickets for reporting |

An **Auto-close Scheduler** was also configured to automatically close tickets with no customer response after 48 hours, running daily at 08:00.

---

### Phase 4 — REST API Integration & Monitoring

Authenticated to the Zammad REST API using a token generated directly via the Rails console. Used the API to query open tickets, create tickets programmatically, and build a real-time SLA breach monitoring script.

**API Token creation via Rails console:**
```bash
sudo zammad run rails r - <<'EOF'
admin = User.find(3)
UserInfo.current_user_id = admin.id
token = Token.create!(
  action: 'api',
  user_id: admin.id,
  name: 'api-monitoring',
  preferences: { 'permission' => ['ticket.agent', 'admin.ticket'] }
)
puts token.token
EOF
```

**Programmatic ticket creation:**
```bash
curl -s -X POST \
  -H "Authorization: Token token=$TOKEN" \
  -H "Content-Type: application/json" \
  -d @/tmp/ticket.json \
  "http://localhost:3000/api/v1/tickets"
```

**SLA Breach Monitor output (`zammad-monitor.sh`):**
```
======================================
  Zammad SLA Monitor - Sat May 02 2026
======================================
=== OPEN TICKETS WITH SLA BREACH ===
  OK (57h 27m left) | #13008 | critical database error on production
  OK (55h 27m left) | #13014 | VPN not connecting from home office
  OK (54h 27m left) | #13016 | I cannot login to my account
Total breached: 0
======================================
```

The monitoring script runs automatically every hour via cron:
```bash
0 * * * * /home/vm1/zammad-monitor.sh >> /var/log/zammad-sla-monitor.log 2>&1
```

---

### Phase 5 — Security Hardening & Backup

**Fail2ban — Brute Force Protection**

Four active jails protecting all critical services:

| Jail | Protection |
|------|-----------|
| `dovecot` | IMAP brute force |
| `postfix` | SMTP flood |
| `postfix-sasl` | SMTP auth brute force |
| `sshd` | SSH brute force |

Policy: **5 failed attempts within 10 minutes → 1-hour IP ban.**

**DSGVO-Compliant Backup**

Daily automated backup runs at `02:00` via cron, covering:
- PostgreSQL database
- Zammad file storage
- Mail server configuration

Backups are retained for **30 days** then automatically purged in compliance with DSGVO data minimisation principles.

**Data Retention Policy**

A weekly retention script closes tickets older than **90 days** every Sunday at `03:00`, ensuring personal data is not held longer than operationally necessary under DSGVO Article 5(1)(e).

---

## 👨‍💻 Live IT Administration

After completing the full setup, the system was operated as a **Senior IT Administrator** running live daily operations for TechCorp Berlin.

**Daily Morning Health Check:**
```
=========================================
  TechCorp Berlin - Morning System Check
  Sat May 02 06:57:34 AM EDT 2026
=========================================
--- Zammad Status ---   Active: active (running)
--- Postfix Status ---   Active: active (exited)
--- Dovecot Status ---   Active: active (running)
--- Fail2ban Status ---  Jail list: dovecot, postfix, postfix-sasl, sshd
--- Disk Usage ---       /dev/sda3  59G  17G  39G  30%
--- Open Tickets ---
    L1-Helpdesk:   3 open tickets
    L2-IT-Support: 2 open tickets
    L3-Sysadmin:   2 open tickets
=========================================
```

**Monday Morning Ticket Triage — 7 tickets triaged by business impact:**

| Ticket | Tier | Assigned To | Priority | Rationale |
|--------|------|-------------|----------|-----------|
| CRITICAL - Database backup failed | L3 | Clara Weber | 🔴 High | Data loss risk — immediate action |
| VPN dropping every 30 minutes | L2→L3 | Clara Weber | 🔴 High | 12 users affected — escalated |
| Exchange server latency | L2 | Ben Schmidt | 🔴 High | Business-wide mail delay |
| Cannot access Outlook | L1 | Anna Müller | 🟡 Normal | Standard password reset |
| Printer on 2nd floor offline | L1 | Anna Müller | 🟡 Normal | Localised hardware issue |
| New laptop setup - Maria Schneider | L1 | Anna Müller | 🟢 Low | Planned, non-urgent |
| Storage server at 89% capacity | L3 | Clara Weber | 🟡 Normal | 8 days before critical |

**Critical Incident — Database Backup Failure:**
```
Investigating failed backup job. Checking cron logs and storage
availability. Will report back within 30 minutes. Priority: CRITICAL.
— Miracle Foka, Senior IT Admin
```

**Escalation — VPN Issue L2 → L3 (ITIL Functional Escalation):**
```
VPN config rollback attempted — issue persisted
Packet loss detected on core router interface eth0
Suspected hardware fault on switch SW-CORE-01
SLA breach risk flagged — 4 hours remaining on first response SLA
```

**Ticket Resolution — Full Lifecycle (Outlook password, resolved in 15 minutes):**
```
RESOLVED - Password Reset Complete

1. Verified user identity via employee ID (EMP-2024-0892)
2. Reset password in Active Directory
3. Enforced password change on next login
4. Confirmed user can now access Outlook successfully
5. Reminded user of TechCorp password policy:
   - Minimum 12 characters
   - Uppercase, lowercase, number, and special character required
   - Password expires every 90 days

Resolution time: 15 minutes
— Miracle Foka, Senior IT Admin
```

---

## 📋 IT Governance, Risk & Compliance

### ITIL v4 Governance Framework

| Principle | Implementation |
|-----------|---------------|
| **Focus on value** | Each ticket tier mapped to business impact — VPN outages affecting 12 users prioritised over single-user issues |
| **Start where you are** | Existing Ubuntu infrastructure assessed before adding new services |
| **Progress iteratively** | Lab built in 5 structured phases, each validated before proceeding |
| **Collaborate and promote visibility** | All ticket actions, escalations, and resolutions documented — full audit trail maintained |
| **Think and work holistically** | Mail stack, ticketing, security, and automation treated as one integrated system |
| **Keep it simple** | Triggers use clear keyword-based routing; scripts are readable and commented |
| **Optimise and automate** | SLA monitoring, backup, retention, and ticket routing fully automated |

### Risk Register

| Risk ID | Description | Likelihood | Impact | Level | Mitigation |
|---------|-------------|-----------|--------|-------|-----------|
| R-01 | IMAP brute force attack | High | High | 🔴 Critical | Fail2ban — 5 attempts = 1hr ban |
| R-02 | Email intercepted in transit | Medium | High | 🟠 High | TLS on Postfix + Dovecot |
| R-03 | Production database loss | Low | Critical | 🔴 Critical | Daily automated backup |
| R-04 | SLA breach | Medium | Medium | 🟡 Medium | Hourly SLA monitoring + escalation triggers |
| R-05 | Disk exhaustion | Medium | High | 🟠 High | Daily disk check + storage ticket at 89% |
| R-06 | Unauthorised ticket access | Low | Medium | 🟡 Medium | Role-based access control |
| R-07 | Personal data retained beyond legal limit | Low | High | 🟠 High | 90-day auto-close, 30-day backup purge |
| R-08 | Single point of failure — all services on one host | High | Critical | 🔴 Critical | Accepted lab limitation; production would use separated hosts |
| R-09 | Weak credentials on mail accounts | Medium | High | 🟠 High | Strong password policy in Dovecot passwd file |
| R-10 | Undetected service outage | Medium | High | 🟠 High | Morning health check monitors all services daily |

### DSGVO / GDPR Compliance

| Article | Requirement | Implementation |
|---------|------------|---------------|
| Art. 5(1)(e) — Storage limitation | Data not kept longer than necessary | Tickets auto-closed after 90 days; backups purged after 30 days |
| Art. 5(1)(f) — Integrity & confidentiality | Data protected against unauthorised access | TLS on all mail transport; Fail2ban blocking brute force |
| Art. 32 — Security of processing | Appropriate technical measures | TLS, Fail2ban, RBAC, encrypted backup |
| Art. 25 — Data protection by design | Privacy built in from the start | Minimal data collected; auto-close removes stale personal data |
| Art. 5(2) — Accountability | All actions logged and attributable | Full Zammad Activity Stream; all actions attributed to user ID |

### BSI Grundschutz Compliance

| BSI Module | Control | Implementation |
|-----------|---------|---------------|
| APP.5.3 — Postfix | Secure SMTP configuration | TLS enforced, weak protocols disabled |
| APP.5.3 — Dovecot | Secure IMAP configuration | TLS on port 993, passwd-file authentication |
| OPS.1.1.3 — Patch management | Systems kept up to date | Ubuntu 22.04 LTS with automatic security updates |
| OPS.1.1.5 — Data backup | Regular automated backups | Daily backup: DB, files, and mail config |
| DER.2.1 — Incident handling | Structured incident response | L1/L2/L3 escalation path with documented procedures |
| ORP.4 — Identity management | Role-based access control | Agent, Admin, IT-Supervisor, Security-Analyst, Team-Lead |

### ITIL v4 Practice Alignment

| ITIL Practice | Implementation |
|--------------|---------------|
| Incident Management | Three-tier routing with SLA enforcement and formal escalation |
| Problem Management | Root cause documented in ticket notes |
| Change Management | All configuration changes tested and documented before deployment |
| Service Level Management | Three SLA tiers with automated monitoring and breach alerting |
| Service Desk | Zammad as single point of contact for all IT requests |
| Continual Improvement | Monitoring scripts feed into daily review; retention policy reviewed quarterly |

---

## 🔐 Threat Modelling — STRIDE + LINDDUN

### System Data Flow Diagram

```
┌─────────────┐  SMTP/TLS  ┌─────────────┐  IMAP/TLS  ┌─────────────┐
│  External   │ ──────────▶│   Postfix   │ ──────────▶│   Dovecot   │
│  Senders    │            │    (MTA)    │            │   (IMAP)    │
└─────────────┘            └─────────────┘            └──────┬──────┘
                                                             │
                                                      Poll mailbox
                                                             │
┌─────────────┐  REST API  ┌─────────────┐         ┌────────▼────────┐
│  IT Admins  │ ◀─────────▶│   Zammad    │◀────────│  helpdesk@      │
│  & Agents   │            │  (Tickets)  │         │  mailbox        │
└─────────────┘            └──────┬──────┘         └─────────────────┘
                                  │
                            PostgreSQL DB
                                  │
                           ┌──────▼──────┐
                           │  Ticket &   │
                           │  User Data  │
                           └─────────────┘
```

### STRIDE Analysis Summary

**Postfix:**

| Threat | Category | Status |
|--------|----------|--------|
| Email sender spoofing | Spoofing | ⚠️ Partial — SPF/DKIM recommended for production |
| Mail queue tampering | Tampering | ✅ Mitigated — queue files owned by root |
| No mail delivery logging | Repudiation | ✅ Mitigated — `/var/log/mail.log` records all transactions |
| Plaintext SMTP interception | Info Disclosure | ✅ Mitigated — TLS enforced; weak protocols disabled |
| SMTP flood / open relay abuse | DoS | ✅ Mitigated — Postfix relay restrictions + Fail2ban |
| Postfix running as root | Elevation | ✅ Mitigated — drops privileges to `postfix` user |

**Dovecot:**

| Threat | Category | Status |
|--------|----------|--------|
| Credential brute force | Spoofing | ✅ Mitigated — Fail2ban: 5 attempts = 1hr ban |
| Mailbox file tampering | Tampering | ✅ Mitigated — files owned by `vmail` user |
| IMAP credentials in cleartext | Info Disclosure | ✅ Mitigated — TLS enforced on port 993 |
| IMAP service crash | DoS | ✅ Mitigated — systemd auto-restart + morning-check |
| Misconfiguration allowing any login | Elevation | ✅ Mitigated — system auth disabled; passwd-file only |

**Zammad:**

| Threat | Category | Status |
|--------|----------|--------|
| API token theft | Spoofing | ✅ Mitigated — tokens scoped to minimum permissions |
| Ticket manipulation via API | Tampering | ✅ Mitigated — token auth required for all writes |
| Admin actions not attributed | Repudiation | ✅ Mitigated — full audit trail in Activity Stream |
| Ticket flood causing outage | DoS | ⚠️ Partial — rate limited by Postfix |
| Agent accessing other groups' tickets | Elevation | ✅ Mitigated — group-based RBAC |

**PostgreSQL:**

| Threat | Category | Status |
|--------|----------|--------|
| Connection spoofing | Spoofing | ✅ Mitigated — DB listens only on localhost |
| SQL injection via ticket fields | Tampering | ✅ Mitigated — ActiveRecord ORM with parameterised queries |
| Database dump accessible | Info Disclosure | ✅ Mitigated — backup files owned by root |
| DB crash due to disk full | DoS | ✅ Mitigated — daily disk check in morning-check.sh |
| DB user with excessive privileges | Elevation | ✅ Mitigated — Zammad DB user scoped to `zammad` DB only |

### LINDDUN Privacy Analysis

Personal data in scope: ticket records, mail messages, user accounts, backup files, log files (names, emails, employee IDs, IP addresses).

| ID | Category | Description | Impact | Status |
|----|----------|-------------|--------|--------|
| L-01 | Linkability | Tickets across incidents can build a behavioural profile | Medium | ✅ Data minimisation applied |
| I-01 | Identifiability | Employee IDs in ticket notes directly identify individuals | High | ✅ Used for verification only |
| N-01 | Nonrepudiation | All admin actions logged — admins cannot deny actions | Low | ✅ Accepted — satisfies DSGVO Art. 5(2) |
| DD-01 | Disclosure | Backup files contain full personal data | Critical | ⚠️ Encrypt at rest — recommended for production |
| DD-02 | Disclosure | API returns full user objects including email | High | ✅ Tokens scoped; HTTPS recommended |
| U-01 | Unawareness | Customers may not know retention period or access rights | High | ⚠️ Privacy notice required for production |
| NC-01 | Noncompliance | Retaining tickets indefinitely violates DSGVO Art. 5(1)(e) | Critical | ✅ 90-day auto-close + 30-day backup purge |
| NC-02 | Noncompliance | No documented lawful basis for processing | High | ⚠️ Art. 6(1)(f) to be documented in RoPA |

### Threat Model Summary

| Framework | Threats Identified | Fully Mitigated | Partially Mitigated | Accepted/Deferred |
|-----------|-------------------|-----------------|--------------------|--------------------|
| STRIDE | 24 | 20 (83%) | 3 (13%) | 1 (4%) |
| LINDDUN | 13 | 5 (38%) | 4 (31%) | 4 (31%) |
| **Total** | **37** | **25 (68%)** | **7 (19%)** | **5 (13%)** |

---

## 🛡️ Cyber Resilience — NIST CSF

### Anticipate — Proactive Threat Awareness
- Daily morning health check — full service status sweep before tickets are touched
- Hourly SLA monitoring — `zammad-monitor.sh` queries the REST API for breaching tickets
- Fail2ban real-time detection — brute force attempts blocked automatically
- Threat modelling (STRIDE + LINDDUN) — 37 threats identified before go-live

### Withstand — Absorbing Disruption
- Service isolation — each component runs as an independent systemd service
- Tiered support structure — if L2 is unavailable, L1 continues first-contact handling
- SLA escalation triggers — tickets self-escalate and notify the responsible agent automatically
- Fail2ban auto-banning — active brute force attacks absorbed and neutralised in real time

### Recover — Rapid Return to Normal

| Objective | Target | Basis |
|-----------|--------|-------|
| RTO (Recovery Time Objective) | < 30 minutes | Based on backup size (~6MB) and restoration testing |
| RPO (Recovery Point Objective) | < 24 hours | Daily backup at 02:00; maximum one day data loss |

**Database restore:**
```bash
gunzip -c /var/backups/zammad/zammad-db-YYYY-MM-DD.sql.gz | psql zammad
```

**Mail config restore:**
```bash
tar -xzf /var/backups/zammad/mail-config-YYYY-MM-DD.tar.gz -C /
```

### Adapt — Learning After Incidents
- Post-incident review — every ticket is a permanent record; full history preserved
- DSGVO retention as operational learning — 90-day window allows pattern identification
- Trigger and SLA tuning — configurations updated based on observed routing behaviour

### NIST CSF Maturity Assessment

| CSF Function | Capability | Maturity |
|-------------|-----------|---------|
| **Identify** | Asset inventory, STRIDE + LINDDUN threat modelling, 10-risk register | 🟢 Managed |
| **Protect** | TLS on SMTP/IMAP, Fail2ban, RBAC, DSGVO data minimisation | 🟢 Managed |
| **Detect** | Morning health check, hourly SLA monitor, Fail2ban + application logs | 🔵 Defined |
| **Respond** | L1/L2/L3 escalation paths, automated SLA triggers, full incident documentation | 🟢 Managed |
| **Recover** | Daily automated backup, documented restore procedure, RTO < 30min | 🔵 Defined |

> **Overall Posture: Managed.** Primary gap to Optimised is the absence of automated service-failure alerting and offsite backup replication — both documented in the improvement backlog below.

**Improvement Backlog:**

| Item | Priority |
|------|---------|
| Encrypt backup files at rest | 🔴 Critical |
| Enable HTTPS on Zammad | 🔴 Critical |
| Publish DSGVO privacy notice | 🟠 High |
| Document RoPA (Records of Processing Activities) | 🟠 High |
| Implement SPF/DKIM for email authentication | 🟠 High |
| Separate services onto dedicated hosts | 🟡 Medium |
| Implement offsite backup replication | 🟡 Medium |
| Add MFA for Zammad admin accounts | 🟡 Medium |

---

## 🚀 How to Reproduce This Lab

### Prerequisites
- Ubuntu 22.04 LTS (VM or bare metal)
- Minimum 4GB RAM, 40GB disk
- Static internal IP

### Quick Start

```bash
# 1. Install Zammad
wget -O /tmp/zammad_install.sh https://dl.zammad.org/install.sh
bash /tmp/zammad_install.sh

# 2. Install Postfix + Dovecot
sudo apt install postfix dovecot-imapd dovecot-pop3d -y

# 3. Configure virtual mailboxes
sudo postconf -e "virtual_mailbox_domains = techcorp-berlin.de"
sudo postconf -e "virtual_mailbox_base = /var/mail/vhosts"
sudo postconf -e "mailbox_size_limit = 0"
sudo postconf -e "compatibility_level = 3.6"

# 4. Enable TLS on Dovecot (port 993)
# Edit /etc/dovecot/conf.d/10-ssl.conf
# ssl = yes
# ssl_cert = </etc/ssl/certs/ssl-cert-snakeoil.pem
# ssl_key = </etc/ssl/private/ssl-cert-snakeoil.key

# 5. Install Fail2ban
sudo apt install fail2ban -y

# 6. Follow Phase 1–5 documentation above
```

---

## 📜 Scripts Reference

| Script | Location | Schedule | Purpose |
|--------|----------|----------|---------|
| `morning-check.sh` | `/usr/local/bin/` | Daily (manual) | System health check |
| `zammad-monitor.sh` | `/home/vm1/` | Every hour (cron) | SLA breach monitoring |
| `zammad-backup.sh` | `/usr/local/bin/` | Daily 02:00 (cron) | Full system backup |
| `zammad-retention.sh` | `/usr/local/bin/` | Weekly Sunday 03:00 (cron) | DSGVO data retention |

---

## 🇩🇪 German Enterprise Context

This lab is specifically designed to reflect real workflows in German organisations:

- **DSGVO (GDPR)** — 30-day backup retention, 90-day ticket retention, data minimisation by design
- **ITIL v4** — Three-tier support structure, formal SLA management, documented escalation process
- **BSI Grundschutz** — TLS enforcement, brute force protection, role-based access control
- **Realistic org structure** — L1/L2/L3 tiers with German naming conventions
- **Domain** — `techcorp-berlin.de` reflecting an authentic German company setup

---

## 👤 Author

**Miracle Foka**
- GitHub: [@Miracle-lead](https://github.com/Miracle-lead)

> Built as a portfolio demonstration of real-world IT/Sysadmin skills for German enterprise environments.

---

## 📄 License

MIT — Free to use for learning, portfolio, or adapting for real deployments.
