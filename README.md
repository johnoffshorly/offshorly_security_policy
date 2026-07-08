# Offshorly Security Policy
**Version:** 0.7 | **Status:** Draft | **Review Cycle:** Monthly | **Date:** July 8, 2026

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
  - **Set ownership based on credential type:**
    - **Company-critical or shared accounts** (Tier A2: server, hosting, DNS, financial, domain registrar, accounts not tied to a specific employee's identity) — set owner to **Ivory Hua**. She receives and actions rotation alerts for these.
    - **Personal-use accounts** (accounts tied to the employee's own email or identity — e.g., personal WP admin, Kinsta, client portals logged in as you) — the **employee is the owner**. Share the credential with the management group so it remains accessible if needed. The employee receives and actions their own rotation alerts.
  - **Label credentials properly** using the naming convention defined in §2.2. The name is the primary way to identify which employee owns which credential and to route ownership transfers during rotation or offboarding.
  - **Place credentials in the proper project folder** for organized access and auditing.
- **Migration of existing credentials:** All credentials currently owned by Ivory Hua are being reviewed. Personal-use credentials will be transferred back to the respective employee when the next rotation alert fires. The naming convention (§2.2) is the reference for identifying the correct owner.

#### 2.1 Password Rotation

Rather than rotating every credential on a calendar, Offshorly takes a **risk-tiered approach**. Credentials with high blast radius and platform-enforceable expiry rotate on a mandatory schedule; high-blast-radius accounts without platform-level enforcement rotate on a recommended schedule; everything else rotates on event.

**Tier A — Mandatory 90-day rotation, platform-enforced**

These accounts have expiry enforced at the platform level — users cannot extend or bypass the policy:

- **Google Workspace account password** — enforced via the Workspace admin console password-expiry policy.
- **Zoho Vault master password** — enforced via Vault's Master Password Policy.

**Recommended rotation (Tier A2) — high-blast-radius, 90-day cycle encouraged**

These accounts carry high blast radius and must be stored in Zoho Vault under the **Critical Accounts** policy. The policy triggers a 90-day rotation alert to the credential owner (Ivory Hua for company-critical accounts). Rotation is strongly encouraged on that cycle and verified during the monthly audit (§4). Platform-level expiry enforcement is not available for these account types — compliance depends on the owner actioning the alert and audit follow-through:

- Primary identity accounts (Microsoft 365)
- Domain registrar and DNS management accounts
- Hosting and cloud root/owner accounts (AWS root, Cloudflare, Vercel/Netlify owner, etc.)
- Financial accounts (Stripe, banking, payment processors, billing portals)
- Offshorly-owned CMS admin accounts
- Client CMS admin accounts where Offshorly holds the credential
- Other high-privilege accounts as identified by the Security Officer

**Tier B — Rotation on event only**

These rotate on compromise, suspected leak, role change, or staff change, but **not** on a calendar. They are either protected by network controls, key-based auth, MFA + generator-produced passwords, or are operationally risky to rotate:

- Database (DB) credentials — protected by network controls, not human-typed, vault-issued
- SSH credentials when key-based auth is in use (see §6)
- API keys, deploy tokens, and service account credentials — rotating these typically breaks production
- Vendor portals and infrequently-used third-party accounts
- Read-only and low-privilege accounts
- All other credentials not listed in Tier A or Tier A2

**Exception — SSH password fallback:** If key-based SSH authentication is not possible for a particular system (legacy host, vendor-managed appliance, client environment that does not support it), the SSH password on that system **must** be rotated on the 90-day cycle. The reason for the fallback must be documented in the vault entry, and the lead/PM should periodically re-check whether key-based auth has become possible.

**Client-owned CMS accounts:** When Offshorly does **not** hold the credential, the client must be reminded to rotate their own. This reminder may be included in the monthly client report on every third cycle (every 3 months).

**Per-project / per-client overrides:** Client contracts may impose stricter rotation requirements; those supersede this policy (see Scope and Limitations).

#### 2.1.1 Implementation in Zoho Vault

Two password policies are configured in Zoho Vault:

- **Critical Accounts** — applied to all Tier A and Tier A2 credentials. Triggers a 90-day rotation alert to the credential owner.
- **Update May 2026 V2** — applied to Tier B credentials. No calendar-based expiry; rotation is event-driven.

When adding a credential, assign the appropriate policy. Re-classify existing credentials to the correct policy during the next rotation audit.

The 90-day rotation cycle is also activated at the platform level for the two Tier A accounts:
- **Zoho Vault master password** — configured via Vault's Master Password Policy.
- **Google Workspace account password** — configured via the Workspace admin console password-expiry policy.

#### 2.1.2 Enforcement

Platform-level expiry enforcement is active on two accounts:

- **Google Workspace**: password expiry is configured in the Workspace admin console at 90 days. Users cannot extend or bypass the policy.
- **Zoho Vault**: master password expiry is configured via Vault's Master Password Policy at 90 days.

For all other accounts — including Tier A2 accounts — enforcement relies on:

- Vault custom-policy expiry alerts (the **Critical Accounts** policy in Zoho Vault, which triggers a notification to the credential owner when it exceeds 90 days).
- Monthly access and rotation audit (§4).

Tier A2 accounts have high blast radius but do not support platform-level password expiry; rotation depends on owner discipline and audit follow-through. Labeling these accounts "recommended" is an honest acknowledgment of this reality, not a lesser commitment to securing them.

#### 2.2 Naming Convention
- Use the format **`Name/Site - Type`** for shared or company-owned credentials.
  - Examples: `offshorly.com - DB`, `offshorly.com - SSH`, `cloudflare.com - Root`
- For personal-use credentials tied to a specific employee, append the employee's first name in parentheses: **`Name/Site - Type (Name)`**.
  - Examples: `offshorly.com - WPAdmin (Ali)`, `kinsta.com - Admin (Ali)`
- The name suffix is how ownership is identified for rotation transfers and offboarding — keep it consistent.
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
- Before Vault access is revoked, the departing employee must **transfer ownership of all personal-use credentials they own to Ivory Hua**. This must happen while their account is still active — ownership cannot be transferred after access is revoked.

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

#### Lost or Stolen Devices

Any device with active work sessions must be reported **immediately** to the Security Officer and Management if lost or stolen — do not wait to confirm the device is truly gone.

- **Report via the standard incident reporting channel** (direct message to Security Officer and Management on company chat — see Reporting Channel above) as soon as the loss is discovered. Do not wait 24 hours hoping the device turns up; report first, recover later.
- The report must indicate which work sessions were active at the time (e.g., Google Workspace, Zoho Vault, client systems, GitHub, chat applications) so the Security Officer can force-logout those sessions from the admin side.
- This applies to **all devices with work accounts logged in**: laptops, phones, tablets, and any other device. Personal phones with work email, Vault, or chat apps logged in are included.
- If the device is recovered later, do not assume sessions are still secure — treat all active sessions as compromised until they have been force-logged out and re-authenticated from the admin side.

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