---
name: neon-postgres-agent-platforms
description: >-
  Multi-tenant AI agent platforms on Neon: provisioning many projects/databases per user,
  dual orgs (sponsored free + paid), org vs personal API keys, project transfer, fleet
  provisioning, compound checkpoints (DB + revision + secrets + deploy metadata),
  snapshot/restore orchestration for tenants, consumption v2, cost isolation, Agent Plan
  rates, co-marketing, support, and neondatabase/neon-for-agent-platforms examples.
  Required companion to neon-postgres (install that first). Does not cover generic Neon
  tutorials (drivers, Drizzle, basic branching how-tos). Use when users mention Neon Agent
  Program, Agent Plan, fleet, transfer, provisioned databases per agent run, checkpoints,
  database versioning at platform scale, pricing, org limits, HIPAA, or neon-for-agent-platforms.
license: Apache-2.0
---

# Neon AI Agent Program (companion skill)

**Partners cloning the repo:** follow **[README, Start here (Agent Program partners)](https://github.com/neondatabase/neon-for-agent-platforms/blob/main/README.md#start-here-agent-program-partners)** and **[examples/api-scripts/MANAGEMENT_API_SAMPLES.md](https://github.com/neondatabase/neon-for-agent-platforms/blob/main/examples/api-scripts/MANAGEMENT_API_SAMPLES.md)** for scripts and env vars. This file is for **assistants** (after **`neon-postgres`**); it does not replace that skill.

This directory follows the **[Agent Skills](https://agentskills.io/specification)** layout (`SKILL.md`, optional `references/`, `scripts/`, `assets/`). Runnable Management API samples live under **`examples/api-scripts/`** at the **[repository root](https://github.com/neondatabase/neon-for-agent-platforms)** (not inside this skill folder).

Neon’s **Agent Program** is for products where **you provision Neon Postgres for end users** (agent platforms, codegen tools, multi-tenant SaaS). Your Neon contact and **[neon.com](https://neon.com)** define pricing, limits, and eligibility.

### Scope: agent platforms vs generic Neon

Use **`neon-postgres`** for **general** Neon usage (everything listed in that skill, drivers, connections, ORMs, branching tutorials, Auth in apps, Data API, MCP, etc.). Do not repeat those topics here.

Use **this skill** when the question is **fleet-scale control plane**: separate platform infrastructure vs generated-app databases, **two Neon orgs**, **project transfer**, **per-tenant lifecycle**, coordinating Neon Management API calls from backend workflows, **consumption and isolation**, and **what to persist beyond a branch id** (compound checkpoints, see **[compound checkpoints reference](https://github.com/neondatabase/neon-for-agent-platforms/blob/main/examples/api-scripts/reference-architecture/COMPOUND_CHECKPOINTS_FOR_AGENT_PLATFORMS.md)** in the repo).

**This skill’s scope (agent platforms):** dual-org economics and keys · project-per-tenant provisioning and transfer · fleet-wide snapshot/restore orchestration and housekeeping · compound checkpoints · consumption polling for metered fleets · Agent Plan commercial terms (with links, not invented numbers) · partner support paths. Everything else → **`neon-postgres`**.

## Install Neon agent skills (order matters)

**Step 1:** Install Neon’s primary skill from **[github.com/neondatabase/agent-skills](https://github.com/neondatabase/agent-skills)**, that bundle is the baseline for **all** generic Neon guidance:

```bash
npx skills add neondatabase/agent-skills -s neon-postgres
```

Or bootstrap skills + MCP together: `npx neonctl@latest init`, see [Agent Skills docs](https://neon.com/docs/ai/agent-skills).

**Step 2:** If you are on the **Neon AI Agent Program** or building an **agent platform** that provisions Postgres for end users, install this companion skill. It is **required** for that context (org layout, transfers, fleet patterns, quotas, cost guidance). It is not a substitute for Step 1:

```bash
npx skills add neondatabase/agent-skills -s neon-postgres-agent-platforms
```

Teams using Neon **without** the Agent Program / per-customer provisioning model may rely on Step 1 alone. Everyone else should use **both** steps. **Do not** rely on this skill alone for full Neon guidance, **`neon-postgres`** stays mandatory as the platform baseline.

## Gotchas

Non-obvious facts agents often get wrong without this skill:

- **Install `neon-postgres` first**: This skill is a *companion*; it does not cover Auth for app code, Data API, drivers, Drizzle setup, generic branching tutorials, or other everyday Neon how-tos (those stay in **`neon-postgres`**).
- **Checkpoints on platforms**: A tenant checkpoint is usually a **compound record** (source revision + Neon snapshot/branch + secrets/env version + deploy URL + agent metadata). Do not equate “checkpoint” with “Neon branch” alone, see the **[compound checkpoints doc](https://github.com/neondatabase/neon-for-agent-platforms/blob/main/examples/api-scripts/reference-architecture/COMPOUND_CHECKPOINTS_FOR_AGENT_PLATFORMS.md)**.
- **Cross-org transfer** needs a **personal** API key (org keys only automate inside one org).
- **After a finalized snapshot restore**, the active **branch ID changes**; poll operations to completion before reconnecting; delete orphaned `(old)` branches to avoid storage cost.
- **Billing-aligned usage**: Prefer `GET /api/v2/consumption_history/v2/projects` over legacy consumption endpoints (fields differ).
- **Snapshot schedules**: Not provided on Agent Plan; partners implement via snapshot API + their own scheduler.
- **Rates and caps**: Never invent dollar amounts or limits; confirm on live neon.com docs for the question at hand.

## Neon documentation (generic)

How to browse Neon docs (Markdown URLs, `llms.txt`, etc.) is covered in **`neon-postgres`**, reuse that rather than duplicating here. For **Agent Plan pricing, limits, and program rules**, still verify on live **[neon.com](https://neon.com)** before quoting numbers.

## Agent Plan & Two Organizations

Use this when the user is on (or applying to) the **Agent Program** and needs the **commercial / org layout**: sponsored tier for *their* free users vs paid tier for paying users.

Key points:

- Two **Neon organizations**: one **sponsored** for your free-tier customers (within program rules on the site), one **paid** for customers you bill (metered per plan).
- **Dollar rates, credits, project caps**: only from live **[Agent Plan](https://neon.com/docs/introduction/agent-plan)** and **[neon.com/agents](https://neon.com/agents)**, do not invent numbers.
- **HIPAA**: the **Agent Plan includes HIPAA** without an additional charge. To **obtain access** or complete enablement, the customer should contact their **main Neon account contact** (not a substitute for reading Neon’s requirements). Process and obligations are documented on Neon’s HIPAA page.

Link: [https://neon.com/docs/introduction/agent-plan.md](https://neon.com/docs/introduction/agent-plan.md)

Link: [https://neon.com/use-cases/ai-agents.md](https://neon.com/use-cases/ai-agents.md)

## HIPAA (Agent Plan)

Use this when an **Agent Program** customer asks about **HIPAA** or compliance for health data on Neon.

Key points:

- **Agent Plan includes HIPAA** with **no extra fee** for that inclusion, still follow Neon’s published HIPAA program (workflows, agreements, configuration) on the site; do not treat this skill as legal advice.
- To **get access** or start the process, they should reach out to their **primary Neon contact** (the main relationship owner for their org).
- Technical and program requirements: **[HIPAA on Neon](https://neon.com/docs/security/hipaa)**.

Link: [https://neon.com/docs/security/hipaa.md](https://neon.com/docs/security/hipaa.md)

## Organization & Project Limit Increases

Use this when an **Agent Program** partner hits **organization-level limits** (for example **maximum projects per org**) and needs a higher allowance.

Key points:

- Current defaults and ceilings are defined on **[neon.com](https://neon.com)** (**[Agent Plan](https://neon.com/docs/introduction/agent-plan)**, **[AI Agent integration guide](https://neon.com/docs/guides/ai-agent-integration)**), do not invent new limits or promise approval.
- For **project increase requests** (or related org capacity changes), email **agents@neon.tech**. Ask the partner to include **organization ID(s)** (free + paid if both), brief **growth / usage context**, and any **timeline**, and to continue using **shared Slack** if they already have a channel with Neon.

Contact: mailto:[agents@neon.tech](mailto:agents@neon.tech)

## Fleet Shape: Project-per-Tenant

Use this when designing **how many Neon projects** to create for customers.

Key points:

- Neon’s documented integration pattern is **one Neon project per tenant app** (project-per-tenant): isolation and quotas line up with Agent Plan metering.
- Sharing one project across tenants is an exception, higher complexity (RLS, migrations, fairness).

Link: [https://neon.com/docs/guides/ai-agent-integration.md](https://neon.com/docs/guides/ai-agent-integration.md)

## API Keys & Cross-Org Transfer

Use this when automating **provision**, **upgrade**, or **downgrade** between free and paid tiers.

Key points:

- **Organization API key**: automation **inside one org** (create project, set quotas).
- **Personal API key**: required to **transfer a project** between organizations when a customer changes tier; then **PATCH quotas** to match the new tier.

Link: [https://neon.com/docs/manage/orgs-project-transfer.md](https://neon.com/docs/manage/orgs-project-transfer.md)

Link: [https://neon.com/docs/guides/ai-agent-integration.md](https://neon.com/docs/guides/ai-agent-integration.md)

## Snapshots & Database Versioning

Use this when an Agent Program partner needs **undo/redo**, **tenant-level versioning**, or **Neon snapshot/restore orchestration** across many projects.

For **snapshot semantics, active-branch patterns, PITR windows, and restore tutorials**, defer to **`neon-postgres`** and Neon’s **[AI database versioning](https://neon.com/docs/ai/ai-database-versioning)**. Here, emphasize **tenant fleets**:

- Persist **snapshot and branch ids per tenant** in your ledger; tie each to **non-Neon state** (**[compound checkpoints](https://github.com/neondatabase/neon-for-agent-platforms/blob/main/examples/api-scripts/reference-architecture/COMPOUND_CHECKPOINTS_FOR_AGENT_PLATFORMS.md)**).
- After finalized restores, **branch ids change** and orphaned **`(old)`** branches can multiply across projects, automate cleanup and update stored ids (**cost + correctness** at fleet scale).
- **Poll operations** to completion before reconnecting tenants’ apps.
- **Snapshot scheduling** is **not** a built-in Agent Plan feature, partners implement via API + **per-tenant** schedulers when needed.
- Product semantics (manual snapshot counts per tier, Beta pricing dates, etc.) change, confirm on **[Agent Plan](https://neon.com/docs/introduction/agent-plan)** / versioning docs, not from memory.

Typical **platform-level** triggers (still persist a **compound** row in your meta DB, not Neon alone):

- Before promoting generated schema changes for a tenant
- Start or end of an **agent run** that mutates that tenant’s database
- Before destructive migrations or customer-visible “restore” actions in your product UI

Links (generic Neon): [AI database versioning](https://neon.com/docs/ai/ai-database-versioning.md) · [Backup & restore](https://neon.com/docs/guides/backup-restore.md) · [snapshots-as-checkpoints demo](https://github.com/neondatabase-labs/snapshots-as-checkpoints-demo), use with **`neon-postgres`**, not as a second tutorial here.

## Sandbox / preview databases for generated apps

Use this when a partner needs **per-tenant preview or sandbox Neon databases** for **generated applications**. (“How do I create a branch?” for a single app → **`neon-postgres`**.)

Key points:

- Your control plane tracks **`project_id` / `branch_id` ↔ customer / agent run** when you spin previews via the Management API.
- **Branch and storage counts scale with fleet size**, monitor caps (free vs paid **branch limits** differ; confirm current numbers on neon.com) and garbage-collect idle previews.
- Short **`suspend_timeout_seconds`** on preview computes reduces cost when agents create sandboxes often.
- Pair branch/snapshot lifecycle with **secrets rotation** and **deploy URLs** via **[compound checkpoints](https://github.com/neondatabase/neon-for-agent-platforms/blob/main/examples/api-scripts/reference-architecture/COMPOUND_CHECKPOINTS_FOR_AGENT_PLATFORMS.md)** so previews stay coherent.

Link: [AI Agent integration guide](https://neon.com/docs/guides/ai-agent-integration.md)

## Cost, consumption, and Agent Plan entitlements

Default: give a **high-level** answer and point to live docs for numbers.

- **Never invent** pricing, quotas, or limits, confirm on **[Agent Plan](https://neon.com/docs/introduction/agent-plan)** and **[consumption metrics](https://neon.com/docs/guides/consumption-metrics)** for the user’s question.
- On usage-based plans, use `GET /api/v2/consumption_history/v2/projects` for billing-aligned fields; legacy endpoints differ, endpoint semantics are generic Neon API territory (**`neon-postgres`** + docs).
- Poll consumption about every **15 minutes**; polling does not wake suspended computes.
- **REST vs Postgres vs billing confusion**: run **`auth-users.ts meta`** from **[examples/api-scripts](https://github.com/neondatabase/neon-for-agent-platforms/tree/main/examples/api-scripts)** for a short routing map (Neon Auth REST vs Postgres roles vs consumption APIs).

Quick links: [Agent Plan](https://neon.com/docs/introduction/agent-plan.md) · [Consumption metrics](https://neon.com/docs/guides/consumption-metrics.md) · [Consumption limits](https://neon.com/docs/guides/consumption-limits.md) · [Cost optimization](https://neon.com/docs/introduction/cost-optimization.md) · [Plans](https://neon.com/docs/introduction/plans.md)

## Co-Marketing

Use this when a partner asks about **joint marketing opportunities** with Neon.

Key points:

- Co-marketing is an included **Agent Plan benefit**.
- Available opportunities: **joint blog posts**, **social promotion** from Neon's channels, **hackathon sponsorship/support**, **case studies**, and landing page features.
- To get started, the partner should reach out via their **shared Slack channel** with Neon or contact their **Neon representative** directly.
- Provide context on what they're building, user/growth numbers, and the type of co-marketing they're interested in.

Link: [https://neon.com/docs/introduction/agent-plan.md](https://neon.com/docs/introduction/agent-plan.md)

## Support

Use this when a partner asks about getting **technical help**, **billing support**, or **limit increases**.

Key points:

- **Shared Slack channel**: Every Agent Plan participant gets a dedicated Slack channel with direct access to the Neon team. This is the fastest path for technical questions and urgent issues.
- **Neon representative**: Each partner has a primary contact at Neon for account-level requests, custom configuration, and escalations.
- **Limit increases** (project caps, rate limits, etc.): Email **agents@neon.tech** with org ID(s), growth context, and timeline. Also flag in the shared Slack channel.
- **Billing questions**: Raise via the shared Slack channel or through the Neon representative. Credit balances and invoices are visible in the Neon Console under Billing for each org.
- **Priority support**: Agent Plan participants receive faster response times for platform-critical issues.
- **Community resources**: [Neon Discord](https://discord.gg/92vNTzKDGp), [Neon documentation](https://neon.com/docs), [API reference](https://api-docs.neon.tech).

Contact: mailto:[agents@neon.tech](mailto:agents@neon.tech)

## neon-for-agent-platforms repository samples

Use when the user wants runnable **Management API** automation from **[neondatabase/neon-for-agent-platforms](https://github.com/neondatabase/neon-for-agent-platforms)**.

There is **no** separate “mini reference” doc, the **[repository README](https://github.com/neondatabase/neon-for-agent-platforms/blob/main/README.md)** (**Start here** section) is the human entry point. **All** runnable scripts live under **`examples/api-scripts/`**:

- **[README, Fleet provisioning & org layout](https://github.com/neondatabase/neon-for-agent-platforms/blob/main/README.md#fleet-provisioning-and-org-layout)**: two-org mental model mapped to scripts.
- **[examples/api-scripts/MANAGEMENT_API_SAMPLES.md](https://github.com/neondatabase/neon-for-agent-platforms/blob/main/examples/api-scripts/MANAGEMENT_API_SAMPLES.md)**: full script list, env vars, commands.
- **[Compound checkpoints for agent platforms](https://github.com/neondatabase/neon-for-agent-platforms/blob/main/examples/api-scripts/reference-architecture/COMPOUND_CHECKPOINTS_FOR_AGENT_PLATFORMS.md)**: what to store besides Neon ids (revision, secrets, deploy, metadata).
- **Neon TypeScript SDK** in these samples means **`@neondatabase/api-client`** only (no other Neon npm packages). Samples use it directly (`createApiClient`); shared **[operations helpers](https://github.com/neondatabase/neon-for-agent-platforms/blob/main/examples/api-scripts/scripts/lib/operations.ts)** only poll async operations. Fleet-oriented flows include create/delete project, branches, snapshots + **`versioning-flow.ts`** ([database versioning](https://neon.com/docs/ai/ai-database-versioning)), org transfer, consumption **v2**, Neon Auth admin routes (**`auth-users.ts meta`** distinguishes REST Auth vs Postgres roles).

For **SQL access from application code** (drivers, pooling, ORMs), use **`neon-postgres`** from agent-skills and **[neon.com/docs](https://neon.com/docs)**, this repo does not ship a separate minimal query sample.

For full product guidance on provisioning fleets and org layout, use the **[AI Agent integration guide](https://neon.com/docs/guides/ai-agent-integration)** on neon.com alongside these samples.

## Partner-facing docs in this repo

- **Single onboarding path:** [README, Start here](https://github.com/neondatabase/neon-for-agent-platforms/blob/main/README.md#start-here-agent-program-partners).
- **Post-call addendum:** [NEON_AGENT_PROGRAM_POST_CALL_GUIDE.md](https://github.com/neondatabase/neon-for-agent-platforms/blob/main/docs/NEON_AGENT_PROGRAM_POST_CALL_GUIDE.md)

