# Changelog

All notable changes to this project will be documented in this file.  
This project follows [Semantic Versioning](https://semver.org/).

---

## [IR Plan v0.5] - 2026-09-14
### Added
- **Draft AWS and Azure sections** in "Hosting and Stack-Specific Containment," as a starting outline for John rather than a blank page: root/Global Admin compromise (Critical severity, same tier as Google Workspace admin or Zoho Vault master), IAM user/role and Entra ID account compromise, EC2/VM isolation and snapshot-before-terminate, S3/Blob exposure, Secrets Manager/Key Vault rotation, and which logs (CloudTrail, VPC Flow Logs, GuardDuty, Azure Activity Log, Entra sign-in logs) are the forensic source of truth. Explicitly marked as a generic, unvalidated draft, not checked against Offshorly's actual client account structures, pending John's review.

### Changed
- Flagged "Hosting and Stack-Specific Containment" as CMS/WordPress-only for now; enterprise-stack sections (AWS, Azure, etc.) are pending, John to add those given his background there. Noted inline in that section and added to Open Items.

---

## [IR Plan v0.3] - 2026-09-14
### Added
- **New section: "Hosting and Stack-Specific Containment."** Runbook C's steps are the same everywhere; this breaks out what "rotate credentials," "take offline," and "verify clean" actually mean per environment: Cloudways (DigitalOcean, the current standard, per-app isolation, Varnish purge, platform-level access), cPanel/shared hosting (legacy, multi-domain blast radius, no-SSH constraints, the standing fix of migrating off entirely), Vultr + RunCloud, Kinsta and other managed WP hosts (personal-use accounts, platform restore points), Bedrock/Composer-managed vs. vanilla WordPress (how to verify each is actually clean), and shared codebase networks (a fix on one site generally applies to every site on that codebase). Cross-referenced from Runbook C.

---

## [IR Plan v0.2] - 2026-09-14
### Added
- **Runbook E: Account Access Recovery (Offshorly Email / Google Workspace, and Zoho).** Merged in from HR's Account Access Recovery guide: the employee-side self-recovery checklist, the admin/Infosec five-phase recovery procedure (verify and contain, recover Google, recover Zoho, sweep for persistence, close out), and a quick-reference table. Cross-referenced from Runbooks A and B, which now point here for the Google/Zoho-specific mechanics instead of describing them generically. Added the explicit principle that an access-recovery request is a known social engineering vector and must be verified through a second channel before any reset.
- Runbook A's persistence-check step now also names Google App Passwords (not just WordPress Application Passwords) and Gmail-specific persistence (forwarding rules, filters, send-as, delegation) as things that survive a plain password reset.
- New Open Items, surfaced while merging HR's guide: the reporting channel is inconsistent across documents (this plan's company-chat DM vs. the HR guide's `infosecadmin@offshorly.com`, itself inconsistently written elsewhere in the same guide as `infosec@offshorly.com`); the Security Policy's MFA section says centralized MFA is "under review" while the HR guide asserts it's already enforced org-wide, one of these is stale; and HR's onboarding/offboarding redesign (Onboarding 2.0, on Zoho) needs to explicitly carry forward the existing onboarding and offboarding security requirements, which aren't mentioned in that roadmap yet.

### Rationale
HR had already written a detailed, Google Workspace/Zoho-specific account recovery procedure that was more concrete than this plan's original generic version of the same steps. Merging it in raises the whole document's operational detail rather than leaving two separate, partially-overlapping procedures to drift apart. The conflicts it surfaced (reporting channel, MFA status) are flagged rather than silently resolved, since they're facts about the org's actual current state that need confirming with HR, not something this document can decide unilaterally.

---

## [IR Plan v0.1] - 2026-09-14
### Added
- **New companion document: `INCIDENT_RESPONSE.md`.** The standalone Incident Response Plan that the Security Policy's Incident Response section has referenced since it was written, but that didn't exist as its own document until now. Covers roles and responsibilities (Security Officer as incident commander, John as IR backup/technical advisor, Nicole and Ivy's business/HR roles, the CMS team's front-line role, all-personnel reporting duty), a severity classification scheme (Critical/High/Medium/Low, with an automatic override for Restricted/minors' data), a NIST SP 800-61-aligned lifecycle (Preparation, Detection and Analysis, Containment/Eradication/Recovery, Post-Incident Activity), and four runbooks: Account or Credential Compromise, Lost or Stolen Device, WordPress Site Compromise or Security Alert Escalation, and Phishing Report.
- The runbooks fold in concrete lessons from the aegisuk.net/Grovepark Design incident: check for persistence mechanisms that survive a password reset (WordPress Application Passwords, malicious browser extensions with session-cookie access, email forwarding rules) before calling an account compromise closed; check for credential reuse across unrelated systems; preserve a snapshot of a compromised site before remediating it; check whether other sites share the same codebase, host, or credential before assuming an incident is contained; audit a live server for security tooling installed outside the git/deploy pipeline before any migration touches it, so a redeploy can't silently drop a WAF or 2FA; check for exposed database dumps in git history or web-served directories; and confirm 2FA is actually enforced, not just installed, before closing out.
- A "When an Incident Outgrows Routine Response" section, addressing the real capacity/burnout and remediation-funding questions the aegisuk.net incident raised: a sustained incident is flagged to Nicole as a resourcing question rather than absorbed solo, and remediation costs beyond routine maintenance are a leadership funding decision, not the Security Officer's to resolve alone.
- An honest Open Items list: no legal/regulatory breach-notification process yet, no automated alert-triage tooling across monitored sites yet, device management is self-attested only, and the shared dev-tools account (Ali/Ivy/John) is still an open decision, not yet split or tightened.

### Rationale
The Security Policy has said "Offshorly maintains an Incident Response Plan" since it was written, without that plan actually existing as a document. Writing it down, and building the runbooks around what has actually gone wrong so far rather than a generic template, closes the most concrete overdue gap identified in the Security Roadmap's first workstream.

---

## [v0.7] - 2026-07-08
### Changed
- **§2.1 Password Rotation** — introduced a three-tier rotation model. Tier A (mandatory, platform-enforced, 90 days): Google Workspace account password and Zoho Vault master password only — the two accounts where platform-level expiry can actually be enforced. Tier A2 (recommended, 90-day cycle, audit-verified): Microsoft 365, domain registrar, cloud root accounts, financial accounts, CMS admin accounts, and other high-privilege accounts; must be stored in Vault under the **Critical Accounts** policy. Tier B (event-based, no calendar rotation): DB credentials, SSH key-based auth, API keys, deploy tokens, vendor portals, low-privilege accounts.
- **§2.1.1 Implementation in Zoho Vault** — documents the two active Vault password policies: **Critical Accounts** (applied to Tier A and Tier A2 credentials; triggers 90-day rotation alert to the credential owner) and **Update May 2026 V2** (applied to Tier B credentials; no calendar expiry). Notes that the Tier A platform-level policies (Vault Master Password Policy and Workspace admin console) are now active.
- **§2 Credential ownership model** — Zoho Vault does not support secondary alert recipients; only the credential owner receives rotation alerts. Ownership is now split by type: employees own personal-use credentials tied to their own identity (and receive their own alerts); Ivory Hua owns company-critical shared credentials (and actions or coordinates rotation on those). Existing credentials owned by Ivory Hua will transfer back to the respective employee when the next rotation alert fires, using the naming convention to identify the correct owner.
- **§2.2 Naming Convention** — personal-use credentials (tied to a specific employee) now append the employee's first name in parentheses: `Name/Site - Type (Name)` (e.g., `offshorly.com - WPAdmin (Ali)`). Shared/company credentials retain the existing `Name/Site - Type` format. The name suffix is the primary identifier for ownership transfers during rotation and offboarding.
- **§5 Offboarding** — added requirement: before Vault access is revoked, the departing employee must transfer ownership of all personal-use credentials they own to Ivory Hua while their account is still active.

### Added
- **§2.1.2 Enforcement** — new subsection clarifying that platform-level expiry is configured on Google Workspace (admin console) and Zoho Vault (Master Password Policy) only; all other rotation including Tier A2 is enforced via the Critical Accounts Vault policy alert and the monthly audit (§4).
- **Lost or Stolen Devices** — new subsection under Incident Response. Employees must report lost or stolen devices with active work sessions immediately (do not wait 24 hours); report must list active sessions so force-logout can be issued from the admin side; applies to all devices with work accounts logged in, including personal phones; device recovery does not restore session trust.

### Rationale
Mandatory rotation is only meaningful where platform-level expiry can be enforced. Listing accounts we cannot force-expire as "mandatory" describes an aspiration, not a control. Concentrating Tier A on the two accounts where enforcement is real, and being explicit about the audit-based nature of Tier A2, gives a more accurate picture of actual security posture. The Vault alert-based enforcement for Tier A2 is honest about what it is: a nudge and an audit trail, not a hard control.

---

## [v0.5.1] - 2026-05-25
### Changed
- **§2.1 and §6 SSH rotation rule** clarified with a fallback:
  - When key-based SSH authentication is in use, SSH credentials remain exempt from calendar-based rotation.
  - When key-based auth is not possible for a particular system (legacy host, vendor appliance, client environment that does not support it), password authentication is permitted as an exception and the SSH password **must** be rotated on the standard 90-day cycle.
  - Reason for the fallback must be documented in the vault entry; lead/PM should periodically re-check whether key-based auth has become possible.

---

## [v0.5] - 2026-05-25
### Added
- **Scope and Limitations** section (new, near the top):
  - States that Offshorly is permanent WFH and personal devices are common, so the policy sets an enforceable baseline.
  - Per-client / per-project requirements supersede this policy where stricter; this policy applies where client requirements are looser.
  - This policy is the floor, not the ceiling.
- **Personal Device Baseline (mandatory)** under Device and Endpoint Security:
  - Full-disk encryption.
  - OS + browser auto-updates enabled.
  - Screen lock ≤ 5 minutes with password/biometric.
  - Antivirus / EDR running.
- **Use of AI Tools** section:
  - Credentials, secrets, and PII never to be pasted into AI tools (no exceptions).
  - Client code and project data: per client contract; confirm with PM/lead before use.
  - Offshorly-internal code: permitted, subject to credential rule.
  - Output review required; AI tool accounts must use Offshorly email.
- **Incident Response → Reporting Channel** subsection:
  - Primary channel: DM Security Officer + Management on company chat app.
  - Off-hours: same channel, do not wait for business hours.
  - Required follow-up content within 24 hours (what, when, what's affected, actions taken).
- **Password generation rule** (§2): all passwords must be generated by ZohoVault or the source app; human-chosen passwords are not permitted.
- **Offboarding ownership** stated explicitly under §5 (HR with PM / project lead).

### Changed
- **§2.1 DB/SSH rotation exemptions** now include the reasoning (DB protected by network controls and not human-typed; SSH uses key-based auth per §6, so there is no password to rotate).
- **§6 Server Security** clarified that SSH keys are per-user, password auth must be disabled in `sshd_config`, and keys rotate on personnel change or suspected compromise.
- **Device and Endpoint Security** restructured:
  - Mandatory personal device baseline is now a distinct, top-level subsection.
  - Dual-boot / VM guidance is moved to "Optional Stronger Configurations" and labeled as encouraged, not required.
  - Future direction noted: company-owned dedicated work devices when budget permits.

---

## [v0.4] - 2026-05-25
### Added
- **Section 6: Server Security** (new section)
  - Prefer Red Hat–based distributions (Fedora, Rocky Linux) for company servers.
  - SSH password authentication must be disabled; SSH key authentication is the default and required method.
  - SSH keys must be generated per user; shared keys are prohibited.
- **GitHub Repository Access**: new requirement that all new repositories be created using the `dev@offshorly.com` account to ensure consistent ownership and auditability from inception.
- **Credential and Password Storage → Password Rotation** (new subsection 2.1):
  - All credentials must be rotated every 90 days.
  - **Exemptions:** DB credentials and SSH credentials are not required to be rotated.
  - Offshorly-owned CMS accounts rotate on the 90-day cycle.
  - Client-owned CMS accounts: client must be reminded to rotate; reminder may be included in the monthly client report every third cycle.
  - All other systems: rotation cadence defers to the relevant lead, PM, or client.
- **Credential and Password Storage → Naming Convention** (new subsection 2.2):
  - Format: `Name/Site - Type` (e.g., `offshorly.com - WPAdmin`, `offshorly.com - DB`, `offshorly.com - SSH`).
  - Apply to all new credentials; update existing active credentials to match.

### Changed
- Credential labeling bullet now references the formal Naming Convention subsection instead of a generic instruction.
- Renumbered: "Monthly Policy Review and Updates" is now Section 7 (previously 6) to accommodate the new Server Security section.

---

## [v0.3.1] - 2025-09-08
### Fixed
- Minor updates to `README.md` for credential storage instructions and formatting.

---

## [v0.3] - 2025-09-08
### Added
- Credential and Password Storage section updated:
  - Require ownership of new ZohoVault credentials to be transferred to Ivory.
  - Require credentials to be labeled properly.
  - Require placement of credentials in the correct project folder.

---

## [v0.2] - 2025-09-08
### Added
- Detailed phishing awareness guidelines (hovering over links, verifying senders, etc.).
- Device/endpoint security details for employees using personal laptops:
  - Laptops with 1TB+ storage → partition and dual boot.
  - Laptops with ≤500GB storage:
    - If another SSD slot is available → request a new SSD, use dual boot.
    - If no extra slot → request larger SSD, partition, then dual boot.
- Requirement to include `dev@offshorly.com` in repository audits.
- Centralized MFA management (in review).
- VPN solution (WireGuard) under consideration.

---

## [v0.1] - 2025-08-20
### Added
- Initial security policy guidelines.
- General device/endpoint security section.
- MFA enforcement for employees.