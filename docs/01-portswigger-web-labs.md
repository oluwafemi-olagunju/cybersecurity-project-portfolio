# Stage 1 — Web Application Testing (PortSwigger Web Security Academy)

## Objective

Solve a set of PortSwigger Practitioner-level labs covering distinct web vulnerability classes, demonstrating manual exploitation technique rather than relying on an automated scanner's output.

## Methodology

1. Worked through each lab using PortSwigger's own lab environment (ephemeral per-session instances), with Burp Suite as the intercepting proxy for request inspection and tampering.
2. For each lab: identified the vulnerable parameter or logic flaw, crafted and sent the exploit through Burp's Repeater/Intruder, and confirmed success against the lab's own "solved" criteria (not just "the request didn't error").
3. Documented each exploit as a step-by-step reproduction so the finding stands on its own, independent of the specific (and temporary) lab instance.

## Findings

| # | Lab | Vulnerability class | What broke |
|---|---|---|---|
| 1 | 2FA broken logic | Authentication | The two-factor step could be completed out of sequence, letting an attacker skip straight to a valid session without ever supplying the second factor. |
| 2 | Method-based access control | Broken access control | An admin-only action was gated only on HTTP method (blocked for GET, not for POST), so switching the request method bypassed the check entirely. |
| 3 | XSS to bypass CSRF defenses | Stored XSS → CSRF | A stored XSS payload was used to execute a state-changing request from within an authenticated victim's own browser session, defeating CSRF tokens because the payload ran as the victim. |
| 4 | SSRF with a blacklist-based filter | Server-side request forgery | The server's SSRF filter blacklisted known-bad hostnames/IPs but not alternate representations of the same address (redirects, decimal/hex IP encoding), so the filter was bypassed to reach an internal-only endpoint. |
| 5 | Blind SQL injection with conditional responses | SQL injection | No query output was reflected in the response, but the application's response *timing/content differed* based on injected boolean conditions — used to extract data (including a database credential) one character at a time via conditional payloads. |

## Why these five

Each lab maps to a different root cause of trust failure: trusting client-reported auth state, trusting HTTP method as an access boundary, trusting stored content as inert, trusting a denylist to be complete, and trusting unsanitized input in a query — the same theme that recurs across the rest of this project (see [07](07-consolidated-findings-recommendations.md)).

## Recommendations

- Enforce authentication state transitions server-side, not by trusting which step the client claims to be on.
- Apply access-control checks uniformly across all HTTP methods for a given endpoint, not per-method.
- Treat all user-controlled stored content as untrusted on output (contextual output encoding), since stored XSS defeats token-based CSRF defenses by executing as the victim.
- Replace SSRF blacklists with allowlists of permitted destinations; blacklists are inherently incomplete against encoding tricks and redirects.
- Use parameterized queries everywhere; blind injection is just as exploitable as reflected injection, only slower to extract.
