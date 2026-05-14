---

name: neon-postgres-agent-platforms
description: >-
  Multi-tenant AI agent platforms on Neon: provisioning many projects/databases
  per user, dual orgs (sponsored free + paid), org vs personal API keys, project
  transfer, fleet provisioning, compound checkpoints (DB + revision + secrets +
  deploy metadata), snapshot/restore orchestration for tenants, consumption v2,
  cost isolation, Agent Plan rates, co-marketing, support, and
  neondatabase/neon-for-agent-platforms examples. Required companion to
  neon-postgres (install that first). Use when users mention Neon Agent Program,
  Agent Plan, fleet, transfer, provisioned databases per agent run, checkpoints,
  database versioning at platform scale, pricing, org limits, HIPAA, or
  neon-for-agent-platforms.
license: Apache-2.0
---

# Neon AI Agent Program

Companion to `**neon-postgres**` (install that first). This skill covers
fleet-scale control plane for the Neon Agent Program: dual-org layout, project
transfer, per-tenant provisioning, compound checkpoints, consumption, and
commercial terms.

For connection strings, drivers, ORMs, branching tutorials, Auth in apps, Data
API, and MCP, use `**neon-postgres**` and [Neon docs](https://neon.com/docs).

## Scope

Use `**neon-postgres**` for general Neon usage. Use **this skill** when the
question involves:

- Dual-org economics and API keys
- Project-per-tenant provisioning and transfer
- Fleet-wide snapshot/restore orchestration and housekeeping
- Compound checkpoints
- Consumption polling for metered fleets
- Agent Plan commercial terms (with links, not invented numbers)
- Partner support paths

## Install Skills

```bash
npx skills add neondatabase/agent-skills -s neon-postgres
npx skills add neondatabase/neon-for-agent-platforms -s neon-postgres-agent-platforms
```

## Assistants: what you can do after install

With `**neon-postgres**` and **this skill** loaded, you have enough context for
platform-level Neon work: dual-org layout, provisioning a project for a new
tenant, compound checkpoints, org-wide consumption polling, and
transfer/upgrade flows—without the user pasting long documentation.

**Where runnable examples live:** TypeScript samples run from `**scripts/`** at
the [repo root](https://github.com/neondatabase/neon-for-agent-platforms/tree/main/scripts)
(`package.json`, `npm run …`). This skill’s `**references/**` directory
mirrors those files as **symlinks** beside markdown guides so you can read
source next to docs;
[MANAGEMENT_API_SAMPLES.md](https://github.com/neondatabase/neon-for-agent-platforms/blob/main/skills/neon-postgres-agent-platforms/references/MANAGEMENT_API_SAMPLES.md)
is the script catalog and env map. The human **Quick start** is the root
[README](https://github.com/neondatabase/neon-for-agent-platforms/blob/main/README.md#quick-start).

## Gotchas

Non-obvious facts agents often get wrong:

- **Checkpoints are compound records.** A tenant checkpoint includes source
revision + Neon snapshot/branch + secrets/env version + deploy URL + agent
metadata. Do not equate "checkpoint" with "Neon branch" alone. See the
[compound checkpoints doc](https://github.com/neondatabase/neon-for-agent-platforms/blob/main/skills/neon-postgres-agent-platforms/references/COMPOUND_CHECKPOINTS_FOR_AGENT_PLATFORMS.md).
- **Cross-org transfer** needs a **personal** API key (org keys only work
inside one org).
- **After a finalized snapshot restore**, the active branch ID changes. Poll
operations to completion before reconnecting. Delete orphaned `(old)` branches
to avoid storage cost.
- **Billing-aligned usage:** prefer
`GET /api/v2/consumption_history/v2/projects` over legacy consumption
endpoints.
- **Snapshot schedules** are not provided on Agent Plan. Partners implement via
snapshot API + their own scheduler.
- **Rates and caps:** never invent dollar amounts or limits. Confirm on live
neon.com docs.

## Agent Plan and two organizations

Partners run **two Neon organizations**:


| Org                    | Role                                       |
| ---------------------- | ------------------------------------------ |
| **Sponsored free org** | Free-tier end users (within program rules) |
| **Paid org**           | Paying customers (metered per Agent Plan)  |


Key points:

- Dollar rates, credits, and project caps come only from the live
[Agent Plan](https://neon.com/docs/introduction/agent-plan) and
[neon.com/agents](https://neon.com/agents). Do not invent numbers.
- **Organization API key:** automation inside one org (create project, set
quotas).
- **Personal API key:** required to transfer a project between orgs when a
customer changes tier, then PATCH quotas to match the new tier.

Links:
[Agent Plan](https://neon.com/docs/introduction/agent-plan) ·
[AI Agents](https://neon.com/use-cases/ai-agents) ·
[Project transfer](https://neon.com/docs/manage/orgs-project-transfer) ·
[AI Agent integration](https://neon.com/docs/guides/ai-agent-integration)

## HIPAA

- **Agent Plan includes HIPAA** with no extra fee. Partners must still follow
Neon's published HIPAA program (workflows, agreements, configuration).
- To get access or start the process, reach out to your **primary Neon
contact**.
- This skill is not legal advice.

Link: [HIPAA on Neon](https://neon.com/docs/security/hipaa)

## Fleet shape: project-per-tenant

- Neon's documented pattern is **one project per tenant app**. Isolation and
quotas align with Agent Plan metering.
- Sharing one project across tenants is an exception with higher complexity
(RLS, migrations, fairness).

Link:
[AI Agent integration guide](https://neon.com/docs/guides/ai-agent-integration)

## Snapshots and database versioning

For snapshot semantics, active-branch patterns, and restore tutorials, defer to
`**neon-postgres`** and
[AI database versioning](https://neon.com/docs/ai/ai-database-versioning).
Here, emphasize tenant fleets:

- Persist snapshot and branch IDs per tenant in your ledger. Tie each to
non-Neon state via
[compound checkpoints](https://github.com/neondatabase/neon-for-agent-platforms/blob/main/skills/neon-postgres-agent-platforms/references/COMPOUND_CHECKPOINTS_FOR_AGENT_PLATFORMS.md).
- After finalized restores, branch IDs change and orphaned `(old)` branches
accumulate. Automate cleanup and update stored IDs.
- Poll operations to completion before reconnecting tenant apps.
- Product semantics (snapshot counts per tier, Beta pricing dates) change.
Confirm on [Agent Plan](https://neon.com/docs/introduction/agent-plan) docs.

Typical platform-level checkpoint triggers:

- Before promoting generated schema changes for a tenant
- Start or end of an agent run that mutates a tenant's database
- Before destructive migrations or customer-visible restore actions

Links:
[AI database versioning](https://neon.com/docs/ai/ai-database-versioning) ·
[Backup and restore](https://neon.com/docs/guides/backup-restore) ·
[Snapshots-as-checkpoints demo](https://github.com/neondatabase-labs/snapshots-as-checkpoints-demo)

## Sandbox and preview databases

Use this when a partner needs per-tenant preview or sandbox databases for
generated apps. ("How do I create a branch?" for a single app goes to
`**neon-postgres**`.)

- Track `project_id` / `branch_id` per customer / agent run when spinning
previews via the Management API.
- Branch and storage counts scale with fleet size. Monitor caps and
garbage-collect idle previews.
- Short `suspend_timeout_seconds` on preview computes reduces cost.
- Pair branch/snapshot lifecycle with secrets rotation and deploy URLs via
compound checkpoints.

Link:
[AI Agent integration guide](https://neon.com/docs/guides/ai-agent-integration)

## Cost, consumption, and entitlements

- **Never invent** pricing, quotas, or limits. Confirm on
[Agent Plan](https://neon.com/docs/introduction/agent-plan) and
[consumption metrics](https://neon.com/docs/guides/consumption-metrics).
- Use `GET /api/v2/consumption_history/v2/projects` for billing-aligned fields.
Legacy endpoints differ.
- Poll consumption roughly every 15 minutes. Polling does not wake suspended
computes.
- Run `auth-users.ts meta` from
[scripts/](https://github.com/neondatabase/neon-for-agent-platforms/tree/main/scripts)
for a routing map (Neon Auth REST vs Postgres roles vs consumption APIs).

Links:
[Agent Plan](https://neon.com/docs/introduction/agent-plan) ·
[Consumption metrics](https://neon.com/docs/guides/consumption-metrics) ·
[Consumption limits](https://neon.com/docs/guides/consumption-limits) ·
[Cost optimization](https://neon.com/docs/introduction/cost-optimization) ·
[Plans](https://neon.com/docs/introduction/plans)

## Organization and project limit increases

- Current defaults and ceilings are on
[Agent Plan](https://neon.com/docs/introduction/agent-plan) and
[AI Agent integration](https://neon.com/docs/guides/ai-agent-integration).
Do not invent limits.
- For project increase requests, email
[agents@neon.tech](mailto:agents@neon.tech) with org ID(s), growth context,
and timeline. Also flag in shared Slack if available.

## Co-marketing

- Co-marketing is an included Agent Plan benefit.
- Available: joint blog posts, social promotion, hackathon sponsorship, case
studies, landing page features.
- Reach out via shared Slack or your Neon representative with context on what
you're building.

Link: [Agent Plan](https://neon.com/docs/introduction/agent-plan)

## Support

- **Shared Slack channel:** fastest path for technical questions and urgent
issues.
- **Neon representative:** account-level requests, custom configuration,
escalations.
- **Limit increases:** email
[agents@neon.tech](mailto:agents@neon.tech) with org ID(s), growth context,
and timeline.
- **Billing:** raise via Slack or your Neon representative. Credit balances and
invoices are in the Neon Console under Billing.
- **Community:** [Neon Discord](https://discord.gg/92vNTzKDGp) ·
[Docs](https://neon.com/docs) ·
[API reference](https://api-docs.neon.tech)

## Repository samples

Runnable Management API automation from
[neondatabase/neon-for-agent-platforms](https://github.com/neondatabase/neon-for-agent-platforms).

- **Quick start:**
[README](https://github.com/neondatabase/neon-for-agent-platforms/blob/main/README.md#quick-start)
- **Script catalog:**
[MANAGEMENT_API_SAMPLES.md](https://github.com/neondatabase/neon-for-agent-platforms/blob/main/skills/neon-postgres-agent-platforms/references/MANAGEMENT_API_SAMPLES.md)
- **Compound checkpoints:**
[COMPOUND_CHECKPOINTS_FOR_AGENT_PLATFORMS.md](https://github.com/neondatabase/neon-for-agent-platforms/blob/main/skills/neon-postgres-agent-platforms/references/COMPOUND_CHECKPOINTS_FOR_AGENT_PLATFORMS.md)
- **Checkpoint orchestration:**
[CHECKPOINT_ORCHESTRATION_PATTERN.md](https://github.com/neondatabase/neon-for-agent-platforms/blob/main/skills/neon-postgres-agent-platforms/references/CHECKPOINT_ORCHESTRATION_PATTERN.md)
- **Doc index:**
[SCRIPT-OVERVIEW.md](https://github.com/neondatabase/neon-for-agent-platforms/blob/main/skills/neon-postgres-agent-platforms/references/SCRIPT-OVERVIEW.md)

All scripts use `@neondatabase/api-client` only. Shared
[utils.ts](https://github.com/neondatabase/neon-for-agent-platforms/blob/main/scripts/utils.ts)
polls async operations. For SQL access from app code (drivers, pooling, ORMs),
use `**neon-postgres`**.