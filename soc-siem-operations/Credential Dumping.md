# Credential Dumping

## Severity
**Default: Critical** — any confirmed access to LSASS memory, SAM database, or NTDS.dit is treated as critical from first detection. Credential dumping is almost always a precursor to lateral movement or ransomware deployment.

## MITRE ATT&CK Mapping
- **T1003** — OS Credential Dumping
  - T1003.001 — LSASS Memory
  - T1003.002 — Security Account Manager (SAM)
  - T1003.003 — NTDS (domain controller credential database)
  - T1003.006 — DCSync

## Detection Logic
**Trigger conditions:**
- A process (especially a non-standard one) opening a handle to `lsass.exe` with memory-read access rights
- Known credential dumping tool signatures/behavior (Mimikatz and similar patterns, even when renamed — EDR behavioral detection over static signature is more reliable here)
- `comsvcs.dll` MiniDump technique invoked against LSASS (a common "living off the land" dumping method)
- Volume Shadow Copy creation followed by access to `NTDS.dit` or the `SAM`/`SYSTEM` registry hives
- Abnormal replication requests to a domain controller from a non-DC host (DCSync pattern)

**Common data sources:**
- EDR process access/handle telemetry (this is the most reliable source — command-line logging alone often misses in-memory techniques)
- Windows Event Log 4656/4663 (object access) with LSASS-specific auditing
- Domain controller security logs for replication events (Event ID 4662 with specific GUIDs for DCSync detection)

## Investigation Steps

1. **Confirm the access, not just the attempt** — did the process successfully read LSASS memory, or was it blocked (e.g., by Credential Guard or EDR prevention)? This changes urgency but not priority — even a blocked attempt indicates active attacker presence.
2. **Identify the accessing process** — is it a known/legitimate tool (some EDR and backup software legitimately touch LSASS) or unrecognized/renamed?
3. **Check process lineage** — what spawned the dumping tool? Trace back to the initial access point.
4. **Determine what credentials were exposed** — if dumping succeeded, assume every credential cached in memory on that host is compromised, including any admin or service account that had logged in previously (not just the current user).
5. **Check for DCSync activity** — if a domain controller is involved, review for replication requests from unexpected hosts, which indicates domain-wide credential theft, not just single-host exposure.
6. **Look for exfiltration of the dump** — was the output file (if written to disk) transferred off the host?

## Containment
- Isolate the affected endpoint immediately via EDR
- Assume domain-wide compromise if DCSync or NTDS.dit access is confirmed — this requires immediate escalation to incident commander/leadership, not standard SOC-tier containment
- Disable any accounts confirmed or suspected to have been cached in memory on the affected host

## Eradication
- **Full credential rotation** for every account that had an active session on the compromised host in recent history — this is non-negotiable for confirmed LSASS access
- If domain-wide compromise is confirmed (NTDS.dit/DCSync), this typically requires a full domain credential reset (krbtgt account reset twice, per Microsoft guidance, plus all privileged account passwords)
- Remove the dumping tool and any persistence mechanisms on the host

## Recovery
- Reimage the affected endpoint — do not attempt in-place remediation for confirmed credential dumping
- Re-enable rotated accounts only after confirming no further suspicious activity
- For domain-wide incidents, recovery should follow a formal incident response plan with executive/legal involvement, not standard SOC recovery steps

## Lessons Learned
- Is Credential Guard enabled on high-value endpoints (servers, admin workstations, domain controllers)? This is the primary technical control that prevents most LSASS-based dumping.
- Was the initial access vector that led to this host identified and closed?
- Review privileged account hygiene — was a domain admin account logged into a lower-tier endpoint, expanding the blast radius unnecessarily? (Tiered administration model violation.)
