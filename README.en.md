# BR App & AI Safety Review Skill

An open-standard **Agent Skill** (`SKILL.md`) for security, privacy (**LGPD**), and AI safety triage—as well as defensive implementation guardrails—tailored to Brazilian applications, with specialized depth for healthcare/EHR (*prontuário eletrônico*) systems, PostgreSQL/Supabase Row Level Security (RLS), Edge Functions, and cloud architectures.

## Overview

Brazilian applications—especially those handling medical and personal data—face unique technical and regulatory intersections:
- **LGPD (Lei nº 13.709/2018):** Strict separation between ordinary personal data and sensitive health data (Art. 5, II & Art. 11), purpose limitation, data minimization, and auditable access trails.
- **Sectoral Healthcare Retention vs. Deletion:** Balancing LGPD data-subject deletion requests against mandatory 20-year medical record retention under **Lei nº 13.787/2018** and **CFM** regulations (enforcing logical archiving and access restriction instead of physical hard-deletes).
- **Supabase / Cloud Perimeter Security:** Preventing common multi-tenant IDOR bugs, `anon` PostgREST table exposures, unsafe `SECURITY DEFINER` RPCs, `service_role` key leaks in client bundles, and unauthenticated brute-force attacks on public verification portals.
- **Brazilian Domain Invariants:** Server-side `CPF`/`CNPJ`/`CRM` validation, Brazilian phone normalization, multi-timezone handling (`America/Sao_Paulo` vs. other Brazilian timezones), and ICP-Brasil digital signature workflows.
- **Application-Level AI Safety:** Protecting clinical free text and RAG pipelines against prompt injection, cross-tenant context leakage, and unauthorized tool execution.

## Operating Modes

1. **Review & Triage Mode (Default):** When reviewing PRs, SQL migrations, Edge Functions, or system architectures, the agent maps trust boundaries and produces an evidence-backed report separating **Severity**, **Confidence**, and **Status** (`Confirmed`, `Suspected`, `Requires investigation`).
2. **Defensive Implementation Mode:** When generating new migrations, RLS policies, RPCs, or backend endpoints, the agent applies least-privilege, tenant isolation, and privacy-by-design controls proactively.

## Folder Structure

```text
.agents/skills/ai-safety-guard-skill-br-app/
├── SKILL.md                        # Core Agent Skill (universal across coding agents)
├── SKILL.pt-BR.md                  # Portuguese translation of the core skill
├── README.md                       # English documentation (this file)
├── README.pt-BR.md                 # Portuguese documentation
└── references/
    └── selara-architecture.md      # Optional project-specific schema & error contracts
```

> **Tip for Reusability:** `SKILL.md` is intentionally framework- and project-agnostic so you can reuse it across any Brazilian app. Keep project-specific table names, custom error codes, and endpoint rules inside `references/` so they load cleanly without polluting the core skill.

## Installation & Setup

### 1. Copy into your repository
Place the skill folder in `.agents/skills/` (the cross-agent open standard location):

```bash
mkdir -p .agents/skills/ai-safety-guard-skill-br-app/references
```

### 2. Enable auto-discovery for Claude Code (and other tools)
Symlink `.claude/skills/` to `.agents/skills/` so Claude Code, Cursor, GitHub Copilot, OpenAI Codex, and Gemini CLI all share the exact same skill definition:

```bash
mkdir -p .claude
ln -s ../.agents/skills/ai-safety-guard-skill-br-app .claude/skills/ai-safety-guard-skill-br-app
```

### 3. Reference in `AGENTS.md` / `CLAUDE.md` (Optional)
Add a short directive to your repository root `AGENTS.md` or `CLAUDE.md` to ensure every agent invokes the skill before touching sensitive layers:

```markdown
## Security, Privacy & Database Guardrails
Before creating or reviewing SQL migrations, RLS policies, storage buckets, Edge Functions, or AI/patient data flows, apply `.agents/skills/ai-safety-guard-skill-br-app/SKILL.md` along with any project-specific rules in `.agents/skills/ai-safety-guard-skill-br-app/references/`.
```

## Usage Examples

* **Code / PR Review:**
  > *"Review `supabase/migrations/` and `supabase/functions/` using `ai-safety-guard-skill-br-app`."*
* **Defensive Feature Implementation:**
  > *"Create a new Edge Function for exporting patient records, following `ai-safety-guard-skill-br-app` guardrails."*
* **AI / RAG Pipeline Audit:**
  > *"Audit how we build the LLM prompt context for clinical notes using `ai-safety-guard-skill-br-app`."*

## Scope & Limitations

This skill performs **initial technical security and privacy triage**. It does not replace formal legal/DPO review under the LGPD, clinical safety validation by medical specialists, penetration testing, or comprehensive security audits.


## Languages

* Portuguese: [README.md](README.md) and [SKILL.md](SKILL.md)
* English: [README.en.md](README.en.md) and [SKILL.en.md](SKILL.en.md)
