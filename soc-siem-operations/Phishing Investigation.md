# Phishing Investigation

## Severity
**Default: Medium** — escalates to **High/Critical** if:
- User confirms they clicked the link and/or entered credentials
- The email targeted a privileged user (finance, exec, IT admin)
- The campaign was sent to multiple users (mass phishing vs. targeted spear-phish)
- Malicious attachment was opened and executed (confirmed via EDR)

## MITRE ATT&CK Mapping
- **T1566** — Phishing
  - T1566.001 — Spearphishing Attachment
  - T1566.002 — Spearphishing Link
- **T1204** — User Execution (if attachment/link led to execution)
- **T1078** — Valid Accounts (if credentials were harvested and reused)

## Detection Logic
**Trigger conditions:**
- User-reported phishing (most common initial trigger)
- Email gateway/secure email gateway (SEG) flag on sender reputation, link reputation, or attachment sandbox detonation
- Anomalous mail flow patterns (bulk send from a single external sender to multiple internal recipients within a short window)
- Post-delivery detection: a previously-clean link is later flagged malicious (retroactive detonation)

**Common data sources:**
- Email security gateway logs (M365 Defender, Proofpoint, Mimecast)
- User-submitted phishing reports (Report Phishing button telemetry)
- URL/attachment sandbox detonation results
- Proxy/DNS logs for outbound connections to the link domain

## Investigation Steps

1. **Preserve the sample** — pull the full email with headers (do not forward as a reply, which can strip headers). Extract sender, reply-to, SPF/DKIM/DMARC results, and originating IP.
2. **Analyze the payload** — if there's a link, check reputation via sandbox/URL analysis. If there's an attachment, submit to detonation sandbox rather than opening locally.
3. **Determine delivery scope** — search mail logs for the same sender/subject/attachment hash across the entire organization. Phishing is rarely sent to one person.
4. **Identify recipients who interacted** — check email gateway click-tracking and proxy logs for any user who visited the link or downloaded the attachment.
5. **Check for credential entry** — if the link led to a credential-harvesting page, determine whether any user submitted credentials (proxy logs showing POST to the malicious domain is a strong indicator).
6. **Check for execution** — for attachment-based phishing, check EDR for process execution tied to the attachment (e.g., Office spawning PowerShell/cmd — a classic macro-payload pattern).
7. **Interview the reporting user** — confirm what they clicked, whether they entered credentials, and whether they noticed anything unusual afterward.

## Containment
- Block the sender domain/IP and malicious URL at the email gateway and web proxy
- Purge the email from all mailboxes org-wide (not just the reporting user's)
- Force password reset + session revocation for any user who entered credentials
- Isolate the endpoint via EDR for any user where the attachment executed

## Eradication
- Confirm no persistence mechanism was dropped (scheduled task, registry run key, new service) on affected endpoints
- For compromised accounts, check for mailbox rule creation, OAuth app consent grants, and forwarding rules — common phishing follow-on actions
- Remove any malicious OAuth application grants discovered during the account review

## Recovery
- Re-image or fully remediate any endpoint with confirmed malware execution rather than trusting partial cleanup
- Re-enable accounts only after password reset, MFA re-enrollment, and session token revocation
- Monitor affected accounts and endpoints for 14 days post-incident

## Lessons Learned
- Did the email gateway catch this, or did it rely entirely on user reporting? If the latter, flag a detection gap.
- Was this campaign targeted (spear-phishing) or opportunistic? Targeted campaigns warrant a broader review of what information the attacker may have used for pretexting.
- Update security awareness training with this specific lure pattern if it's novel.
- Track click-through rate and report rate for this campaign as an input to the next training cycle.
