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
