---
name: ai-safety-guard-skill-br-app
description: Performs initial technical triage of application security, personal data handling in the Brazilian context, authentication, authorization, database access, edge functions, and AI safety boundaries. Use when reviewing application code, APIs, databases, privacy, PII, or AI workflows.
---

# BR App & AI Safety Review

## Summary

An AI agent skill for initial technical triage of applications targeting Brazil, covering database security, personal data handling, authentication and authorization, edge function guards, and application-level AI safety.

The skill emphasizes evidence-based findings, calibrated confidence, clear limitations, and targeted human verification.

## When to Use

- Initial review of database queries, access policies, and handling of Brazilian users' personal data.
- Inspection of authentication, authorization, APIs, edge functions, and backend handlers.
- Evaluation of workflows involving user input, retrieval-augmented generation (RAG), or AI model integrations.
- Early-stage security risk triage for developers and reviewers.
- Identification of technical risks potentially relevant to LGPD requirements.

## Review Steps

### 1. Map the Trust Boundaries

Before reviewing individual components:

- Identify trusted and untrusted inputs.
- Map authentication and authorization mechanisms.
- Identify database access paths and external service integrations.
- Identify where personal data enters, moves through, and leaves the application.
- Map interactions between users, application logic, AI models, retrieval systems, and tools.
- Identify actions that can modify data, trigger external services, or affect other users.

Do not assume that a security control is absent simply because it is not visible in the provided snippet.

### 2. Review Database Security and Personal Data

Inspect the following areas when relevant:

**Data exposure and minimization**
- Check whether queries or API responses expose personal data beyond what is necessary.
- Identify potential exposure of emails, phone numbers, government identification numbers, sensitive personal data, or other users' records.
- Check whether sensitive data or free-text user content enters logs, caches, analytics, or AI payloads without clear necessity.

**Access control and isolation**
- Verify whether user or tenant isolation is enforced before reading, updating, or deleting records.
- Inspect Row Level Security (RLS) policies when applicable.
- Identify potential object-level authorization issues, including access to another user's resources through manipulated identifiers.
- Review database functions, views, and stored procedures for unexpected privilege escalation or access-control bypasses.

**Query and storage security**
- Look for unsafe query construction and potential SQL injection.
- Inspect file storage permissions, bucket policies, and access controls when uploaded files are involved.
- Check whether database access follows the intended least-privilege principle.

### 3. Review Authentication and Authorization

- Distinguish authentication (who the user is) from authorization (what the user is allowed to do).
- Check whether identity and permissions are verified before querying data, mutating state, or calling external services.
- Identify missing ownership checks on resources and operations.
- Verify that privileged credentials remain server-side and are not exposed through client bundles, API responses, or logs.
- Check whether privileged credentials bypass database-level security policies, including RLS, and whether explicit authorization checks compensate for that behavior.
- Look for basic abuse protections, including rate limiting, oversized payload restrictions, and unauthenticated access to expensive operations.
- Consider whether authentication and authorization controls are consistently enforced across relevant endpoints.

### 4. Review Application-Level AI Safety

**Input and instruction boundaries**
- Check whether user inputs and external data are treated as untrusted content rather than system instructions.
- Consider direct and indirect prompt injection through user messages, retrieved documents, web content, and external sources.
- Inspect whether retrieved context can contain instructions that conflict with the application's intended behavior.

**Context and data leakage**
- Check whether context assembled for the model could expose other users' data, internal information, credentials, or sensitive application details.
- Review conversation history, retrieval systems, and external integrations for unintended information disclosure.
- Identify unnecessary personal data being sent to external AI providers.

**Model outputs and actions**
- Verify that model outputs are validated before being used in SQL queries, API calls, HTML rendering, or state-changing operations.
- Check whether generated queries and structured outputs are validated and appropriately constrained.
- Verify that model-triggered tools and actions enforce authorization independently of model decisions.
- Identify actions that may require human approval, especially consequential or irreversible operations.
- Check for unsafe output handling, including potential injection through generated content.

### 5. Review Brazilian Privacy Context (LGPD)

Identify technical signals and potential privacy concerns relevant to Brazil's Lei Geral de Proteção de Dados (LGPD).

When applicable:

- Distinguish ordinary personal data from sensitive personal data under the LGPD.
- Identify potential data minimization and purpose limitation concerns.
- Look for indications of excessive or indefinite personal data retention.
- Identify personal data shared with external AI providers, analytics platforms, or other third-party services.
- Flag potential international data transfers for further privacy and legal assessment.
- Identify technical mechanisms relevant to data subject requests, including access, correction, and deletion.
- Consider whether personal data is unnecessarily exposed in logs, monitoring systems, or development environments.

These checks identify technical signals for further assessment. They do not establish legal non-compliance or replace legal advice.

### 6. Report Findings with Calibrated Confidence

For each finding, provide:

- **Title:** A concise description of the potential issue.
- **Evidence in provided code:** The relevant code, configuration, or behavior observed.
- **Potential risk:** What could happen if the suspected issue exists.
- **Severity:** Critical, High, Medium, Low, or Undetermined.
- **Confidence:** High, Medium, or Low, based on the available evidence.
- **Verification status:** Confirmed in provided evidence, suspected, or requires further investigation.
- **Recommended action:** A minimal, practical mitigation or investigation step.
- **What to verify:** Additional files, middleware, configurations, policies, or runtime behavior needed to establish the issue.

Keep severity and confidence separate:

- Severity represents the potential impact of the issue.
- Confidence represents how strongly the available evidence supports the finding.
- Verification status describes what has actually been established.

Do not assign a confirmed vulnerability status solely because a visible code snippet lacks an expected check.

## Gotchas and Limitations

### Avoid False Positives

- Do not treat missing visible checks as confirmed vulnerabilities. Authorization may be enforced through middleware, database policies, or another application layer.
- State conditional risks when the available context is incomplete.
- Request additional context when necessary rather than inventing implementation details.
- Distinguish observed behavior from assumptions and hypotheses.

### Do Not Confuse Inspection with Testing

- Code inspection alone does not prove that a vulnerability is exploitable.
- Do not claim to have executed tests, verified runtime behavior, or performed penetration testing unless those activities actually occurred.
- A reviewed snippet does not establish the security of the complete application.
- State the scope and limitations of the evidence used.

### Avoid Generic Recommendations

- Explain the underlying security principle at risk.
- Prefer minimal, readable, context-appropriate mitigations.
- Avoid recommending unnecessary complexity or controls unrelated to the observed risk.
- Do not assume a particular technology or architecture without evidence.

### Important Limitations

- This skill performs initial technical triage, not comprehensive security audits.
- It does not replace penetration testing, architecture reviews, threat modeling, or specialist security assessments.
- It does not establish formal LGPD compliance or provide legal advice.
- Findings must be verified against the broader codebase and, when necessary, actual application behavior.
- Absence of detected issues does not establish that an application is secure.

## Expected Output

Organize findings by severity, while preserving confidence and verification status.

Conclude with:

1. The main risks identified.
2. Important uncertainties or missing context.
3. The most relevant next verification steps.
4. A clear statement of the review scope and limitations.

Prioritize accuracy, traceability, and responsible uncertainty over the number of findings.
