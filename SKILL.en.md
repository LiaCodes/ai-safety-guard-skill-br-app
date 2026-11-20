---
name: ai-safety-guard-skill-br-app
description: Initial security, privacy (LGPD), and AI safety triage — plus defensive implementation guidelines — for applications in Brazil, especially systems handling patient and health data (electronic health records), CPF/CRM identifiers, RLS in Supabase/Postgres, Edge Functions, storage buckets, cloud services, and AI/RAG workflows.
---

# Security and AI Review for Brazilian Applications

## Purpose and scope

Perform initial evidence-based technical triage — or apply defensive guidelines during implementation — for application security, privacy, and AI integration risks in Brazilian systems, with special attention to patient data and medical information.

Review database and storage access control (e.g., RLS in PostgreSQL/Supabase, RPCs, buckets), handling of personal data and sensitive health data, authentication and authorization, APIs and edge functions, Brazilian domain invariants (CPF/CRM, time zones, retention vs. deletion), shared responsibility in the cloud, third-party integrations, and application-level AI security.

**Out of scope:** clinical diagnosis, treatment recommendations, medical decision-making, clinical accuracy, formal LGPD compliance opinion, legal advice, penetration testing (*pentest*), or comprehensive security audit. If AI may influence clinical decisions, flag the need for specialized clinical and regulatory safety evaluation.

## Operating modes

1. **Review and Triage Mode (default when inspecting existing code, PRs, schemas, or architectures):** Follow the 7-step review flow below and structure the output according to the **Findings and report** template.
2. **Defensive Implementation Mode (when asked to generate or modify code, migrations, or APIs):** Proactively apply all controls from sections 1 to 6 before writing the code and record at the end any security assumptions or pending operational checks.

## Review flow

### 1. Map the system and trust boundaries

Before pointing out issues, establish what is known about:

- Users, profiles/roles, *tenants* (clinics, organizations, healthcare professionals, administrative staff, patients), authentication, authorization, and privileged identities (`service_role`, administrative keys, webhooks).
- Data flows: where personal and health data enter, are stored, queried, logged, exported, sent to AI providers, archived, and deleted.
- Databases, APIs, storage buckets, edge functions, backend handlers, external services, AI models, retrieval sources (RAG), and *tools*.
- Trusted versus untrusted inputs, state-changing actions, and operations that affect other users or *tenants*.
- Cloud services and the security responsibilities of the provider, the application owner, and third parties.

Do not infer that a control is missing merely because it does not appear in an isolated code snippet. Request relevant additional context when necessary.

### 2. Review application and database security

- **Authorization and isolation:** verify ownership and *tenant* boundaries before reads, writes, and deletions; inspect RLS policies (`USING` and `WITH CHECK`), composite foreign keys, object-level access checks, `SECURITY DEFINER` functions, views, procedures, and least-privilege permissions (`anon`, `authenticated`, `service_role`). Consider improper access across users, clinics, or organizations (IDOR).
- **Authentication and privileged access:** distinguish identity verification from permission verification; confirm that checks occur before data access, state changes, and external calls. Verify that privileged credentials (`SUPABASE_SERVICE_ROLE_KEY`, signing secrets) remain exclusively on the server — never in frontend bundles (`VITE_*`, `NEXT_PUBLIC_*`) — and evaluate how `service_role` operations enforce *tenant* boundaries when bypassing RLS.
- **Queries and storage:** look for unsafe query construction / SQL injection (hardening `search_path` in SQL functions); review uploaded file permissions, bucket visibility (`public` vs. private), upload paths, signed URL expiration time (TTL), and whether documents inherit the access boundaries of associated records.
- **Abuse resistance:** evaluate *rate limits*, CAPTCHA/Turnstile on public endpoints, brute-force lockout on PINs or short tokens, excessive payloads, unauthenticated expensive operations, and protection consistency across endpoints.
- **Brazilian domain exposure and signals:** check whether APIs, queries, URLs, logs, caches, analytics, debugging tools, exports, or AI payloads unnecessarily expose emails, phone numbers, Brazilian identifiers (`CPF`, `CNPJ`, `CRM`, `CNS`), health records, free text, or credentials. Check time zone handling (e.g., `America/Sao_Paulo` vs. other time zones in Brazil) whenever expiration windows, appointments, or timestamps depend on local time.

### 3. Give additional scrutiny to health data

Under the LGPD (Art. 5, II and Art. 11), data relating to health are sensitive personal data. Check:

- Data classification and least-privilege access separating clinical records from administrative/scheduling data across patients, healthcare professionals, front desk/administration, clinics, and organizations.
- Health record isolation and authorization across all access paths, including post-finalization immutability of clinical progress notes and signed prescriptions/documents.
- Emergency access (*break-glass*): restricted scope, justified, logged, and auditable.
- Tension between retention and deletion: Brazilian health legislation (e.g., Law No. 13,787/2018 and CFM regulations) requires a minimum retention of 20 years for medical records, prevailing over generic deletion requests provided for in Art. 16 of the LGPD. Check whether clinical records are protected against accidental or unauthorized physical deletion (using archiving/access blocking) while data not subject to mandatory retention have an appropriate lifecycle.
- Encryption in transit and at rest, key management responsibilities, backups, restoration, and digital signature integrity (e.g., ICP-Brasil flows, hash verification).
- Unnecessary copies or disclosures in logs, analytics, development environments, exports, attachments, and third parties.
- Whether pseudonymization or anonymization is appropriate; do not assume that pseudonymized data are automatically anonymous.
- Patient data in prompts, model context, retrieval (RAG), outputs, and external AI services; evaluate minimization, access restrictions, retention, and contractual safeguards.
- Whether generated content can reveal information about one patient to another user.

An *enterprise* plan, provider privacy statement, or security certification does not, by itself, prove that the processing performed by the application is secure or appropriate.

### 4. Review application-level AI security

Evaluate safe integration and data protection, not clinical correctness.

- Treat user inputs, clinical free text, retrieved documents, web content, and external data as untrusted; consider direct and indirect *prompt injection* and instruction conflicts.
- Check assembled context, RAG filters, and conversation history for leakage across *tenants* or users, exposure of patient data, internal information, secrets, and unnecessary personal data.
- Validate and restrict model outputs before using them in SQL, APIs, HTML, structured data, or state-changing operations.
- Ensure *tools* and actions enforce authorization independently of model decisions; require human approval for significant or irreversible actions when appropriate.
- Consider unsafe rendering, *output injection*, and unintended use of tools.
- If the AI feature appears to influence diagnosis, treatment, or clinical decisions, stop the clinical safety evaluation and recommend review by specialists.

### 5. Apply the Brazilian privacy context

Identify technical signals relevant to the LGPD; do not issue legal conclusions.

- Distinguish ordinary personal data from sensitive personal data; treat health data as sensitive.
- Consider purpose limitation, data minimization, excessive or indefinite retention (balanced with legal retention obligations in healthcare), sharing with third parties, and international data transfer (e.g., AI APIs or cloud hosted abroad).
- Check technical support for data subject rights (access, correction, anonymization/blocking/deletion when legally applicable), access restrictions, and audit/accountability trails (`actor_id` attribution even in privileged backend operations).
- Identify exposure in logs, monitoring, analytics, and development environments.

Flag questions about legal basis, controller/processor roles, or formal compliance for qualified legal/privacy review.

### 6. Evaluate shared responsibility in the cloud and third parties

For each managed or cloud service, identify provider-managed controls versus customer/application responsibilities. Consult service-specific documentation rather than assuming.

- **Provider:** identify operational and infrastructure controls managed by the provider, as well as relevant documentation or assurances. This does not prove that the application is secure.
- **Application team / customer:** review application logic, IAM, authorization, data classification, database and storage permissions, secrets, encryption configuration, network controls, and logging.
- **Third parties:** map which AI, analytics, identity, digital signature, messaging (e.g., WhatsApp), storage, and other providers process, store, or access application or patient data; inspect flows and permissions across organizations.
- **Accountability:** document undefined controls or those without a clear owner and evidence that needs to be obtained from vendors.

Do not confuse shared responsibility in the cloud with legal roles under the LGPD. A cloud provider is not automatically a processor under the LGPD, nor is the application owner automatically a controller; roles depend on actual processing decisions and contractual arrangements.

### 7. Use the OWASP Top 10:2025 as a review lens

Keep the **OWASP Top 10:2025** in mind when reviewing applicable risks. Use it as a supplementary checklist to broaden coverage and organize relevant observations — not as a substitute for contextual analysis, evidence, threat modeling, or verification. Do not assume all categories apply and do not report a vulnerability merely because an item from the list is not visible in an isolated snippet. For AI-specific risks, also examine the trust boundaries and controls described in this skill.

## Findings and report

For each finding in Review and Triage Mode, include:

1. **Title** — concise description of the issue.
2. **Evidence** — exact code, configuration, or observed behavior supporting the finding.
3. **Potential impact** — what can happen and who is affected.
4. **Severity** — Critical, High, Medium, Low, or Indeterminate.
5. **Confidence** — High, Medium, or Low, according to the quality of the evidence.
6. **Status** — Confirmed in provided evidence, Suspected, or Requires investigation.
7. **Recommended action** — minimal, practical mitigation or next check.
8. **Additional verification** — files, middlewares, policies, configurations, provider documentation, or runtime behavior needed.

Keep these concepts separate:
- **Severity** estimates potential impact.
- **Confidence** describes how strongly the evidence supports the finding.
- **Status** indicates what has actually been proven.

Examples of potential high impact include cross-*tenant* exposure of health records, unauthorized access to medical history, tampering with prescriptions/signed documents, or uncontrolled disclosure of identifiable patient data to external services. Treat these cases only as triage signals: assign severity based on actual exposure, impact, and context, not by fixed rule.

Organize findings by severity, keeping confidence and status explicit. Conclude with top risks, uncertainties or missing context, cloud or third-party responsibilities to verify, prioritized next steps, and the scope/limitations of the review.

## Evidence discipline and limitations

- Separate observed facts from assumptions, hypotheses, and recommendations.
- Do not classify a suspected issue as confirmed merely because a visible snippet does not display a validation; the control may exist in middleware, database policies, or another layer.
- State conditional risks when context is incomplete; request the smallest additional evidence needed.
- Code inspection does not prove exploitability. Never claim to have run tests, verified production behavior, or performed penetration tests without actually having done so.
- An isolated snippet or the absence of findings does not prove that the entire application is secure.
- Prefer specific, minimal recommendations suited to the architecture; do not assume technologies or prescribe unnecessary complexity.
- Consult each provider's specific documentation before assigning cloud responsibilities.
- Technical observations support privacy assessment, but do not establish LGPD compliance or substitute for legal advice.
- This skill is intended only for initial triage; it does not replace threat modeling, architectural review, penetration testing, specialized security assessment, or clinical/regulatory review.
