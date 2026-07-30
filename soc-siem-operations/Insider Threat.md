# Insider Threat

## Severity
**Default: High** — insider threat investigations start high due to legal, HR, and reputational sensitivity, and are handled with stricter access controls on the investigation itself (need-to-know basis, coordination with HR/Legal before action).

## MITRE ATT&CK Mapping
- **T1005** — Data from Local System
- **T1213** — Data from Information Repositories
- **T1567** — Exfiltration Over Web Service (cloud storage, personal email, messaging apps)
- **T1052** — Exfiltration Over Physical Medium (USB)
- **T1020** — Automated Exfiltration (bulk download/sync tools)

## Detection Logic
**Trigger conditions:**
- Anomalous data access volume compared to the user's historical baseline (large or repeated downloads from file shares, SharePoint, or databases outside normal role activity)
- Access to data/systems outside the user's normal job function or the departments they don't work with
- Bulk download or mass file access shortly before a resignation date or after a termination notice
- Use of USB mass storage devices on systems where policy restricts it, especially with large data transfer volume
- Uploads to personal cloud storage (Google Drive, Dropbox, personal OneDrive) or personal email from a corporate endpoint
- Access attempts after standard working hours inconsistent with the employee's normal pattern, especially combined with data access

**Common data sources:**
- DLP (Data Loss Prevention) alerts
- CASB (Cloud Access Security Broker) logs for cloud upload activity
- File server / SharePoint audit logs
- Endpoint USB device logs
- HR system integration for employment status changes (resignation, termination, PIP) — critical context for risk scoring

## Investigation Steps

1. **Verify legal/HR involvement before proceeding** — insider threat investigations typically require coordination with HR and Legal from the outset due to employment law and privacy considerations. Confirm the organization's insider threat investigation policy is being followed.
2. **Establish the baseline** — what does normal data access look like for this user's role? Compare current activity against their own historical pattern and their peer group's pattern.
3. **Timeline the activity** — build a chronological view of data access, downloads, and any transfer events. Correlate against HR context (resignation date, performance issues, role change) if available and authorized for SOC visibility.
4. **Identify what data was accessed** — classify the sensitivity (PII, financial, source code, customer data, trade secrets). This drives both severity and notification obligations.
5. **Determine the destination** — where did the data go? Personal cloud storage, personal email, USB device, or an external party via messaging/email? Each has different forensic and legal implications.
6. **Check for anti-forensic behavior** — log clearing, use of privacy/cleaning tools, or attempts to disable monitoring agents suggest intentional concealment rather than incidental policy violation.
7. **Assess intent indicators cautiously** — avoid speculation. Document only observed technical behavior; intent determination is typically an HR/Legal/investigative function, not a SOC analyst's call.

## Containment
- Coordinate with HR/Legal on timing before restricting access — premature action can tip off the individual or create legal exposure if not properly authorized
- When authorized: disable remote access, revoke VPN/cloud credentials, and suspend account access
- Preserve the endpoint (forensic image) rather than continuing to let the user operate on it once investigation is authorized to proceed to this stage
- Revoke access to specific sensitive repositories if broader account suspension isn't yet authorized

## Eradication
- This phase looks different than a technical intrusion — "eradication" here means closing the access pathway (revoking permissions, disabling accounts) and preserving evidence for HR/Legal/law enforcement as applicable
- Review and tighten DLP policies or access controls that allowed the bulk access/exfiltration to occur

## Recovery
- Coordinate account/device return-to-service (if the employee remains employed) with HR guidance
- Reassess data access permissions organization-wide if the incident reveals overly broad access grants (a common root cause — the user had access to data unrelated to their role)

## Lessons Learned
- Did the user have access beyond what their role required (least-privilege violation)? This is the most common contributing factor in insider incidents.
- Was DLP/CASB coverage adequate to catch this pattern, or did detection rely on manual review/tip-off?
- Review offboarding procedures — was access revoked promptly relative to the resignation/termination date, or did a gap exist that extended the exposure window?
- Update access review cadence for sensitive data repositories based on findings.
