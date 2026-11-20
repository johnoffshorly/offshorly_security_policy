# Changelog

All notable changes to this project will be documented in this file.  
This project follows [Semantic Versioning](https://semver.org/).

---

## [v0.9] - 2026-11-17
### Added
- **Three new rules under Use of AI Tools:**
  - **Training and data retention:** work use only on plans that do not train on inputs (business, team or enterprise plans, or a personal plan with training switched off).
  - **Connectors and integrations:** connecting an AI tool to Gmail, Drive, Calendar, Zoho or chat tools gives it access to everything the account can see, so only Security Officer-approved tools may be connected, and only where the client contract allows AI use on that project's data.
  - **Meeting recorders and note-takers:** only in client meetings the client has agreed to, announced at the start, with recordings kept in an Offshorly account and treated as Confidential.

### Rationale
The 2026-11-17 review of third-party app access across one client project team found AI tools connected to Google accounts on most of the team (including Gmail and Drive connectors) and a meeting recorder connected for four people. The existing AI rules covered what may be pasted, but not which plans may be used, what may be connected, or how meeting recordings are handled.

---

## [v0.8] - 2026-11-17
### Added
- **New File Sharing rule under Data Classification and Handling.** Files are shared with named people; "anyone with the link" is only for genuinely public material and needs PM approval for client work; sharing outside Offshorly is limited to named contacts who need the file, for as long as they need it; named client contacts are preferred over shared client accounts; project leads review sharing at project end and when people leave; exceptions are agreed with the PM and Security Officer and recorded.
- **Records the supporting Google Workspace settings** (set 2026-11-17): new files private to the owner, a warning when sharing outside Offshorly, and the Access Checker limited to named recipients. "Anyone with the link" stays technically allowed until existing client files are moved to named sharing.

### Rationale
During the 2026-11-16 client allegation review, about 85 of one client's project files turned out to be shared as "anyone with the link", including the core requirements document. Google does not log views by people who open such a link without signing in, so the audit could not rule out outside access. Named sharing makes that question answerable next time.

---

## [IR Plan v0.14] - 2026-11-20
### Added
- **Runbook F step 11: "patched is not clean".** When a vulnerability was exploited before the fix reached a site, sweep every site that ran a vulnerable version for signs of compromise (unknown admins, accounts created after disclosure, application passwords, random-named plugins, Code Snippets, PHP in uploads, a changed `admin_email`, deleted-account gaps), on every host including managed ones. Version checks alone can't see an intruder who got in before the patch.
- **Runbook F step 12 and Post-Incident Review: every open follow-up gets an owner and a due date** in the triage sheet or Zoho.
- **Runbook F lessons from a client network's wp2shell compromise.**
- **Runbook C:** sites that don't send alerts to `infosec@` must run the Security Watch plugin or count as unmonitored; check for sites outside the deploy and update process (internal hubs, old public staging copies); user-sync plugins copy accounts between sites; look for database-stored backdoors that file scans miss; lock attacker accounts out first, then delete them once evidence is saved.
- **Kinsta specifics** under Hosting and Stack-Specific Containment: per-environment SSH, about 4 days of server access logs, file-only malware scans, IP Deny, database password rotation on Bedrock, backups in MyKinsta.
- **Open Items:** no recurring compromise sweep yet; new-admin alerts only cover one client network so far.

### Changed
- Version header updated to v0.14.

### Rationale
On 2026-11-20 wp2shell attacker accounts were found on every site of a client's WordPress network on Kinsta, created in July during the 11 days between the WordPress fix and the sites' update. Every later security check reported the sites as patched, which they were, so nothing looked for the accounts. The 2026-10-27 sweep that found the same campaign on Offshorly's staging servers had left managed hosts as an unscheduled follow-up. The network's internal hub, outside the deploy pipeline, stayed vulnerable and was fully taken over from September to November.

---

## [IR Plan v0.13] - 2026-11-17
### Added
- **New Runbook G: Client Allegation of a Leak or Misuse by Offshorly Personnel.** Covers a client suspecting that someone at Offshorly leaked or misused their material, data, or product ideas, or is behind a competing or look-alike account or business. Nicole owns the response; the Security Officer gathers facts; HR is involved before any staff-facing step. Steps: pin down the real concern, keep it need-to-know, preserve evidence with a log, gather public-source facts (company register, domain and hosting records), map who had access, check sharing and logs quietly before announcing anything to staff, then statements, then a client report that offers joint verification. Includes what the process can and can't show.

### Changed
- **Version header corrected.** The header still said v0.11 after the v0.12 changes; it now reads v0.13.
- **Runbook G step 8 now confirms NDAs and contracts instead of collecting signed statements by default,** matching management's decision during the first run. Signed statements remain a management call.
- **Runbook G lessons extended** with what the first run found: link-shared client files that cannot be audited for anonymous views, shared accounts blocking attribution on both sides, where each check lives in the Google Admin console, and that a leaver check must cover Google, Zoho, Git and client platform user lists.
- **Runbook E step 9 now names Offshorly's actual Zoho admin location** (Zoho Projects Plus admin panel: Projects > Settings gear > Directory > Users). The previous wording pointed at Zoho One or Zoho Directory, which Offshorly does not use for user management.

### Rationale
First raised on 2026-11-16, when a platform client asked whether Offshorly staff were behind a newly registered competitor with a near-identical pitch. The plan had no runbook for an allegation against Offshorly's own people. The case showed that the order of steps matters: checking sharing and logs before telling staff preserves evidence, and having the client pull or co-review its own logs makes the findings credible to them.

---

## [IR Plan v0.12] - 2026-10-27
### Corrected
- **The Vultr + RunCloud section claimed per-app isolation like Cloudways. That is wrong for Offshorly's servers.** On SNG Staging Test all 30 apps run as the one shared `runcloud` user, so one compromised app can reach every other app's files and database credentials. The section now treats these servers like the cPanel shared-hosting case (every app in scope until ruled out), adds the containment steps actually used (server-wide basic auth, taking one app offline by moving its files out of the webroot), and names per-app system users or a move to an isolated server as the standing fix.

### Added
- **Bedrock section: core security updates and login URLs.** Bedrock pins WordPress in composer, so WordPress's forced security auto-updates never reach these sites, and a vulnerable version pinned in a boilerplate is inherited by every project built from it. A custom login URL is not a defence on its own: it is changed per site at deployment, but the boilerplate's default slug remains on staging copies and undeployed boilerplate installs.

### Rationale
Found during the 2026-10-27 SNG Staging Test incident: one staging app backdoored since June through a valid admin login, plus about 90 backdoor admin accounts on three boilerplate-based staging apps created through the wp2shell WordPress core chain (CVE-2026-63030 + CVE-2026-60137), which their pinned WordPress versions never received the fix for. Attackers also found and logged in through the boilerplate's default login slug, still in place on the two boilerplate staging installs.

---

## [IR Plan v0.11] - 2026-10-22
### Corrected
- **v0.10 wrongly claimed no site/server inventory exists.** It does: the CMS Projects Masterlist (a Google Sheet with ~72 projects, active and inactive, including live/staging URLs, hosting, maintenance status, repo, and Vault credential links). Checked directly, it would have immediately surfaced several of the legacy sites the PHP2Shell response only found through ad hoc digging, including two that were already tracked there as active projects. Rewrote Runbook F step 2, step 7, the Lessons, and the corresponding Open Item to reflect this accurately: the real gaps are that nobody thought to check it during the incident, it's missing security-relevant fields (PHP version, server/IP, last-verified date), roughly one project in six has no Maintenance Status recorded, and at least five domains found during the response  aren't in it at all.

---

## [IR Plan v0.10] - 2026-10-22
### Added
- **New Runbook F: Platform or Infrastructure Vulnerability Disclosure**, drawn directly from the PHP2Shell (CVE-2026-109618/109619) response run by the team on 2026-10-22, the first time the IR process ran without the Security Officer present. Covers triaging by hosting provider rather than by site, verifying a fix two ways (version installed AND service restarted), flagging EOL/unsupported platforms for migration instead of in-place patching, sweeping for legacy or former-client sites Offshorly still has some trace of (an old GitLab repo, a past hosting account, a DNS record) and giving them a good-faith heads-up even without an active contract, and closing the loop with any external party who flagged the issue.
- **New Open Item: no single, durable inventory of every site or server Offshorly has ever built, hosted, or had access to.** The PHP2Shell response had to rebuild the host list from memory mid-incident, and separately found former-client sites only through ad hoc individual digging. Runbook F's sweep step is a stopgap; the actual fix (a maintained inventory) is a Security Roadmap prevention item.

### Rationale
This was the first real test of the plan running without the Security Officer in the room, and it surfaced gaps the document hadn't accounted for: a vulnerability class this plan had no runbook for (a platform-level CVE, not a single-site compromise or account issue), a verification discipline gap ("the provider says it's patched" isn't the same as confirming it), and a duty-of-care gap toward sites Offshorly no longer actively maintains but still has some lingering connection to. Writing these in while the incident is still fresh, rather than after memory of it fades, is what makes the plan actually reflect how the team behaves under pressure.

---

## [IR Plan v0.9] - 2026-10-19
### Changed
- **Runbook C step 1 now describes automated alert triage instead of a manual inbox read.** An automated tool (Google Apps Script, repo gitlab.com/offshorly-cms/infosec-siem) grades every alert sent to `infosec@offshorly.com`, logs it to the "InfoSec SIEM Log" sheet with a triage Status column, posts CRITICAL/HIGH alerts on Monthly Report sites to `#infosec-alerts` in Cliq, and posts a 09:00 Manila daily digest there. The step makes clear the tool narrows down what needs attention but a person still makes the call.
- **Added the priority-site rule.** Sites tagged "Monthly Report" in BlogVault are worked on straight away; an issue on any other site needs approval before anyone works on it. Reflected in Runbook C's "at a glance" and steps 1 and 4, and in the CMS Dev Team row under Roles. A suspected active compromise on a non-priority site is still reported to the Security Officer immediately; approval governs who spends time fixing it, not whether anyone is told.

### Resolved
- **Open Item "No automated alert-triage tooling" closed**, with its two remaining limits noted: the tool runs under the Security Officer's Google account, and alerts held in the group's moderation queue aren't triaged until released.

---

## [IR Plan v0.8] - 2026-10-14
### Changed
- **Removed repeated "John doesn't handle this" / "Nicole isn't involved by default" phrasing.** v0.7's fixes for who performs account resets and how escalation routes restated the negative (who doesn't do something) in nearly every place it touched: Roles, Runbook B's and Runbook E's "at a glance," and both Communication bullets. Trimmed to one authoritative statement (the "Who actually resets an account" note under Roles) and plain positive statements everywhere else ("Ali, Ivy, or Nicole perform the reset"), rather than restating the exclusion each time.

---

## [IR Plan v0.7] - 2026-10-14
### Added
- **New Definitions section**, a plain-language glossary for recurring technical terms (rotate, revoke/force-log-out, isolate/contain, persistence mechanism, preserve/snapshot, eradicate, severity/classify), for readers who aren't deep technical users but do perform these actions (Ivy, Nicole).
- **"At a glance" summary** at the top of every runbook (A through E): who acts, typical severity, and the core action sequence, before the detailed numbered steps.
- **New first step in Runbook B:** before anything else, the employee removes access on their own end if still possible (remote sign-out, their own Find My/remote lock, or physically retrieving the device), in parallel with reporting, not instead of it.

### Changed
- **Escalation re-routed through PMs for anything short of Critical.** Roles, the Restricted-data severity override, and the Communication section all previously routed to Nicole by default. Now: Critical goes to Nicole directly and immediately; High goes to the relevant PM first, with Nicole looped in only when her resourcing/legal/client-facing call is actually needed; Medium/Low stay in the normal channel with the PM informed. Nicole's and the PM's Roles rows updated to match.
- **Corrected who actually performs an account reset.** John (IR Backup/Technical Advisor) was described as handling session revocation and credential rotation directly; per Ivy, that's actually done by the admins with Google Workspace/Zoho console access (Ali, Ivy, Nicole), not John, whose role is advisory (forensic reasoning, infrastructure decisions). Fixed in Roles (new explicit note), Runbook B, and Runbook E's "at a glance."
- **De-duplicated lost-device guidance.** The Quick Reference table in Runbook E repeated a generic "lost a device" instruction that overlapped with Runbook B. Split into two distinct, clearly different rows: losing just the authenticator app (device still in hand) stays in Runbook E, losing the device itself now points to Runbook B instead of restating it.
- **Resolved the reporting-channel Open Item.** `infosecadmin@offshorly.com` and `infosec@offshorly.com` are two intentionally separate addresses, not an inconsistency: `infosecadmin@offshorly.com` is for a person reporting an incident (alongside the existing chat DM to Security Officer + Management), `infosec@offshorly.com` is where automated security-tool alerts from various projects land. Updated the Reporting Channel section, Runbook B, Runbook C's alert-triage step, Runbook E's employee steps and quick-reference table, and Open Items to reflect this.

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
- The runbooks fold in concrete lessons from a 2025 client multi-site compromise: check for persistence mechanisms that survive a password reset (WordPress Application Passwords, malicious browser extensions with session-cookie access, email forwarding rules) before calling an account compromise closed; check for credential reuse across unrelated systems; preserve a snapshot of a compromised site before remediating it; check whether other sites share the same codebase, host, or credential before assuming an incident is contained; audit a live server for security tooling installed outside the git/deploy pipeline before any migration touches it, so a redeploy can't silently drop a WAF or 2FA; check for exposed database dumps in git history or web-served directories; and confirm 2FA is actually enforced, not just installed, before closing out.
- A "When an Incident Outgrows Routine Response" section, addressing the real capacity/burnout and remediation-funding questions that incident raised: a sustained incident is flagged to Nicole as a resourcing question rather than absorbed solo, and remediation costs beyond routine maintenance are a leadership funding decision, not the Security Officer's to resolve alone.
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