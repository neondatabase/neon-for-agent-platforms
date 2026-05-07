# [AGENTS.md](http://AGENTS.md)

Guidance for AI coding agents (Cursor, Claude Code, Copilot, etc.) working in this repository.

For the Agent Skills file layout and frontmatter rules, see the [Agent Skills specification](https://agentskills.io/specification) and [overview](https://agentskills.io/home).

**Relationship to [postgres-skills](https://github.com/neondatabase/postgres-skills):** That repository is Neon’s home for **general Postgres agent skills** (best practices, schema design, etc.). **This repo does not replace it.** Stay subscribed to postgres-skills for that content. Here we only ship **Agent Program-specific** samples plus one **companion** skill; when you edit that skill, you can use postgres-skills’ `[CONTRIBUTING.md](https://github.com/neondatabase/postgres-skills/blob/main/CONTRIBUTING.md)` as an optional guide for **SKILL.md** quality and structure, same ecosystem, different scope.

## What this repository is

Sample **Node** examples and documentation for the **[Neon AI Agent Program](https://neon.com/use-cases/ai-agents)** (platforms that provision Neon Postgres per customer), plus a **companion skill** that complements the upstream `[neon-postgres](https://github.com/neondatabase/agent-skills)` bundle.

**Differentiation:** `**neon-postgres`** covers general Neon usage (drivers, connection patterns, ORMs, branching tutorials, Auth in apps). **This repo** focuses on **multi-tenant agent-platform control planes**, orgs/fleets, transfers, tenant lifecycle, compound checkpoints, Management API orchestration, without duplicating those generic guides.

This repo uses the **[Agent Skills directory shape](https://agentskills.io/specification)** under `**skills/neon-postgres-agent-platforms/`** (`SKILL.md`, `references/`, `scripts/`, `assets/`) and keeps **executable samples** in `**examples/api-scripts/`** at the root, see **[README, What’s in this repository](README.md#whats-in-this-repository)**.


| Area                                    | Your focus                                                                                                                                                                                             |
| --------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `examples/api-scripts/`                 | **Neon TypeScript SDK** = **[`@neondatabase/api-client`](https://www.npmjs.com/package/@neondatabase/api-client)** only (no other `@neondatabase/*` packages); (`scripts/*.ts` → `dist/scripts/*.js`); shared **[operation polling helpers](examples/api-scripts/scripts/lib/operations.ts)** only |
| `skills/neon-postgres-agent-platforms/` | Companion skill, Agent Program context (orgs, transfers, fleets). Ship with `**neon-postgres`**, not instead of it                                                                                      |


Unlike skill-only repos, **example TypeScript is first-class**. Prefer small, explicit scripts with clear env validation and structured JSON output where appropriate.

## Editing the companion skill

- Directory `**skills/neon-postgres-agent-platforms/`** must stay aligned with the skill `**name`** in YAML frontmatter (see Agent Skills spec).
- Follow **[Skill creation best practices](https://agentskills.io/skill-creation/best-practices)**: valid frontmatter, **Gotchas** for non-obvious corrections, **defaults** over long option menus, **progressive disclosure** (detailed tables in `references/` with explicit *when to read* hints in `SKILL.md`).
- For tone and depth on the companion skill only, you may borrow patterns from [postgres-skills `CONTRIBUTING](https://github.com/neondatabase/postgres-skills/blob/main/CONTRIBUTING.md)` (concrete tradeoffs, imperative guidance, `references/` for long material), that repo remains authoritative for Postgres-wide skills; this skill stays narrowly about Agent Program provisioning and org patterns.

## Editing examples (`examples/api-scripts/scripts/**/*.ts`)

Before opening a PR that touches example scripts:

```bash
npm install          # repo root, format/typecheck devDependencies
npm run fmt:check
npm run typecheck
```

Formatting scripts align with [postgres-skills](https://github.com/neondatabase/postgres-skills): `**npm run fmt**` / `**npm run fmt:check**`.

Expectations: ES modules; TypeScript sources in **`examples/api-scripts/scripts/`**, compile with **`npm run build`** (`tsc`) to **`dist/scripts/`**, run with **`node dist/scripts/...js`**; runtime deps are **`@neondatabase/api-client`** and **`dotenv`** only; validate **`NEON_API_KEY`** (and script-specific env) at startup; prefer structured JSON for machine-readable CLI output; never commit `.env`, keys, or connection strings.

## Docs and README

**Human onboarding** uses **[README § Start here](README.md#start-here-agent-program-partners)** only, do not introduce a second “getting started” story in other files without linking back there.

Keep cross-links between **[README.md](README.md)** (single doc entry point), **[examples/api-scripts/MANAGEMENT_API_SAMPLES.md](examples/api-scripts/MANAGEMENT_API_SAMPLES.md)**, and **[skills/neon-postgres-agent-platforms/SKILL.md](skills/neon-postgres-agent-platforms/SKILL.md)** consistent when you add or rename scripts.