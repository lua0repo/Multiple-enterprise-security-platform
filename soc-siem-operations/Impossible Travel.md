# Impossible Travel

## Severity
**Default: Medium** — escalates to **High** if:
- The account is privileged
- The second location is a known high-risk geography or anonymization service
- Post-login activity shows data access, mailbox rule changes, or privilege escalation
- The pattern repeats across multiple accounts (indicates credential compromise at scale, e.g., from a breach dump)

## MITRE ATT&CK Mapping
- **T1078** — Valid Accounts (use of compromised credentials)
- **T1078.004** — Cloud Accounts specifically, if detected via IdP/cloud login telemetry

## Detection Logic
**Trigger conditions:**
- Two successful logins from the same account, from geographically distant locations, within a time window that makes physical travel impossible (e.g., login from Manila and login from Frankfurt 40 minutes apart)
- Most IdPs (Okta, Azure AD/Entra) have this as a built-in risk detection — validate the underlying calculation rather than trusting the label blindly

**Common data sources:**
- IdP sign-in logs (Okta System Log, Entra ID sign-in logs)
- VPN logs (to rule out the user being on a corporate VPN with an unusual exit node)

## Investigation Steps

1. **Rule out false positives first** — this is one of the highest false-positive-rate alerts in most environments. Check:
   - Is the user on a corporate VPN or using a proxy/CDN that changes apparent geolocation?
   - Is this a shared/service account with legitimate multi-location access?
   - Did the user recently travel (check calendar/travel records if available)?
   - Is this a mobile carrier IP that resolved to an inaccurate geolocation (common false-positive source)?
2. **Confirm the timeline** — pull exact timestamps and IP addresses for both logins. Calculate whether the travel time is genuinely impossible given the distance.
3. **Check device/browser fingerprint** — did both logins come from the same device/browser, or different ones? Different devices from impossible locations is a stronger compromise indicator than the same device (which suggests VPN/proxy behavior).
4. **Review MFA status on both logins** — was MFA satisfied on both, one, or neither? A login that bypassed MFA (e.g., via legacy protocol or MFA fatigue) is higher priority.
5. **Check post-login activity** — for the suspicious location's session, review what the account did: email access, file downloads, mailbox rule creation, admin actions.
6. **Check for concurrent/overlapping sessions** — are both sessions still active simultaneously?

## Containment
- If compromise is suspected, revoke all active sessions and force password reset immediately
- Require re-authentication with step-up MFA
- Block the suspicious source IP if it doesn't correspond to a legitimate service

## Eradication
- Review and remove any mailbox rules, forwarding rules, or OAuth grants created during the suspicious session
- Check for any new MFA device enrollment during the suspicious session — attackers often add their own MFA method to maintain persistence
- Confirm no lateral use of the compromised account (access to shared drives, other systems using SSO)

## Recovery
- Re-enable the account after password reset and MFA re-verification
- Consider enrolling the account in conditional access policies (e.g., block legacy auth, require compliant device) if not already in place
- Monitor for 7–14 days

## Lessons Learned
- Was this a true positive or a tuning issue (VPN exit nodes, mobile carrier geolocation inaccuracy)? Feed this back into the detection rule to reduce future false positives.
- If true positive: how were credentials obtained (phishing, credential stuffing from a breach dump, password reuse)?
- Is conditional access / risk-based authentication policy tuned appropriately for this account's risk tier?
