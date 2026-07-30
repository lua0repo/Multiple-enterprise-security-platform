# Identity & Access Management (Okta)

Configuration approach, MFA policy design, and access-governance recommendations.

> Part of my [security portfolio](../PROFILE-README.md) · See [Services page](https://www.linkedin.com/services/page)

## Contents

- `mfa-policy-design/` — adaptive MFA policy tiers by risk (device trust, geo, network zone)
- `access-governance/` — access review cadence, least-privilege recertification process
- `sso-app-integration/` — SAML/OIDC integration checklist
- `lifecycle-management/` — joiner/mover/leaver automation design notes

## MFA Policy Tiering (example model)
| Risk Signal | Policy Action |
|---|---|
| Known device + trusted network | Standard MFA |
| New device | Step-up MFA + device registration |
| Impossible travel / anomalous geo | Block + verification challenge |
| Privileged app access | Always step-up MFA, shorter session |

## Access Governance Cadence
- Quarterly access recertification for standard apps
- Monthly recertification for privileged/admin roles
- Automated deprovisioning tied to HRIS termination trigger
- Orphaned-account detection sweep (accounts with no recent auth)

## Sample Artifacts (to add)
- [ ] MFA policy tiering diagram
- [ ] Access review template (spreadsheet or Notion export)
- [ ] Joiner/mover/leaver flow diagram

## Note
Illustrative policy design only — no tenant configurations, real app integrations, or client identity data.
