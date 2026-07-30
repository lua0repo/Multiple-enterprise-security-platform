# Suspicious PowerShell

## Severity
**Default: Medium** — escalates to **High/Critical** if:
- PowerShell was spawned by Office applications, a browser, or another user-facing app (classic post-exploitation pattern)
- Encoded/obfuscated command content is present
- Network connections follow shortly after execution (download-and-execute pattern)
- Execution occurs on a server, domain controller, or privileged endpoint

## MITRE ATT&CK Mapping
- **T1059.001** — Command and Scripting Interpreter: PowerShell
- Frequently paired with:
  - **T1027** — Obfuscated Files or Information (Base64/encoded commands)
  - **T1105** — Ingress Tool Transfer (download cradles)
  - **T1218** — System Binary Proxy Execution (LOLBins invoking PowerShell)

## Detection Logic
**Trigger conditions:**
- PowerShell execution with `-enc` / `-EncodedCommand` flag
- PowerShell spawned as a child process of winword.exe, excel.exe, outlook.exe, or a browser process
- Download cradle patterns: `IEX (New-Object Net.WebClient).DownloadString(...)`, `Invoke-WebRequest`, `Invoke-Expression` combined with a remote URL
- Execution policy bypass flags (`-ExecutionPolicy Bypass`, `-NoProfile -NonInteractive -WindowStyle Hidden`)
- Unusually long command-line length (often indicates obfuscation/encoding)

**Common data sources:**
- Windows Event Log 4688 (process creation) with command-line auditing enabled
- PowerShell Script Block Logging (Event ID 4104) — critical for seeing decoded/deobfuscated content
- EDR process tree and command-line telemetry

## Investigation Steps

1. **Pull the full command line** — never assess PowerShell alerts from the alert summary alone; retrieve the complete, unabbreviated command line from the raw event.
2. **Decode if encoded** — Base64-decode any `-EncodedCommand` content to see the actual instructions being executed.
3. **Check the parent process** — what spawned PowerShell? A user manually opening PowerShell is very different from Word silently spawning it (indicates macro-based execution).
4. **Check for network activity** — did the script attempt to reach out to an external host? Cross-reference the destination against threat intel and check if it's newly registered or known-malicious.
5. **Check for follow-on process creation** — did PowerShell spawn additional processes (another script, an executable dropped to disk)?
6. **Assess script block logging content** — if enabled, this often reveals deobfuscated intent even when the initial command line was encoded.
7. **Check user context** — was this run under the logged-in user's context, SYSTEM, or a service account? SYSTEM-level PowerShell from an unexpected trigger is a strong escalation indicator.

## Containment
- Isolate the endpoint from the network via EDR if malicious intent is confirmed or strongly suspected
- Kill the PowerShell process and any child processes it spawned
- Block any identified C2/download domains at the network level

## Eradication
- Identify and remove any dropped files, scheduled tasks, registry run keys, or services created by the script
- Check for credential harvesting artifacts if the script touched LSASS or credential stores (see Credential Dumping playbook if applicable)
- Review and remove the initial delivery mechanism (malicious macro, exploited application) if this was part of a larger chain

## Recovery
- Reimage the endpoint if persistence or credential theft is confirmed rather than attempting in-place cleanup
- Reintroduce to the network only after confirmation of a clean state
- Monitor the endpoint and any accounts that were active on it for 14 days

## Lessons Learned
- Is PowerShell Script Block Logging and Module Logging enabled organization-wide? This is the single highest-value logging source for this alert category and is frequently missing.
- Should PowerShell Constrained Language Mode or application allowlisting (e.g., AppLocker/WDAC) be evaluated for high-risk endpoint tiers?
- Was this delivered via a macro-enabled document? If so, review macro execution policy.
