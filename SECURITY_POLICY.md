# Offshorly Security Policy
**Version:** 0.2 | **Status:** Draft | **Review Cycle:** Monthly | **Date:** August 20, 2025  

---

## Purpose

This document establishes Offshorly’s **Rolling Release Security Policy**, along with baseline security practices. It defines responsibilities, acceptable behaviors, and standards to protect company data, systems, and personnel from unauthorized access, disclosure, alteration, and destruction.

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

### 3. GitHub Repository Access
- Access to company GitHub/GitLab repositories is only permitted through accounts registered under Offshorly email addresses.  
- Repository access must be limited to authorized personnel only.  
- Repository access must be audited monthly to remove inactive or former employees. 
- The "dev@offshorly.com" email must be added to the repository for auditing.

### 4. Change Logging and Access Auditing
- All changes to sensitive systems must be logged and subject to audit.  
- Monthly audits must be performed to review access to Vault, third-party apps, and repositories.  

### 5. Principle of Least Privilege
- Employees are granted the minimum level of access necessary to perform their responsibilities.  
- Access rights are reviewed during onboarding, offboarding, and the rolling review cycle.  

### 6. Monthly Policy Review and Updates
- This policy will be updated at least once every 30 days.  
- Changes may include revised procedures, added requirements, or new security controls.  
- A changelog will be maintained, and employees must acknowledge new versions.  

---

## Basic Security Policy

### Authentication and Access Control
- Multi-factor authentication (MFA) is mandatory on all sensitive systems.  
- Centralized MFA management is under review.  
- Passwords must be at least 12 characters in length and stored securely using the company’s password manager.  
- Quarterly access reviews must be conducted to enforce compliance.  

### Incident Response
- Any suspicious activity, including phishing attempts, potential breaches, or unauthorized access, must be reported within 24 hours.  
- Guidelines for recognizing phishing attempts must be followed.
    - **Verify sender identity**: Check the sender’s email address carefully for misspellings or unusual domains.  
    - **Hover before you click**: Hover over links to confirm the actual destination before clicking.  
    - **Inspect attachments**: Do not open unexpected attachments; confirm legitimacy with the sender first.  
    - **Check for urgency or scare tactics**: Be cautious of emails demanding immediate action or threatening consequences.  
    - **Look for inconsistencies**: Poor grammar, misspellings, or unusual formatting can indicate a phishing attempt.  
    - **Do not provide sensitive data**: Never share credentials, MFA codes, or financial details via email, chat, or unknown links.  
    - **Report suspicious messages immediately**: Report suspicious emails or activity to the Security Officer or Management.
- Offshorly maintains an Incident Response Plan outlining escalation procedures and responsibilities.  

### Data Classification and Handling
- Data must be categorized as Public, Internal, Confidential, or Restricted.  
- Confidential and Restricted data must be encrypted and shared only with authorized individuals, using secure channels.  

### Device and Endpoint Security
- All company devices must be encrypted and use strong authentication (password and/or biometrics).  
- Operating systems, security patches, and antivirus software must remain up to date.  
- Employees are encouraged to isolate their work environment using **dual-boot setups** or **virtual machines (VMs)**, depending on hardware capabilities:  

  **When to use dual-boot:**  
  - Recommended for employees with laptops that have at least **1 TB of storage**.  
    - Partition the disk (e.g., 200–300 GB for the work OS) and install a dedicated operating system for Offshorly work.  
  - If the laptop has **500 GB or less of storage**:  
    - If an additional SSD slot exists, request a company-approved SSD and configure dual boot with the new drive.  
    - If no additional slot is available, request a replacement SSD with larger storage (e.g., 1 TB) and partition it for dual boot.  

  **When to use virtual machines (VMs):**  
  - Recommended for employees with limited storage or those who prefer not to dual boot.  
  - Allocate at least **100 GB of disk space** and **8 GB of RAM** to the VM.  
  - Use company-approved VM software (e.g., VirtualBox, VMware, or Parallels for macOS).  

- Employees must keep work environments strictly separate from personal environments to minimize the risk of data leakage, malware exposure, or accidental credential sharing.  

### Use of Software and Network Access
- Only Management-approved software is permitted on company devices.  
- Employees must use VPNs when accessing Offshorly resources on unsecured or public networks.  
- A custom WireGuard VPN solution is being considered for company-wide use.  

### Security Training and Awareness
- Security training is mandatory upon onboarding and repeated every six months.  
- All personnel must confirm understanding of this policy and stay current with all rolling release updates.  

---

## Compliance and Enforcement

Violations of this policy may result in disciplinary action, up to and including termination of employment or contracts. The Security Officer is responsible for managing compliance, performing audits, and overseeing all rolling updates.
