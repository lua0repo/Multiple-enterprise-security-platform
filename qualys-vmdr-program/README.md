# Vulnerability Management (Qualys VMDR)

End-to-end VMDR program design: asset tagging strategy, scanning cadence, risk-based prioritization, policy compliance, and remediation tracking.

> Part of my [security portfolio](https://www.linkedin.com/in/lua-cybersecurity-prof/) · See [Services page](https://www.linkedin.com/services/page/1943b0320a0418661a)

## Contents

- `asset-tagging/` — tagging taxonomy (business unit, criticality, environment, owner)
- `scanning-cadence/` — scan schedule design by asset tier (internet-facing vs internal vs dev)
- `risk-prioritization/` — QDS/CVSS + business-context scoring model
- `policy-compliance/` — sample policy compliance control mapping
- `remediation-tracking/` — SLA framework by severity, escalation matrix
- `executive-reporting/` — sample exec dashboard structure (risk trend, SLA compliance, top offenders)

## Methodology Overview

**Risk-based prioritization model**
1. Base severity (CVSS/QDS)
2. Asset criticality weighting (crown jewels vs low-value)
3. Exploit availability / active-exploitation intel
4. Exposure (internet-facing vs internal)
5. Compensating controls already in place

**Remediation SLA framework (example)**
| Severity | Internet-Facing | Internal |
|---|---|---|
| Critical | 72 hours | 7 days |
| High | 7 days | 14 days |
| Medium | 30 days | 45 days |
| Low | 90 days | Next cycle |

## Sample Artifacts (to add)
- [ ] Sample executive vulnerability report (anonymized data)
- [ ] Asset tagging taxonomy doc
- [ ] False-positive management workflow
- [ ] Patch verification checklist

## Note
No client scan data, asset inventories, or environment specifics are included — all figures are illustrative.
