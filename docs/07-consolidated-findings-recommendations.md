# Consolidated Findings & Recommendations

## The cross-cutting takeaway

Every finding in this engagement traces back to trusting something that should have been verified — at two different layers:

| Layer | What the gap looks like |
|---|---|
| OS / Log layer | A missing config attribute, an unaudited subcategory, an unenforced lockout policy — gaps in what gets checked and recorded |
| Application layer | A caller-supplied Intent extra, an unsanitized query parameter, a logged exception — the same trust failure, baked directly into code |

The claim isn't that these are the same finding twice — it's that they're the same *class* of failure recurring at two different layers. At the OS/log layer, the gap is about what gets checked and recorded: things that should have been verified as configured correctly, and weren't. At the application layer, the identical pattern is baked directly into code: trusting an Intent extra, trusting an unsanitized query parameter, trusting that user input reaching a log statement is harmless.

**The actionable lesson:** verification has to happen at the point of trust, not be assumed from a prior step — whether that step is a config file, a caller's Intent, or a database query.

## Consolidated prioritized recommendations

| Priority | Area | Control |
|---|---|---|
| 1 | Mobile — Access Control | Never let a security decision depend on caller-supplied data; verify server-side or re-check state internally |
| 1 | Mobile — SQL Injection | Disable export on components by default; enforce signature permissions; use parameterized queries |
| 1 | Infra — WinRM hardening | Disable Digest auth, restrict Remote Shell Access, disallow RunAs credential storage on the server |
| 2 | Infra — Audit coverage | Enable Windows Firewall connection logging, Process Creation auditing, and PowerShell Transcription logging on both hosts |
| 2 | Mobile — Data handling | Encrypt sensitive stored fields; stop logging sensitive fields entirely |
| 3 | Infra — Account policy | Implement an account lockout policy; enforce password history |
| 3 | Mobile — Build hygiene | Remove hardcoded secrets; disable debuggable builds via CI enforcement |
| 4 | Infra — Compliance | Build a phased plan against the broader 300+ control CIS compliance gap (both hosts score under 30%) rather than attempting a full pass at once |

## Methodology theme across stages

A pattern worth naming explicitly: the strongest findings in this project didn't come from running a scanner and reading its output. They came from treating a scanner's silence or a gap in results as a question, not an answer — diffing a broken config against a working one (FIM), running the actual Windows audit-policy command instead of guessing (process visibility), going back to raw exported data instead of trusting a dashboard summary (phishing), and reproducing an exploit live instead of asserting it from source code (mobile access control and SQLi). That discipline — validate, then dig until the actual root cause is found — is the method this whole project was built to demonstrate.

## Engagement scope

This repo covers 6 of the 8 project stages in detail (environment setup and the two "already covered elsewhere" writeups are summarized in the main [README](../README.md)). The two stages chosen for live defense in the final panel — SIEM deployment/Windows log forensics and DIVA mobile application security — were selected as the strongest, most defensible investigative work from the full engagement.
