# Brute Force Investigation

## Severity
**Default: Medium** — escalates to **High** if:
- Source succeeds after repeated failures (indicates credential validity)
- Target account has privileged/admin access
- Activity originates from a known-malicious IP or anonymization service (VPN/Tor/proxy)
- Multiple accounts targeted from the same source (spray pattern, not single-account brute force)

## MITRE ATT&CK Mapping
- **T1110** — Brute Force
  - T1110.001 — Password Guessing
  - T1110.003 — Password Spraying
  - T1110.004 — Credential Stuffing

## Detection Logic
**Trigger conditions:**
- N+ failed authentication attempts against a single account within a defined window (baseline: 5+ failures in 5 minutes; tune per environment)
- N+ failed attempts against multiple accounts from a single source IP within a short window (spray pattern)
- Failed attempts followed immediately by a successful login from the same source

**Common data sources:**
- Windows Security Event Log (4625 failed logon, 4624 successful logon)
- Okta / IdP authentication logs
- VPN concentrator logs
- Application/API authentication logs (M365, cloud consoles)

**SIEM logic pattern:**
```
Count failed_logon events
WHERE source_ip = X
GROUP BY target_account, source_ip
HAVING count > threshold WITHIN 5m window
```

## Investigation Steps

1. **Confirm the pattern** — pull failed/success ratio for the source IP and target account(s) over the last 24 hours. Distinguish brute force from a user who genuinely forgot their password (isolated failures, no spray pattern).
2. **Identify source** — geolocate the source IP. Cross-reference against known corporate IP ranges, VPN exit nodes, and threat intel feeds for known-malicious infrastructure.
3. **Check for success** — did any attempt from this source succeed? If yes, treat as a confirmed compromise, not just an attempted one — jump to containment immediately.
4. **Scope the blast radius** — is this one account or a spray across many? Pull a list of every account targeted from the source in the last 24–72 hours.
5. **Check account risk tier** — is any targeted account privileged (domain admin, service account, finance/exec)? This changes severity and urgency.
6. **Review MFA status** — was MFA enforced on the targeted account(s)? A successful password guess against an MFA-protected account is lower urgency than against one without MFA.
7. **Check for post-auth activity** — if a login succeeded, pull subsequent activity for that session (new logon locations, privilege changes, mailbox rule creation, data access).

## Containment
- Force password reset on any account with a successful login from the source
- Block the source IP at the firewall/WAF/IdP level
- If MFA was bypassed or not present, enforce step-up MFA on the affected account immediately
- For spray patterns, consider temporary account lockout policy tightening account-wide

## Eradication
- Confirm no persistence was established during a successful-login window (new mailbox rules, OAuth app grants, added MFA devices, new inbox forwarding rules)
- Rotate any credentials that may have been reused elsewhere if the account owner confirms password reuse
- Revoke active sessions/tokens for the compromised account, not just the password

## Recovery
- Re-enable the account only after password reset + MFA re-enrollment
- Monitor the account for 7–14 days post-incident for anomalous activity
- Confirm the account owner has changed the password on any other services where it was reused

## Lessons Learned
- Was the alert threshold appropriate, or did it fire too late (attacker already succeeded) or too often (analyst fatigue)?
- Was MFA enforced on this account? If not, this is a control gap to escalate, not just an incident to close.
- Should this source IP/ASN be added to a standing blocklist or geo-fencing policy?
- Document time-to-detect and time-to-contain for the SOC metrics dashboard.
