---
name: ai-safety-guard-skill-br-app
description: Performs initial technical triage for security, personal data handling (Brazil context), and AI safety across database code, authentication, edge functions, and AI workflows. Use when asked to review security, auth, guards, edge functions, databases, privacy/PII, or AI safety in applications.
---

# BR App & AI Safety Review

## Summary

Helps perform an initial code review of applications targeting Brazil across database privacy, authentication and edge function guards, and application AI safety boundaries, with calibrated confidence and clear verification steps.

## When to Use

* Initial review of queries, access policies, or handling of Brazilian users' personal data.
* Inspection of authentication, authorization, and guards in edge functions or backend handlers.
* Evaluation of workflows that accept user input, retrieve context, or invoke AI models.
* Early-stage code risk triage for developers or reviewers building foundational security and AI safety habits.

## Steps

1. **Map the boundary under review:** identify untrusted inputs, authentication/guard logic, database access, and AI model interactions.
2. **Review database and personal data (Brazil focus):**
   * Check whether queries or responses return personal data (such as email, phone, IDs/documents, or other users' history) beyond what is needed.
   * Verify whether user or tenant isolation applies before reading, updating, or deleting records.
   * Check whether sensitive data or free-text user content enters logs, caches, or AI payloads without clear necessity.
3. **Review auth and edge function guards:**
   * Check whether identity and authorization are verified before querying data, mutating state, or calling external services.
   * Verify that privileged keys stay server-side and are used only after permission checks.
   * Look for basic abuse controls (high-frequency retries, oversized payloads, or unauthenticated access on expensive routes).
4. **Review the AI safety boundary:**
   * Check whether user inputs and external data are treated as untrusted data rather than system instructions.
   * Check whether context assembled for the model could leak other users' data or internal information.
   * Verify whether model outputs that trigger actions, queries, or state changes have validation and bounds.
5. **Report findings with calibrated confidence:**
   * Structure each item around: **Evidence in snippet**, **Potential risk**, **Severity / Confidence (High / Medium / Low or Needs check)**, and **What to verify in the rest of the codebase**.

## Gotchas

* **Do not treat missing visible checks as confirmed vulnerabilities:** guards, middleware, or policies may exist in another file. State the conditional risk and ask to check the full context.
* **Avoid generic silver bullets:** explain the underlying principle at risk (such as missing ownership checks before data access) and propose a minimal, readable fix.
* **Do not give legal advice or claim complete security:** keep the output scoped to initial technical triage that complements human review and testing.
