# SOC Operations & SIEM Management

Configuration standards, rule-tuning methodology, and alert-optimization frameworks used to run enterprise SIEM operations across Splunk, Microsoft Sentinel, and ArcSight.

> Part of my [security portfolio](../PROFILE-README.md) · See [Services page](https://www.linkedin.com/services/page)

## Contents

- `splunk/` — SPL search examples, dashboard layout patterns, rule-tuning checklist
- `microsoft-sentinel/` — KQL query examples, analytics rule design, workbook structure
- `arcsight/` — correlation rule logic, use-case design notes
- `alert-optimization/` — false-positive reduction methodology, tuning cadence, alert-fatigue metrics
- `log-correlation/` — cross-source correlation examples (auth logs + EDR + firewall)

## Methodology Overview

**Rule Tuning Lifecycle**
1. Baseline noise volume per rule/use case
2. Identify false-positive drivers (source, asset class, time pattern)
3. Apply suppression logic or enrichment (asset context, threat intel)
4. Re-baseline and measure alert-to-incident conversion rate
5. Document and version the change

**Sample metrics tracked**
- Alert volume by severity/source
- False-positive rate (target trend, not absolute — varies by environment)
- Detection coverage against MITRE ATT&CK tactics
- Mean time from alert to triage decision

## Sample Artifacts (to add)
- [ ] 5–8 annotated SPL/KQL queries with purpose + expected output + false-positive notes
- [ ] One before/after tuning case study (anonymized)
- [ ] SIEM use-case design template (blank, reusable)

## Note
All examples use synthetic or publicly documented log samples. No proprietary configurations, client names, or internal architecture are represented.
