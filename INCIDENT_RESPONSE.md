# Offshorly Incident Response Plan
**Version:** 0.3 | **Status:** Draft | **Review Cycle:** Monthly | **Date:** September 14, 2026

---

## Purpose

This document is the companion Incident Response Plan referenced in the [Security Policy](README.md) ("Offshorly maintains an Incident Response Plan outlining escalation procedures and responsibilities"). It defines who does what during a security incident, how incidents are classified, and step-by-step runbooks for the scenarios Offshorly actually encounters.

It follows the same rolling-release model as the Security Policy: reviewed monthly, versioned, and changed via the shared [CHANGELOG](CHANGELOG.md).

---

## Scope and Limitations

- Applies to all Offshorly personnel, contractors, and any Offshorly or client system.
- Where a client contract sets a stricter incident-response or breach-notification requirement, that requirement takes precedence for that project (same principle as the Security Policy's Scope and Limitations).
- This is a working plan for a team where security is a shared, part-time responsibility, not a dedicated security operations function. Steps are written to be run by whoever is available and reachable, not to assume specialist tooling or staffing that doesn't exist yet. Where a step depends on something Offshorly doesn't have in place, that's called out explicitly rather than assumed.

---

## Roles and Responsibilities

Offshorly's day-to-day security work is done by an informal team of four, each with security as a secondary responsibility on top of their main role. This plan uses that same team as the incident response team, with the Security Officer as incident commander.

| Role | Person | Responsibility during an incident |
|---|---|---|
| **Security Officer / Incident Commander** | Ali | Owns triage and severity classification. Coordinates containment and recovery. Runs the post-incident review. Is the default point of contact for the reporting channel below. |
| **IR Backup / Technical Advisor** | John | Steps in when Ali is unreachable or unavailable. Second opinion on technical containment decisions (session revocation, credential rotation, site remediation). Advisory, not hands-on by default. |
| **General Manager** | Nicole | Business-side decisions during an incident: resourcing, prioritization against client work, and any client- or public-facing communication. Final call on anything with legal or reputational exposure (see Open Items). |
| **HR** | Ivy | Personnel-side actions: offboarding-related compromises, coordinating with the affected employee, anything that touches employment status. |
| **CMS Dev Team** | Project-assigned devs | First line of detection and action for WordPress vulnerabilities and site alerts on projects they maintain. Apply fixes directly within their available capacity (existing practice); escalate to the Security Officer when a fix is out of capacity, the site is unmaintained, or a compromise (not just a disclosed vulnerability) is suspected. |
| **PM / Project Lead** | Varies | Escalation point when the Security Officer and Management are both unreachable (per the Security Policy's reporting channel). Owns client-facing coordination for their project alongside Nicole. |
| **All personnel** | Everyone | Reporting obligation: report suspicious activity within 24 hours via the standard channel (see below). Report device loss and phishing immediately, don't wait. |

None of these roles are full-time security positions. Capacity is shared across this list plus everyone's primary job, which is the real constraint on response speed, not any single person's availability.

---

## Reporting Channel

Same channel as the Security Policy, restated here for completeness:

- **Primary:** direct message to the Security Officer and Management on the company chat app, including off-hours, weekends, and holidays. Don't wait for business hours.
- **If unreachable:** escalate to the employee's PM or project lead, and keep attempting to reach Security/Management.
- **Follow-up in writing** within 24 hours (email or written chat summary), including: what was observed, when, systems/accounts/data potentially affected, and actions already taken.

---

## Severity Classification

The Security Officer classifies every reported incident as soon as there's enough information to do so, and can re-classify as more facts come in. Classification drives response time and who gets pulled in.

| Severity | Definition | Examples | Initial response target |
|---|---|---|---|
| **Critical** | Confirmed unauthorized access to a company-critical system, or active exposure of client data. | Compromised Google Workspace admin, Zoho Vault master, hosting/cloud root, or financial account. Active client data breach. Malware or compromise spreading across multiple sites/accounts at once. | Immediate. Security Officer (or John) acknowledges and begins containment within 1 hour of report; Nicole notified immediately. |
| **High** | A single account, device, or site is confirmed compromised, or a credential is confirmed leaked, but scope looks contained. | One employee account compromised. Lost/stolen device with active work sessions. One client WordPress site confirmed compromised (malware, defacement, unauthorized admin user). Credential found exposed (public repo, paste site, etc.). | Within 4 hours. |
| **Medium** | Something suspicious or newly disclosed that hasn't been confirmed as exploited yet. | Phishing message reported but not actioned. Suspicious login flagged but not confirmed malicious. A disclosed WordPress vulnerability affecting a site, not yet known to be exploited. A security-plugin alert that looks like more than routine blocked-scan noise and needs a human look. | Within 24 hours (matches the Security Policy's existing 24-hour reporting window). |
| **Low** | A policy gap or false positive with no active compromise. | Credential found stored outside Vault. Personal email used for a work tool. A security-plugin alert that turns out to be routine blocked scan traffic. | Next business day, or rolled into the monthly audit. |

When in doubt, classify one level higher and downgrade later. It's cheaper to stand down from a false alarm than to under-react to a real one.

**Automatic override:** if an incident touches data in the Restricted category (see the Security Policy's Data Classification section), including anything relating to minors, classify as at least High regardless of how contained the technical scope looks, and loop in Nicole immediately. This isn't about technical severity, it's about legal and reputational exposure that a small technical footprint can still carry.

**Scope can grow after initial classification.** A report that looks like "one site is down" can turn out to be a months-long compromise across several accounts on shared infrastructure once someone actually looks. Re-classify as facts come in rather than anchoring to the first report, and see "When an Incident Outgrows Routine Response" below for what to do when it does.

---

## Incident Lifecycle

This follows the same shape as the standard industry model for incident response (NIST SP 800-61: Preparation; Detection and Analysis; Containment, Eradication, and Recovery; Post-Incident Activity), adapted to Offshorly's actual team and tooling rather than assuming a dedicated SOC.

0. **Preparation**: the ongoing, before-anything-happens work that makes the rest of this possible: this plan itself being current, the reporting channel being known to everyone, Vault credential ownership being accurate (see the Security Policy §2), backups actually existing and being restorable, and the contact list below being current. Preparation gaps found outside an active incident go to the Security Roadmap, not into this plan as a workaround.
1. **Report**: via the channel above.
2. **Triage and classify (Detection and Analysis)**: Security Officer assigns severity (see table) and confirms scope: which systems, accounts, or data are potentially affected. Confirm the scope question again once initial containment is done, since it commonly expands.
3. **Preserve, then contain**: before making changes, capture what you can of the current state (who has access right now, what's in the logs, what's actually installed) so the investigation isn't destroyed by the cleanup itself. Then stop the bleeding: revoke sessions, rotate credentials, isolate the affected site or account. Runbooks below cover the common cases.
4. **Eradicate**: remove the actual cause (malicious file, unauthorized user, a persistence mechanism, the phished credential's usefulness to an attacker), not just the symptom. Check for anything that would survive a plain password rotation (see Runbook A) before calling this done.
5. **Recover**: restore normal operation, confirm the fix holds (re-scan, re-check logs) before standing down.
6. **Post-incident review**: for Critical and High incidents, a short blameless review (see below).
7. **Policy update, if needed**: anything that should change how Offshorly operates going forward goes through the normal rolling-release process in the Security Policy and CHANGELOG, not as an ad hoc fix mid-incident.

### When an Incident Outgrows Routine Response

Some incidents stop being a same-day fix and become a sustained, multi-week investigation. When that happens:

- The Security Officer flags this explicitly to Nicole as a capacity and resourcing question, not something to quietly absorb solo. Ongoing security work is already split across four part-time people (see Roles above); a large incident competing with that same limited time is a real constraint, not a personal shortcoming to push through.
- Remediation costs that go beyond routine maintenance (a hosting migration, a full site rebuild, extended forensic work) are a business decision on who pays and who resources it, owned by Nicole/leadership, not something the Security Officer is expected to fund or resolve unilaterally.
- John (IR Backup/Technical Advisor) should be actively pulled into hands-on work during a sustained incident, not just consulted, if the load justifies it.

---

## Communication

- **Internal, Critical/High severity:** Security Officer opens a dedicated thread/channel for the incident so the timeline stays in one place, and pulls in Nicole and the relevant PM immediately.
- **Internal, Medium/Low severity:** handled in the normal reporting channel; logged for the monthly audit.
- **Client-facing:** Nicole and the project's PM/lead own the content and timing of any client communication. The Security Officer's role is to provide the technical facts (what happened, what's affected, what's been done), not to draft or send client-facing messages directly.
- **Client-owned credentials:** if the client holds the credential themselves (not Offshorly), remind them to rotate it as part of the incident, same as the existing monthly-report reminder cadence in the Security Policy.

---

## Runbooks

### A. Account or Credential Compromise

**Trigger:** suspicious login, a leaked credential, a phishing victim who entered credentials, or any other confirmed/suspected unauthorized access to an account.

1. Identify scope: which account, and which systems that account (or its Vault entry) can reach.
2. **Before changing anything, note what's currently there**: active sessions, logged-in devices, and (for a WordPress account) any Application Passwords on the user. Rotating the password first and looking later has already cost a real investigation the ability to tell how an attacker got in.
3. Force-log-out or revoke active sessions for that account from the admin side (Google Workspace admin console, Zoho Vault, GitHub, CMS admin panel, as applicable). For the Google Workspace and Zoho specific mechanics, use Runbook E.
4. Rotate the compromised credential, and treat any secret that account could view or copy in Vault as potentially exposed too, not just the account's own password.
5. **Check for persistence mechanisms that survive a plain password rotation**, not just the password itself:
   - **WordPress Application Passwords** on the affected account (Users → Profile → Application Passwords), and **Google App Passwords** on a Workspace account. Both authenticate independently of the login password and of 2FA, and a rogue one will quietly survive a full password reset.
   - **Browser extensions** on the device that was used to log in, especially anything with `cookies`/`webRequest`/`debugger`/`<all_urls>` permissions. A malicious extension steals the live session cookie, which bypasses unique passwords and MFA entirely, no credential theft required.
   - **Email forwarding rules, filters, send-as addresses, or mailbox delegation** (Gmail settings), and **OAuth app grants** on either Google or Zoho.
6. **Check whether the same credential (or the same password) is reused anywhere else**: search Vault, and check the affected employee's saved browser passwords for the same login used against other, unrelated sites or systems. A reused credential is how a single compromise on one system has turned into multiple compromised sites before; treat every match as in-scope until ruled out.
7. Check Vault's access log for that account for unusual reads around the suspected window.
8. Notify the credential's owner (per the naming convention in the Security Policy §2.2) so the right person knows what changed and why.
9. If MFA appears to have been bypassed rather than just the password compromised, treat as Critical, not High. This is fine to update after the fact when the full picture is clear.
10. Log the timeline and hand off to the Security Officer for classification and closeout.

### B. Lost or Stolen Device

Expands the Security Policy's "Lost or Stolen Devices" section into concrete steps.

1. Report immediately via the standard channel. Don't wait to confirm the device is truly gone.
2. List every work session known to be active on the device (Google Workspace, Zoho Vault, client systems, GitHub, chat apps).
3. Security Officer (or John, if unavailable) force-logs-out each listed session from the admin side. See Runbook E for the Google Workspace and Zoho specific steps.
4. If the device has remote wipe/lock available and configured (e.g. Find My, Android device manager) and holds company data, trigger it. **Note:** device management is currently self-attested only, not centrally enforced by Offshorly, so this step depends on whatever the employee personally has set up. Closing that gap is tracked as a prevention item, not solved by this plan.
5. Rotate any credential that was visible or auto-filled on the device (browser-saved passwords), not just the account-level passwords already covered in step 3.
6. If the device is later recovered, don't trust it as clean. Re-authenticate all sessions fresh rather than assuming recovery means the sessions were never touched.
7. Close out once the employee confirms all sessions are re-authenticated; log the incident.

### C. WordPress Site Compromise or Security Alert Escalation

**Trigger:** a security-plugin alert (Wordfence, file-change monitor, BlogVault, MalCare, or whichever tool the site uses) that looks like more than routine blocked-scan traffic: a new vulnerability disclosure, malware or file-change detection, or a WAF block-rate spike well above that site's normal baseline.

1. **First-line triage** (currently manual, per-site, done by whoever monitors that site, usually Ali or the assigned CMS dev): distinguish routine blocked-scan noise from something that needs action. **Note:** an automated, aggregated triage flow across all monitored sites doesn't exist yet; this is tracked as an open prevention item, so for now this step relies on a human actually reading the alert.
2. **Check whether this site shares infrastructure or a codebase with others** before assuming the incident is contained to the one reported site: the same hosting account, the same shared theme/codebase, or a shared/reused admin credential all mean other sites are potentially affected too. Compromises have previously spread silently across multiple domains on one shared host this way.
3. **If confirmed compromise** (malware found, unexpected admin user, defacement, unexplained file changes). The steps below apply everywhere; for the actual mechanics of rotating access, taking a site offline, and verifying it's clean on the specific host and codebase it runs on, see "Hosting and Stack-Specific Containment" after this runbook:
   - **Preserve a snapshot (files + database) before remediating**, even though the site is compromised. Cleaning up first, without keeping a copy of the "as found" state, has previously made it impossible to trace how or when an attacker got in.
   - Treat the site's hosting/CMS admin credentials as potentially exposed; rotate them.
   - Put the site in maintenance mode or otherwise restrict public access if it's actively serving malicious content. If the live code itself isn't trusted yet, use a static maintenance page rather than a WordPress plugin (a plugin still runs on the untrusted codebase).
   - Restore from the last known-clean backup if one exists, or manually remove the malicious files/plugins/users after identifying how they got in.
   - As part of root-causing, check for WP core/plugin version drift, including whether the site's `.gitignore` has a blanket `*.lock` rule excluding `composer.lock`, which silently turns `composer install` into an uncontrolled `composer update` and can drift versions unnoticed. This has been found on more than one Offshorly site before.
   - **If this investigation involves a migration or redeploy of any kind, first audit the live server directly for anything installed outside the normal git/deploy pipeline**: security plugins, WAF rules, 2FA enforcement, custom login URLs. A pure code-based deploy has previously gone live missing a WAF, 2FA, and the default login page exposed, because those were only ever installed by hand on the old server and were never in git.
   - **Check for exposed database dumps**: a raw `.sql` file committed into git history, or an export left inside a web-served directory (e.g. an uploads/export folder from a DB-migration plugin), either of which can expose the entire database to anyone who finds the URL or the repo. Remove and, if it was in git, purge it from history rather than just deleting the latest copy.
   - Re-scan clean before lifting maintenance mode.
   - **Before closing out, confirm 2FA is actually enforced for every admin account, not just installed as a plugin.** A previously-compromised site can still show 0 of its admins actually protected months later if this isn't checked explicitly.
4. **If a vulnerability is disclosed but not yet exploited:** the CMS team applies the fix directly if it's within their capacity for that project (existing practice). If it's out of capacity, or the site has no assigned maintainer, escalate to the Security Officer to track until patched.
5. Notify the PM/client per their contract; if the client owns the credential, fold the rotation reminder into the next monthly report.
6. Log the outcome regardless of severity. This feeds both the monthly audit and the quarterly WordPress vulnerability check-in.

## Hosting and Stack-Specific Containment

Runbook C's steps are the same everywhere. What "rotate credentials," "take the site offline," and "verify it's clean" actually mean depends on where the site lives and how its code is managed. This section is organized by the environments Offshorly actually runs, not a generic hosting checklist.

### Cloudways (DigitalOcean), current standard for new and migrated sites

- **Isolation model:** one Application per site, each with its own system user and PHP-FPM pool. A compromise on one app does not, by itself, reach the others on the same server. The shared blast radius is server-level: the master SSH/root login and the Cloudways platform account itself.
- **Containment:**
  - If the Cloudways platform login is suspected compromised (not just one app), revoke platform-level access first, this affects every site on every server under that account, not just the one that alerted.
  - Each app's own SSH/SFTP credentials can be reset independently from the Cloudways dashboard without touching other apps on the same server.
  - Server-wide PHP settings (including `disable_functions`) apply to every app on that server. A hardening change made for one app's incident may need to be checked against every other app on the same box.
  - Varnish sits in front of the app. A code-level fix will not visibly take effect until Varnish is purged from the Cloudways dashboard; there's no CLI purge from the app-level SSH user.
- **Recovery:** rebuild by cloning the repo and running `composer install` against its committed lock file rather than trusting the live filesystem as-is (see the Bedrock section below), re-import a known-clean database dump, and rotate every secret in `.env` (DB credentials, WP salts, API keys) rather than carrying old values forward.

### cPanel or other shared hosting (legacy, being migrated off)

- **Multiple unrelated domains commonly live on one shared hosting account.** A compromised cPanel/WHM login is not scoped to a single site; treat every domain on that account as in-scope until each is individually ruled out. This is exactly how a compromise has spread before: one shared, unrotated, no-2FA cPanel login was the root cause of a multi-domain compromise across four separate sites on a single account.
- **Containment:**
  - Change the cPanel/WHM account password immediately. On this class of host it's usually the single point of control for every site on the account, not per-site.
  - Terminate any active FTP/SSH sessions the panel exposes.
  - SSH access is frequently unavailable on shared hosting. When it is, `.htaccess`-level restriction (deny all, or an IP allowlist on wp-admin) is often the fastest way to restrict one site without touching the others sharing the account.
  - Forensic file collection without SSH means going through FTP or the host's File Manager, and asking the host for raw access/error logs directly. Budget more time for this than for a Cloudways-hosted site.
- **The standing fix, not a same-day one:** migrate the affected site(s) off shared hosting entirely. This has been Offshorly's actual response to this exact scenario before, moving to Cloudways, one Application per site, so this class of shared blast radius doesn't recur.

### Vultr + RunCloud

- Same isolation shape as Cloudways: RunCloud manages a per-app system user on a Vultr droplet. Apply the same logic as the Cloudways section above (per-app credential reset without affecting other apps, server-wide settings apply to every app on the box, revoke the RunCloud panel login itself if that's what's compromised rather than only the one app).

### Kinsta and other managed WordPress hosts (personal-use accounts)

- These are typically personal-use credentials under the Security Policy's ownership model (owned by the individual employee, not Ivy/company-critical), so containment starts with that employee's own MFA and session revocation on the platform account itself, before falling back to the host's support.
- Managed hosts usually ship their own malware scanning and one-click restore points. Use the platform's own restore point as the first recovery option before a manual file-level cleanup; it's faster and more reliable than reconstructing the site by hand.

### Bedrock (Composer-managed) WordPress vs. vanilla WordPress

- **Bedrock (Roots.io) sites:** WordPress core, plugins, and theme are dependency-managed via `composer.json`/`composer.lock`, the doc root is `web/`, and secrets live in `.env`. Verify a suspected compromise by rebuilding from `composer install` against the committed lock file and diffing the result against the live filesystem, rather than trusting whatever is currently deployed. Before trusting that diff, confirm `.gitignore` doesn't exclude `composer.lock` itself (see Runbook C step 3); if it does, the lock file was never authoritative and the diff will pass cleanly regardless of what's actually live.
- **Vanilla WordPress (non-Bedrock):** plugins and themes are committed directly into git, doc root is the repo root, no composer layer. Before trusting a pure git-based redeploy or rebuild here, audit the live server directly for anything installed by hand outside git (security plugins, WAF rules, custom login-URL plugins, 2FA). A previous git-based redeploy on this kind of setup went live with none of those, because they were never tracked in the repo to begin with.
- Either stack: treat a raw `.sql` database dump found committed in git history, or sitting in a web-served uploads/export directory, as an exposure to close, per Runbook C.

### Shared codebase networks

- Some clients run several sites on one shared codebase or theme. A vulnerability, malware injection, or fix that applies to one site generally applies to every site on that same codebase. Treat every site on the shared codebase as in scope for triage the moment one of them alerts, and roll the fix out across all of them together rather than patching one and leaving the rest exposed until they alert independently too.

### D. Phishing Report

**Trigger:** an employee reports a suspicious message, or reports having clicked a link, opened an attachment, or entered credentials.

1. **If credentials or an MFA code were entered:** treat immediately as an Account or Credential Compromise (Runbook A). Don't wait for further review.
2. **If a message was only received, not actioned:** the Security Officer reviews it and, if it looks like a targeted or company-wide campaign, warns other staff with the sender/domain pattern via the company chat app.
3. **If an attachment was opened:** treat the device as potentially compromised. Run an AV/EDR scan, and consider the session-rotation steps from Runbook B as a precaution if anything suspicious follows.
4. Log the report even when no action was needed. A pattern of reports for the same campaign is what tells you it's targeted phishing (higher severity) rather than generic spam.

### E. Account Access Recovery (Offshorly Email / Google Workspace, and Zoho)

**Trigger:** an employee can't sign in: forgotten password, lost or stolen device, lost authenticator, or a suspected compromise discovered while trying to recover access. This runbook is the detailed, tool-specific procedure that Runbooks A and B point to for the actual Google Workspace and Zoho mechanics. On its own (a plain forgotten password, nothing lost or suspicious), this is a Low severity, routine request. As soon as a device is lost/stolen or compromise is suspected, this runs alongside Runbook A and/or B, at their severity, not this one's.

**Standing readiness, not just recovery:** 2FA is required org-wide; every employee should keep two sign-in methods active (an authenticator app and SMS), check anytime via Google Account → Security → How you sign in to Google. Backup codes are issued at onboarding and should be saved somewhere safe, away from the sign-in device itself, since they're what makes self-recovery possible without waiting on an admin.

**A recovery request is a known social engineering vector.** Never verify identity from the requesting channel alone (an email claiming to be someone locked out is exactly what an attacker would send); confirm via a second channel (video call, a known personal number, or the person's Team Lead) before any reset action.

**Employee side, work through in order:**
1. Report it immediately through any working channel (personal email, chat, SMS, or via your Team Lead), naming the affected account and what happened. Don't wait to confirm.
2. Identify what was lost: password only, device, authenticator, or suspected compromise, since that determines the recovery path.
3. Locate the backup codes issued at onboarding. Having them means self-recovery; not having them means HR/Admin handles it.
4. Expect identity verification before any reset happens, no exceptions.
5. Complete the recovery, either self-service with a backup code or an admin-driven reset of the password, sign-in sessions, and backup codes.
6. Set a new, strong, never-reused password.
7. Re-secure the account: re-add the authenticator app, confirm the SMS number (Google), or reconfigure OneAuth and set a new passphrase (Zoho).
8. Save the new backup codes somewhere safe, away from the sign-in device.
9. Test sign-in on both Google and Zoho.
10. Confirm with Infosec that access is fully restored and the account is secured.

**Admin/Infosec side, five phases:**

*Phase 1, verify and contain (first 30 minutes):*
1. Verify identity through a second channel, per the social-engineering note above. Never act on the requesting email alone.
2. Classify the case: forgotten password only / lost or stolen device / lost authenticator / suspected compromise. This sets how aggressive containment needs to be.
3. If a device is lost or stolen, or compromise is suspected, contain before any reset: on Google, reset sign-in cookies and OAuth tokens from the Admin console; on Zoho, close all active sessions from the Admin Panel.

*Phase 2, recover Google Workspace:*
4. Reset the password from the Admin console and require a change at next sign-in (this also revokes OAuth access/refresh tokens on most connected apps).
5. Generate fresh backup codes (Admin console → user → Security → 2-step verification) and deliver them through a verified channel only.
6. Remove all Google App Passwords the user had. A password reset does not always invalidate these.
7. Remove the lost device from the account; update the phone number and 2FA sign-in methods.
8. Unsuspend the account, then walk the employee through re-enrollment: new authenticator, confirmed SMS number, new backup codes saved.

*Phase 3, recover Zoho:*
9. Use the Admin Panel (Zoho One / Zoho Directory) to reset the user's password or MFA. This depends on the org's email domain staying verified in Zoho, don't let that verification lapse.
10. Prefer self-recovery through the employee's OneAuth passphrase or backup verification codes before doing an admin-side MFA reset.
11. If there's no passphrase, no backup codes, and admin reset fails, escalate to Zoho Support. Infosec owns this contact; employees should not raise tickets with Zoho directly.
12. Once recovered, the employee reconfigures OneAuth, sets a new passphrase, and generates a fresh set of backup codes.

*Phase 4, sweep for persistence (run this for every lost or stolen device, not only a confirmed compromise):* a password reset alone doesn't undo everything an attacker may have set up, and these are the most common way access is quietly regained.
13. Check Gmail settings for forwarding rules, filters, send-as addresses, and mailbox delegation; remove anything the employee doesn't recognize.
14. Review third-party app (OAuth) access on both Google and Zoho; revoke anything unknown or unused.
15. Review sign-in and audit logs for unusual locations, times, or admin actions around the incident window, and check recent Drive sharing changes.

*Phase 5, close out:*
16. Confirm with the employee: sign-in works on both Google and Zoho, and two sign-in methods are active again.
17. Log the incident (date, employee, cause, actions taken, who handled it) in the Infosec register.
18. Escalate to management if there's any indication company or client data was accessed or exposed.

**Quick reference**

| If you... | Then... |
|---|---|
| Forgot your email password, or need backup codes | Contact HR/Admin |
| Lost a device or authenticator | Report immediately via the standard reporting channel |
| Forgot your Zoho password | Contact HR/Admin |
| Can't complete Zoho MFA (OneAuth) | Try your passphrase, then backup codes, then escalate to HR/Admin or the Security Officer |
| Suspect your account is compromised | Report immediately via the standard reporting channel |

Never share backup codes, passphrases, or one-time codes with anyone. Identity is always verified before any reset.

---

## Post-Incident Review

For every Critical or High severity incident, once it's closed:

- The Security Officer runs a short, blameless review with whoever was involved: what happened, what worked, what should change.
- Anything that should become a permanent policy change goes through the normal rolling-release process (Security Policy + CHANGELOG), not as an ad hoc edit mid-incident.
- Anything that's a systemic prevention gap (tooling, device management, alert triage automation) gets logged against the Security Roadmap's prevention workstream rather than being solved in the moment under incident pressure.

---

## Plan Maintenance

- Reviewed on the same monthly cycle as the Security Policy.
- Changes are recorded in the shared [CHANGELOG](CHANGELOG.md).
- Runbooks are added incrementally as new incident types come up; this version covers the scenarios Offshorly has actually encountered or is actively monitoring for.

---

## Open Items

These are real, acknowledged gaps rather than things this plan pretends to have solved:

- **No legal or regulatory breach-notification process exists yet.** No legal counsel has been engaged and SOC 2 is not yet in place. Until this is defined, any suspected exposure of client data is escalated to Nicole (and the CEO, if warranted) before any external communication goes out, rather than following a fixed notification procedure.
- **No automated alert-triage tooling across monitored sites yet.** The process in Runbook C step 1 is manual and per-site. Options have been scoped (Gmail filters, a lightweight automation tool with a tracking sheet, or eventually a SIEM) but nothing is built yet.
- **Device management is self-attested only.** There is no centrally enforced remote wipe/lock capability; Runbook B step 4 depends on what each employee has personally configured.
- **The shared dev-tools account (held by Ali, Ivy, and John) is still an open decision**, not yet split into individual logins or otherwise tightened. Until it's resolved, an incident involving any one of those three devices means treating that shared account as in-scope for Runbook A, not just the individual's personal credentials.
- **Reporting channel needs reconciling across documents.** This plan and the Security Policy both name a company-chat DM to the Security Officer and Management as the primary channel. A separate Account Access Recovery guide (written by HR) names `infosecadmin@offshorly.com` as the channel for lost devices, lost authenticators, and suspected compromise, and inconsistently elsewhere in that same guide, `infosec@offshorly.com`. Runbook E above was normalized to point at the existing standard channel rather than either address, but the underlying question (is there an actual monitored shared inbox, and is it meant to replace or supplement the chat DM) is still open.
- **MFA enforcement status is inconsistent between documents.** The Security Policy states centralized MFA management is "under review." The Account Access Recovery guide states 2FA is already enforced organization-wide across Google Workspace and Zoho. One of these is stale; needs confirming which, and updating the other.
- **Onboarding/offboarding redesign needs to preserve existing security requirements.** HR's roadmap includes standardizing onboarding ("Onboarding 2.0," moving fully to Zoho) and formalizing offboarding. Whatever that redesign lands on needs to keep: official-email enforcement, personal device baseline confirmation, and security training at onboarding (Security Policy §1, Device and Endpoint Security, Security Training); and the Vault credential-ownership transfer to Ivy before access revocation at offboarding (Security Policy §5). Nothing in the roadmap conflicts with these, they're just not mentioned in it yet.

---

## Compliance and Enforcement

Same as the Security Policy: violations may result in disciplinary action up to and including termination of employment or contracts. The Security Officer is responsible for maintaining this plan and running the incidents it covers; Management (Nicole) is responsible for business-level decisions during an incident and for resourcing gaps this plan surfaces.
