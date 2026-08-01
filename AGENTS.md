# AGENTS.md

Guidance for AI coding agents working in this repository.

For the Agent Skills specification and general skill-authoring patterns, see [neondatabase/agent-skills](https://github.com/neondatabase/agent-skills) (`AGENTS.md`).

## Repository Overview

Sample code and a companion Agent Skill for the [Neon AI Agent Program](https://neon.com/docs/introduction/agent-plan) — targeting platforms that provision and operate Lakebase Postgres, Object Storage, Functions, Managed Better Auth, and/or AI Gateway access for their users (agent platforms, codegen tools, multi-tenant SaaS).

| Path | Purpose |
| --- | --- |
| `skills/neon-postgres-agent-platforms/` | Companion agent skill (`SKILL.md`, references) |
| `skills/neon-postgres-agent-platforms/scripts/` | Runnable `@neon/sdk` TypeScript samples (separate `package.json`) |

For connection strings, drivers, ORMs, and general Neon app integration, use the [neon-postgres skill](https://github.com/neondatabase/agent-skills) and [Neon docs](https://neon.com/docs) first.

## Terminology

Public-facing copy in this repo follows the Neon naming rules, which match [neondatabase/agent-skills](https://github.com/neondatabase/agent-skills) (`AGENTS.md`):

- **Lakebase Postgres** is the database product. Don't call it "Neon Postgres", "Neon Serverless Postgres", or "Neon" — it is the same database whether reached through Neon or through Databricks.
- **lakebase architecture** (lowercase) is the category: OLTP built directly on cloud object storage, storage decoupled from compute. It replaces "the Neon architecture".
- **Neon** is the brand and the access path, and brand-scope copy uses it alone. Never "Neon and Lakebase Postgres" — that coordinates the brand with one of its own components.

"Platform" needs care in this repo specifically, because it appears constantly and is usually correct: **agent platforms**, a partner's own platform, and the Databricks Platform are all fine. Neon itself is never a platform — it is not separate from the Databricks Platform, so "the Neon platform" cannot be used.

Skill ids, `neon-postgres` cross-references, and Neon API resource names (a **Neon project**, a **Neon org**) are identifiers and stay as they are.

## Validation

Before opening a PR, run the same checks as CI:

```bash
npm ci --ignore-scripts
npm run validate:ci
```

To validate a single skill directly:

```bash
skills-ref validate skills/neon-postgres-agent-platforms
```

## CI/CD

Neon maintains **two** agent-skill repositories with a shared, hardened CI pipeline. Keep them aligned when you change CI/CD in either repo.

| Repo | GitHub | What CI validates |
| --- | --- | --- |
| **agent-skills** | [neondatabase/agent-skills](https://github.com/neondatabase/agent-skills) | Every skill under `skills/` via `skills-ref`, plus Cursor and Claude plugin manifests under `plugins/` |
| **neon-for-agent-platforms** (this repo) | [neondatabase/neon-for-agent-platforms](https://github.com/neondatabase/neon-for-agent-platforms) | Every skill under `skills/` via `skills-ref` |

Shared pipeline shape (both repos):

- Workflow: `.github/workflows/validate.yml` (job name **Validate**)
- Install: `npm ci --ignore-scripts` from `package-lock.json`
- Entry point: `npm run validate:ci`
- Supply chain: SHA-pinned GitHub Actions, exact-pinned npm dependencies (`save-exact=true` in `.npmrc`, no ranges and no unpinned `npx`), `package-lock.json` resolving from `registry.npmjs.org`, `harden-runner` egress audit, Dependabot for `github-actions` + `npm`

**Repo-specific:** this repo has no `plugins/`, so `validate:ci` is skills-only. The `agent-skills` repo additionally validates Cursor/Claude plugin manifests (`validate:plugins`) and filters on `plugins/**` — that's an intentional difference to preserve, not drift to "fix".

**When you change CI/CD here** — workflow triggers, install hardening, `skills-ref` pinning, Dependabot config, or validate scripts — **apply the same change to [neondatabase/agent-skills](https://github.com/neondatabase/agent-skills)**, preserving each repo's intentional differences (that repo keeps its plugin validation and `plugins/**` path filter).
