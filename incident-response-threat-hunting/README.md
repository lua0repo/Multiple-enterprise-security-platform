# Incident Response & Threat Hunting

Triage workflow design, escalation process improvement, MITRE ATT&CK-mapped investigations, and incident reporting for leadership.

> Part of my [security portfolio](https://www.linkedin.com/in/lua-cybersecurity-prof/) · See [Services page](https://www.linkedin.com/services/page/1943b0320a0418661a)

## Contents

- `playbooks/` — investigation playbooks (Phishing, Brute Force, Ransomware, Impossible Travel, Suspicious PowerShell, Credential Dumping, Insider Threat)
- `threat-hunts/` — hypothesis-driven hunt writeups
- `incident-reports/` — sample fictional incident reports (full lifecycle)
- `triage-framework/` — severity classification, escalation matrix, SLA targets

## Playbook Template (used for every scenario)
- Severity classification
- MITRE ATT&CK mapping
- Detection logic / trigger source
- Investigation steps
- Containment actions
- Eradication steps
- Recovery steps
- Lessons learned

## Threat Hunt Template
- Hypothesis
- Data sources used
- Query logic (SPL/KQL)
- Findings
- MITRE ATT&CK mapping
- Follow-up detections created

## Sample Incident Report Structure
- Executive Summary
- Timeline
- Business Impact
- Root Cause
- Indicators of Compromise
- MITRE ATT&CK Mapping
- Remediation
- Lessons Learned

## Sample Artifacts (to add)
- [ ] 4–6 completed playbooks (start with Phishing + Ransomware — highest recruiter interest)
- [ ] 2–3 threat hunt writeups
- [ ] 1 full fictional incident report end-to-end

## Note
All scenarios are fictional/composite. No real incident data, client names, or IOCs from actual engagements are included.
