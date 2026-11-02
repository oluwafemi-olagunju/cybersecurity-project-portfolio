# Stage 3 — Phishing Simulation (GoPhish)

## Objective

Run a controlled phishing simulation against a small set of consenting test recipients to measure susceptibility to a realistic social-engineering pretext, using an outbound sandbox so no real mail infrastructure was touched.

## Methodology

1. Built a "Phishing Simulation — IT Support Test" campaign in GoPhish: a fake IT Support login page as the landing page, routed through a Mailtrap sandbox SMTP relay (so no email ever reached a real inbox outside the test).
2. Launched the campaign against 3 consenting test recipients.
3. Measured the funnel directly from GoPhish's exported results and raw-events CSVs — not from the dashboard summary alone, since (as it turned out) the summary view glossed over a delivery failure that the raw data exposed.

## Findings

| Metric | Result |
|---|---|
| Recipients targeted | 3 |
| Emails actually delivered | 1 of 3 — the other 2 failed with a sandbox rate-limit error (`550 5.7.0 Too many emails per second`), a free-tier sandbox limitation, not a security finding |
| Link clicks (delivered recipient) | 4, over ~7 minutes |
| Credentials submitted | Yes — full funnel completed from click through credential entry on the fake landing page |
| Report/flag action taken by recipient | None |
| Open-pixel tracking | Recorded 0 opens despite confirmed clicks and submission — a known tracking-pixel-blocking artifact, not evidence of no engagement |

## A correction worth keeping in the writeup

An earlier pass at this report summarized the campaign as "all recipients engaged," based on GoPhish's dashboard roll-up. Going back to the actual exported CSVs surfaced the real picture: only 1 of 3 emails was ever delivered, so the effective sample size for susceptibility measurement is **n = 1**, not 3. The corrected report states this plainly rather than overstating the sample. This is the same discipline as the SIEM stage: check the raw data, don't trust the summary view.

## Recommendations

- For any future simulation, use a production-grade SMTP relay or a paid sandbox tier sized to the campaign — the rate limit here cut the usable sample from 3 to 1.
- Don't rely on open-tracking pixels as a standalone engagement signal; cross-check against click and submission events, since pixel blocking is common and will silently undercount.
- For the one completed funnel: the fact that a single click led to repeated re-clicks over several minutes before submission is consistent with a hesitant, security-aware user second-guessing the page — worth noting as a soft signal in any individual follow-up/training conversation, separate from the pass/fail metric.
