# Vulnerability Assessment & Management — Two Attack Surfaces, One Engagement

An 8-stage vulnerability assessment conducted end-to-end in an isolated home lab: web application testing, network scanning, a phishing simulation, SIEM deployment with Windows log forensics, and Android mobile application security testing. Completed as a capstone project for the Google Cybersecurity Professional Certificate, presented live to a final review panel.

This repo documents the methodology, findings, and remediation recommendations from each stage — written up the way a real engagement report would be, with the lab credentials, IPs, and any personally identifying details from the phishing test redacted or replaced with placeholders.

## Why this project

Most "vulnerable app" writeups stop at "ran the tool, got the finding." The goal here was the step most tutorials skip: once a tool flags something, dig until you find the actual root cause, and prove exploitability rather than just asserting it. Two examples that run through this repo:

- A SIEM agent showing zero real-time file-integrity events wasn't declared broken — it was diffed against a working config until the one missing attribute causing it was found (see [04-05](docs/04-05-siem-log-analysis.md)).
- An Android PIN screen wasn't just "probably bypassable" — it was bypassed live, with a working exploit chain, and the leaked credentials displayed on screen (see [06](docs/06-mobile-app-security-diva.md)).

## Lab environment

Everything below ran in an isolated VirtualBox host-only network (no real production systems, no real external targets):

| Host | Role | Tooling |
|---|---|---|
| Kali Linux | Attack box | Burp Suite, Nmap, GoPhish |
| Windows 11 client | Target endpoint | Wazuh agent, FIM |
| Windows Server 2025 | Target server | Wazuh agent, FIM, WinRM (only exposed service) |
| Ubuntu Server | SIEM | Wazuh manager, indexer, dashboard |
| Genymotion (Android API 26) | Mobile test target | DIVA (Damn Insecure and Vulnerable App) |

Static mobile analysis used jadx and apktool; dynamic analysis used drozer, acting as an unprivileged third-party app would.

## Results at a glance

| Stage | Area | Headline result | Severity |
|---|---|---|---|
| 1 | Web application testing (PortSwigger) | 5/5 practitioner labs solved — 2FA logic, broken access control, XSS→CSRF bypass, SSRF filter bypass, blind SQLi | — |
| 2 | Network vulnerability scan | Clean network perimeter — no legacy SMB exposure, signing enforced, minimal exposed ports | Low |
| 3 | Phishing simulation | Full funnel completed end-to-end (click → credential submission) for the one recipient whose email was actually delivered | — |
| 4–5 | SIEM deployment & Windows log forensics | Both hosts score **under 30%** against their CIS benchmark; process-auditing and FIM gaps root-caused to specific config/policy omissions | High |
| 6 | Mobile app security (DIVA) | 6 confirmed findings — including a **Critical** PIN bypass via a trusted Intent extra and a **Critical** SQL injection via an unprotected content provider | Critical |

## Contents

1. [Web Application Testing — PortSwigger Labs](docs/01-portswigger-web-labs.md)
2. [Network Vulnerability Scan — Nmap](docs/02-network-vulnerability-scan.md)
3. [Phishing Simulation — GoPhish](docs/03-phishing-simulation.md)
4. [SIEM Deployment & Windows Log Forensics — Wazuh](docs/04-05-siem-log-analysis.md)
5. [Mobile Application Security — DIVA](docs/06-mobile-app-security-diva.md)
6. [Consolidated Findings & Recommendations](docs/07-consolidated-findings-recommendations.md)

## Skills demonstrated

Vulnerability scanning and manual web exploitation (Burp Suite, PortSwigger methodology) · phishing campaign design and metrics (GoPhish) · SIEM deployment and detection validation (Wazuh) · Windows log analysis and audit-policy troubleshooting (`auditpol`, Event IDs) · CIS benchmark / SCA compliance review · static and dynamic Android analysis (jadx, apktool, drozer) · report writing and live panel defense.

## Note on data in this repo

Lab network ranges, VM hostnames, and tool output are from a private, isolated lab and are not security-sensitive. Anything that could identify a real person (phishing test recipients) or any literal credential value has been redacted or replaced with a placeholder — the methodology and findings are preserved exactly; only the specific sensitive strings are not reproduced here.
