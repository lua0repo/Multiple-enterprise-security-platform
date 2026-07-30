# SOC Team Leadership & Analyst Training

Escalation playbooks, performance frameworks, and cross-functional training programs used to reduce incident response time and strengthen SOC/SIEM operations.

> Part of my [security portfolio](../PROFILE-README.md) · See [Services page](https://www.linkedin.com/services/page)

## Contents

- `escalation-playbooks/` — tiered escalation paths (Tier 1 → Tier 2 → IR Lead → Leadership)
- `performance-frameworks/` — analyst KPI/scorecard design
- `training-programs/` — onboarding curriculum outline, tabletop exercise templates
- `soc-metrics/` — MTTD, MTTR, alert volume, SLA compliance dashboard structure

## Escalation Framework (example)
| Tier | Owns | Escalates When |
|---|---|---|
| Tier 1 Analyst | Initial triage, known-pattern alerts | Unknown TTP, confirmed malicious, VIP asset involved |
| Tier 2 Analyst | Investigation, containment recommendation | Multi-system impact, active exfiltration suspected |
| IR Lead | Coordination, stakeholder comms | Business-critical outage, legal/regulatory exposure |
| Leadership | Business decision, external comms | Breach disclosure threshold met |

## Analyst Performance Scorecard (dimensions)
- Triage accuracy (true positive identification rate)
- Time to first response
- Documentation quality
- Escalation judgment (appropriate vs premature/late)
- Detection improvement contributions (rules/playbooks authored)

## SOC Metrics Dashboard (sections)
- Mean Time to Detect (MTTD) / Mean Time to Respond (MTTR)
- Alert volume by source and severity
- False positive rate trend
- Analyst workload distribution
- SLA compliance by severity tier

## Sample Artifacts (to add)
- [ ] Analyst onboarding curriculum (first 30/60/90 days)
- [ ] Tabletop exercise scenario (1 full writeup)
- [ ] Sample SOC metrics dashboard (Excel/Power BI mockup with synthetic data)

## Note
Frameworks are generalized management methodology — no real performance data, employee information, or internal team structures are represented.
