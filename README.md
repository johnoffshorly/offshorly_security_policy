# Offshorly Security Policy
**Version:** 0.5.1 | **Status:** Draft | **Review Cycle:** Monthly | **Date:** May 25, 2026

---

## Purpose

This document establishes Offshorly’s **Rolling Release Security Policy**, along with baseline security practices. It defines responsibilities, acceptable behaviors, and standards to protect company data, systems, and personnel from unauthorized access, disclosure, alteration, and destruction.

---

## Scope and Limitations

This policy applies to all Offshorly personnel, contractors, and any systems used to access Offshorly or client data.

- **Permanent remote work.** Offshorly operates as a fully remote company. Employees typically use personal devices for work, which limits the company’s ability to mandate hardware-level controls. This policy therefore sets a **minimum baseline** that is enforceable on personal devices, with optional stronger configurations (e.g., dual-boot, dedicated work devices) encouraged where feasible.
- **Per-project / per-client requirements supersede this policy where stricter.** Where a client contract or project agreement imposes stricter security requirements (e.g., shorter rotation cycles, mandated tooling, prohibited tools), those requirements take precedence. Where client requirements are looser than this policy, this policy applies.
- **Floor, not ceiling.** This policy is the minimum standard. Project leads, PMs, and clients may impose additional controls as needed.

---

## Rolling Release Definition

Offshorly’s security policy follows a **rolling release model**, meaning the policy is continuously reviewed, updated, and improved on a regular cycle (e.g., monthly). Unlike static policies, rolling release policies evolve over time to address:

- New security threats or vulnerabilities
- Changes in organizational structure or tools
- Improvements in best practices or legal requirements
- Additions of new security-related features, procedures, or technologies

All team members must comply with the current version and acknowledge updates as they are issued.

---

## Rolling Release Security Policy

### 1. Use of Official Company Emails
- Only Offshorly-issued email addresses must be used for account registration and access to all third-party services (e.g., GitHub, project tools, cloud platforms).
- Existing accounts must be audited to ensure compliance, especially older projects.
- Use of personal or external email addresses for work-related tools is strictly prohibited.
- This requirement must be included in the onboarding process.

### 2. Credential and Password Storage
- Google Docs, Notion, chat apps, and email must not be used to store or share credentials.
- All credentials must be stored in the designated password manager (e.g., ZohoVault).
- Vault links must be shared instead of passwords directly.
- Access must follow role-based access control and be reviewed periodically.
- All passwords must be generated using ZohoVault’s password generator or the source application’s built-in generator. Human-chosen passwords are not permitted for work systems.
- When adding a credential to ZohoVault:
  - **Change ownership to Ivory Hua** immediately after adding.
  - **Label credentials properly** using the naming convention defined in §2.2.
  - **Place credentials in the proper project folder** for organized access and auditing.

#### 2.1 Password Rotation
- All credentials must be rotated every **3 months (90 days)**, with the following exemptions:
  - **Database (DB) credentials** — rotation not required. DB credentials are typically protected by network controls (firewall rules, private networks, IP allowlists), are not human-typed in day-to-day use, and are issued via the vault, so calendar-based rotation provides minimal additional security and introduces operational risk.
  - **SSH credentials** — rotation not required **when key-based authentication is in use** (see §6). Password authentication over SSH must be disabled by default, so there is normally no SSH password to rotate. SSH keys are rotated on personnel change or suspected compromise, not on a calendar.
    - **Exception:** If key-based authentication is not possible for a particular system (e.g., a legacy host, vendor-managed appliance, or client environment that does not support it), SSH passwords on that system **must** be rotated on the standard 90-day cycle. The reason for the fallback must be documented in the vault entry, and the lead/PM should periodically re-check whether key-based auth has become possible.
- Offshorly-owned CMS accounts must be rotated on the 90-day cycle.
- Client-owned CMS accounts: the client must be reminded to rotate their own credentials. This reminder may be included in the monthly client report on every third cycle (every 3 months).
- For all other systems, rotation cadence and ownership defer to the relevant lead, PM, or client.

#### 2.2 Naming Convention
- Use the format **`Name/Site - Type`**.
  - Examples: `offshorly.com - WPAdmin`, `offshorly.com - DB`, `offshorly.com - SSH`
- Update existing active credentials to follow this convention.
- Apply the convention to all new credentials going forward.

### 3. GitHub Repository Access
- Access to company GitHub/GitLab repositories is only permitted through accounts registered under Offshorly email addresses.
- Repository access must be limited to authorized personnel only.
- Repository access must be audited monthly to remove inactive or former employees.
- The “Dev Offshorly” email (`dev@offshorly.com`) must be added to the repository for auditing.
- **New repositories must be created using the `dev@offshorly.com` account** to ensure consistent ownership and auditability from inception.

### 4. Change Logging and Access Auditing
- All changes to sensitive systems must be logged and subject to audit.
- Monthly audits must be performed to review access to Vault, third-party apps, and repositories.

### 5. Principle of Least Privilege
- Employees are granted the minimum level of access necessary to perform their responsibilities.
- Access rights are reviewed during onboarding, offboarding, and the rolling review cycle.
- Offboarding (revocation of Vault, GitHub, client systems, email, and other access) is owned by **HR in coordination with the employee’s PM or project lead**.

### 6. Server Security
- **Operating system:** As much as possible, use a Red Hat–based distribution (e.g., Fedora, Rocky Linux) for company servers.
- **SSH access:**
  - Password authentication over SSH must be **disabled** (`PasswordAuthentication no` in `sshd_config`).
  - SSH key authentication is the default and required method.
  - SSH keys must be generated **per user**; shared keys are prohibited.
  - SSH keys are rotated on personnel change, role change, or suspected compromise.
  - **Fallback for systems that cannot support key-based auth** (e.g., legacy hosts, vendor appliances, certain client environments): password authentication is permitted only as an exception. In those cases the SSH password must be rotated on the 90-day cycle (per §2.1), the reason for the fallback must be documented in the vault entry, and the lead/PM should re-check periodically whether key-based auth has become possible.
- Server access must follow the Principle of Least Privilege and be reviewed during monthly audits.

### 7. Monthly Policy Review and Updates
- This policy will be updated at least once every 30 days.
- Changes may include revised procedures, added requirements, or new security controls.
- A changelog will be maintained, and employees must acknowledge new versions.

---

## Basic Security Policy

### Authentication and Access Control
- Multi-factor authentication (MFA) is mandatory on all sensitive systems.
- Centralized MFA management is under review.
- Passwords must be at least 12 characters in length, generated by ZohoVault or the source application, and stored securely in the company’s password manager.
- Quarterly access reviews must be conducted to enforce compliance.

### Incident Response
- Any suspicious activity, including phishing attempts, potential breaches, or unauthorized access, must be reported within 24 hours.

#### Reporting Channel
- **Primary channel:** direct message to the Security Officer **and** Management on the company chat app.
- **Off-hours, weekends, and holidays:** same channel — DM the Security Officer and Management directly. Do not wait for business hours; assume someone is reachable.
- Follow up in writing (email or a written summary in the chat) within 24 hours of the initial report, including:
  - What was observed
  - When it was observed
  - Systems, accounts, or data potentially affected
  - Actions already taken (e.g., password changed, session revoked)
- If the Security Officer and Management are unreachable, escalate to the employee’s PM or project lead and continue attempting to reach Security/Management.

#### Recognizing Phishing
Guidelines for recognizing phishing attempts must be followed:
- **Verify sender identity:** Check the sender’s email address carefully for misspellings or unusual domains.
- **Hover before you click:** Hover over links to confirm the actual destination before clicking.
- **Inspect attachments:** Do not open unexpected attachments; confirm legitimacy with the sender first.
- **Check for urgency or scare tactics:** Be cautious of emails demanding immediate action or threatening consequences.
- **Look for inconsistencies:** Poor grammar, misspellings, or unusual formatting can indicate a phishing attempt.
- **Do not provide sensitive data:** Never share credentials, MFA codes, or financial details via email, chat, or unknown links.
- **Report suspicious messages immediately** via the channel above.

Offshorly maintains an Incident Response Plan outlining escalation procedures and responsibilities.

### Data Classification and Handling
- Data must be categorized as Public, Internal, Confidential, or Restricted.
- Confidential and Restricted data must be encrypted and shared only with authorized individuals, using secure channels.

### Device and Endpoint Security

#### Personal Device Baseline (Mandatory)
Because Offshorly is permanent WFH and most employees use personal devices, the following controls are **mandatory** on any device used to access Offshorly or client systems:

- **Full-disk encryption** enabled (FileVault on macOS, BitLocker on Windows, LUKS on Linux).
- **OS and browser auto-updates** enabled. Security patches must not be deferred indefinitely.
- **Screen lock** set to **5 minutes or less**, requiring password or biometric to unlock.
- **Antivirus / EDR** running and kept up to date (built-in protections such as Windows Defender or macOS XProtect are acceptable as a baseline).

Compliance with the personal device baseline is confirmed during onboarding and re-confirmed during the quarterly access review.

#### Optional Stronger Configurations (Encouraged)
Employees are encouraged — but not required — to further isolate their work environment using dual-boot setups, virtual machines, or, ideally, a dedicated work-only device. These options are presented for personal preference and for use when budget or hardware allows.

**Future direction.** Where budget permits, Offshorly intends to provide dedicated company-owned work devices. Until then, the configurations below are voluntary and at the employee’s discretion.

**When to use dual-boot**
- Recommended for employees with laptops that have at least **1 TB of storage**.
  - Partition the disk (e.g., 200–300 GB for the work OS) and install a dedicated operating system for Offshorly work.
- If the laptop has **500 GB or less of storage**:
  - If an additional SSD slot exists, request a company-approved SSD and configure dual boot with the new drive.
  - If no additional slot is available, request a replacement SSD with larger storage (e.g., 1 TB) and partition it for dual boot.

**When to use virtual machines (VMs)**
- Recommended for employees with limited storage or those who prefer not to dual boot.
- Allocate at least **100 GB of disk space** and **8 GB of RAM** to the VM.
- Use company-approved VM software (e.g., VirtualBox, VMware, or Parallels for macOS).

Employees adopting any of the optional configurations must keep work environments strictly separate from personal environments to minimize the risk of data leakage, malware exposure, or accidental credential sharing.

### Use of Software and Network Access
- Only Management-approved software is permitted on company devices.
- Employees must use VPNs when accessing Offshorly resources on unsecured or public networks.
- A custom WireGuard VPN solution is being considered for company-wide use.

### Use of AI Tools
AI assistants (e.g., ChatGPT, Claude, Copilot, Gemini) are permitted for work use, subject to the following rules:

- **Credentials, secrets, and PII:** **Never** paste passwords, API keys, tokens, private keys, environment files, customer PII, or any restricted data into any AI tool. This rule has no exceptions and is not overridable by project agreement.
- **Client code and project data:** Use of AI tools with client code or project-confidential data is **per client contract**. Before using AI tools on a given project, confirm with the PM or project lead whether the client contract permits it. When in doubt, do not paste.
- **Offshorly-internal code and data:** AI tools may be used freely, provided the credential rule above is respected.
- **Output review:** Anything produced by an AI tool must be reviewed for correctness, license compliance, and security before it is committed, shipped, or shared with a client.
- **Tool accounts:** AI tool accounts used for work must be registered under an Offshorly email (per §1).

### Security Training and Awareness
- Security training is mandatory upon onboarding and repeated every six months.
- All personnel must confirm understanding of this policy and stay current with all rolling release updates.

---

## Compliance and Enforcement

Violations of this policy may result in disciplinary action, up to and including termination of employment or contracts. The Security Officer is responsible for managing compliance, performing audits, and overseeing all rolling updates.