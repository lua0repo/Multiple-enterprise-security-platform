# Endpoint & Network Security Configuration

Platform deployment, policy design, and hardening guidance across the endpoint and network security stack.

> Part of my [security portfolio](../PROFILE-README.md) · See [Services page](https://www.linkedin.com/services/page)

## Contents (one folder per platform)

- `crowdstrike/` — policy tiering, detection/prevention policy design principles
- `trend-micro-vision-one/` — deployment best practices, XDR correlation notes
- `trellix-hx/` — endpoint policy design
- `palo-alto/` — firewall rule hygiene, Zero Trust segmentation notes
- `fortinet/` — FortiGate baseline hardening checklist
- `check-point/` — policy layer design principles
- `zscaler/` — SSE/SASE architecture notes
- `cato-networks/` — SASE deployment reference architecture

## Reference Architectures (diagrams)
- Standard SOC data flow: Internet → Firewall → EDR → SIEM → SOAR → Ticketing
- Zero Trust segmentation model
- Remote workforce security (SASE-based)
- Cloud security baseline (AWS/Azure)

## Hardening Checklist Template (per platform)
- Baseline policy tier (default-deny posture)
- Logging/telemetry forwarding confirmed to SIEM
- Exception/allowlist governance process
- Review cadence (quarterly policy audit)
- Change management tie-in

## Sample Artifacts (to add)
- [ ] 3–4 architecture diagrams (draw.io/Visio export as PNG or SVG)
- [ ] One hardening checklist per platform (start with CrowdStrike + Fortinet)

## Note
General best-practice guidance only — no proprietary policy exports, client configurations, or internal network diagrams.
