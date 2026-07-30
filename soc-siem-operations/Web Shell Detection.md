# Web Shell Detection

## Severity
**Default: High** — a confirmed web shell means the attacker has remote command execution on a server, typically internet-facing. Treat as high severity even before full scope is known.

## MITRE ATT&CK Mapping
- **T1505.003** — Server Software Component: Web Shell
- Often follows exploitation of a public-facing application:
  - **T1190** — Exploit Public-Facing Application
- Frequently paired with:
  - **T1059** — Command and Scripting Interpreter (commands executed through the shell)
  - **T1105** — Ingress Tool Transfer (additional tools staged via the shell)

## Detection Logic
**Trigger conditions:**
- New or modified script file (`.php`, `.aspx`, `.jsp`, etc.) appearing in a web-accessible directory outside of normal deployment activity
- Web server process (IIS, Apache, nginx worker) spawning a child process it normally shouldn't (`w3wp.exe` or `httpd` spawning `cmd.exe`/`powershell.exe`/`bash`) — this is one of the highest-fidelity web shell indicators
- Web request patterns showing unusual POST requests to newly-created or rarely-accessed script files
- File integrity monitoring alert on the web root directory
- Outbound connections initiated by the web server process to unexpected destinations

**Common data sources:**
- EDR process telemetry (web server spawning shell/script interpreter is the strongest signal)
- Web server access logs (unusual URIs, POST-heavy request patterns to single files)
- File integrity monitoring / file creation events in web-accessible paths
- WAF logs if deployed in front of the application

## Investigation Steps

1. **Confirm via process lineage first** — a web server process spawning a command shell is close to definitive evidence of a web shell; don't wait for file analysis to begin containment planning.
2. **Locate the shell file** — identify the exact file path. Check file creation/modification timestamp to establish a timeline.
3. **Review the shell's capability** — most web shells (China Chopper, common PHP/ASPX variants) provide file management, command execution, and sometimes database access. Assume full capability unless the specific variant is identified and confirmed more limited.
4. **Determine the initial access vector** — how did the file get there? Check for:
   - Exploited vulnerability in the web application (check for known CVEs against the app/framework/plugin versions in use)
   - Compromised admin/deployment credentials used to upload the file directly
   - Vulnerable file upload functionality
5. **Review command history through the shell** — web server access logs showing POST requests to the shell file, correlated with process creation events, reconstruct what commands the attacker ran.
6. **Check for lateral movement** — did the attacker use the web shell's command execution to pivot further into the internal network?
7. **Check for additional shells** — attackers frequently drop multiple shells for redundancy. Search the entire web root and any writable directories for similar patterns, not just the one that triggered the alert.

## Containment
- Isolate the server from the network (internet-facing servers should be pulled from public access immediately)
- Do not simply delete the shell file yet — preserve it for analysis before remediation
- Block the source IP(s) that interacted with the shell at the WAF/firewall

## Eradication
- Remove all identified web shell files (search comprehensively, not just the triggering file)
- Patch the vulnerability or close the access method that allowed the initial upload
- Rotate any credentials that were accessible from the compromised server (application secrets, database credentials, service account tokens)
- Review and remove any additional persistence the attacker established via command execution (scheduled tasks, new user accounts, SSH keys)

## Recovery
- Rebuild the server from a known-clean image/deployment rather than trusting manual cleanup, given the difficulty of confirming full eradication
- Redeploy the application only after the vulnerability is patched and confirmed closed
- Monitor closely for 30 days post-incident — web shell incidents frequently see re-exploitation attempts against the same vulnerability

## Lessons Learned
- Was the vulnerability that allowed initial upload a known, patchable CVE? If so, this reflects a patch management gap.
- Is file integrity monitoring deployed on all internet-facing web roots? This is frequently the fastest detection path for web shells.
- Should the web server process be restricted from spawning command interpreters at all (application allowlisting / least-privilege service accounts)?
