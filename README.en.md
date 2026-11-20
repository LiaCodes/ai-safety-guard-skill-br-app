# BR App & AI Safety Review Skill

An AI agent skill for initial technical triage of **database and personal data handling (Brazil context)**, **authentication and edge function guards**, and **application-level AI safety boundaries**.

## Motivation & Context

This project started as a study and portfolio artifact motivated by helping review the technical foundation of an application aimed at the Brazilian market, combining interests in **cybersecurity**, **machine learning**, and **AI safety**.

Because I am early in my hands-on journey across these areas, the skill is designed to **reduce false certainty**, avoid inflated claims, and guide targeted human verification.

## What This Skill Covers

* Signals of over-exposure of personal data, user/tenant isolation, and sending sensitive user data into AI workflows or logs.
* Validation order, privileged key usage, and basic abuse protections in authentication and edge functions/guards.
* Application AI safety boundaries, including separating instructions from untrusted input, context leakage, and guardrails on model-triggered actions or outputs.
* Structured findings separating observed code evidence, potential risk, and what needs manual verification.

## Important Limitations

* **Does not replace** thorough security reviews, penetration testing, full architecture reviews, or legal/compliance advice (such as formal LGPD compliance).
* Missing a visible guard in a single snippet **does not prove** a vulnerability exists in production; enforcement may live elsewhere in the codebase.

## How to Use

1. Add `SKILL.md` to your agent's skills directory (or provide it as workflow guidance when reviewing code snippets).
2. Share database code, auth/edge function guards, or AI integration flows for an initial review.
3. Verify findings against the broader codebase before making changes.


## Languages

* Portuguese: [README.md](README.md) and [SKILL.md](SKILL.md)
* English: [README.en.md](README.en.md) and [SKILL.en.md](SKILL.en.md)
