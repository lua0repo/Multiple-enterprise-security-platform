# Ransomware Investigation

## Severity
**Default: Critical** — ransomware is treated as critical from the first credible indicator, before full confirmation. Do not wait for encryption to complete to escalate.

## MITRE ATT&CK Mapping
- **T1486** — Data Encrypted for Impact
- **T1490** — Inhibit System Recovery (shadow copy deletion, backup targeting)
- **T1489** — Service Stop (stopping security/backup services pre-encryption)
- **T1027** — Obfuscated Files or Information (packed/encrypted payloads)
- Often preceded by: **T1078** (Valid Accounts), **T1021** (Remote Services — lateral movement), **T1547** (Boot or Logon Autostart Execution — persistence)

## Detection Logic
**Trigger conditions:**
- EDR/AV detection of known ransomware family signature or behavioral detection (mass file modification, rapid file renaming with new extensions)
- Shadow copy deletion command execution (`vssadmin delete shadows`, `wmic shadowcopy delete`)
- Sudden spike in file write/rename operations on a file server or endpoint within a short window
- Ransom note file creation across multiple directories
- Backup software or security service being stopped/disabled shortly before mass encryption activity

**Common data sources:**
- EDR telemetry (process execution, file system activity)
- File server audit logs (mass rename/modify events)
- SIEM correlation across shadow copy deletion + service stop + file encryption in sequence

## Investigation Steps

1. **Do not wait for full confirmation to act** — isolate first, investigate in parallel. Every minute of delay increases encrypted scope.
2. **Identify patient zero** — trace back from the first encryption/detection event to the initial access vector (phishing, exposed RDP, exploited vulnerability, compromised credentials).
3. **Determine the ransomware family** — file extension patterns, ransom note content, and known IOCs can often identify the variant, which informs whether a public decryptor exists.
4. **Map lateral movement** — pull authentication logs and EDR process trees to determine which systems the attacker touched before triggering encryption. Ransomware deployment is usually the final stage of a longer intrusion.
5. **Check backup integrity** — confirm whether backups were also targeted (many ransomware operators specifically hunt and delete/encrypt backup repositories before triggering the main payload).
6. **Assess data exfiltration** — many modern ransomware operations exfiltrate data before encrypting (double extortion). Check egress traffic logs for large outbound transfers in the hours/days prior to encryption.
7. **Determine scope** — how many endpoints/servers are affected? Is the spread contained or still active?

## Containment
- Isolate all affected endpoints from the network immediately (network isolation via EDR, not just disconnecting — attacker may have remote access tools)
- Disable affected user/service accounts identified in the lateral movement chain
- Block identified C2 infrastructure at the firewall/DNS level
- Segment or isolate backup infrastructure if not already separated from production network
- Do not power off encrypted machines unless instructed by forensics — memory may contain decryption keys or evidence

## Eradication
- Remove all identified persistence mechanisms across every touched system, not just the encrypted ones
- Rotate every credential the attacker had access to, including service accounts and any credentials cached on compromised systems
- Patch or remediate the confirmed initial access vector
- Full reimage of confirmed-compromised endpoints rather than in-place cleanup

## Recovery
- Restore from backups confirmed to be clean and predating the intrusion (not just predating encryption — the attacker may have had access for weeks before deploying ransomware)
- Bring systems back online in stages with heightened monitoring, not all at once
- Coordinate with legal/compliance on breach notification obligations if data exfiltration is confirmed or suspected

## Lessons Learned
- What was the initial access vector, and is that gap now closed?
- How long was the attacker in the environment before deploying ransomware (dwell time)? This is a key metric for detection program maturity.
- Were backups isolated/immutable, or were they reachable and therefore a target?
- Update the incident response runbook and tabletop exercise scenarios based on what was learned during this incident.
