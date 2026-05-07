# Compound checkpoints for agent platforms

For **multi-tenant AI agent platforms**, products that provision, mutate, version, and retire **many** Neon-backed databases on behalf of users, a **checkpoint is not merely a Neon branch or snapshot**.

Treat a checkpoint as a **compound version record** your control plane owns. It should bind together, at minimum:

| Dimension | What to record | Why it matters for agent platforms |
| --------- | ---------------- | ---------------------------------- |
| **Source revision** | Git commit, template hash, artifact id, or codegen bundle id | Code and schema migrations move together with database state. |
| **Database state** | Neon **snapshot id** and/or **branch id** (plus project id) | Neon gives point-in-time and branch semantics; your ledger tells you which API objects belong to which tenant generation. |
| **Secrets / environment** | Version of env injected into the generated app (API keys, feature flags, `DATABASE_URL` rotation) | Restoring DB without matching secrets produces “works in Console, broken in app” failures. |
| **Deployment or sandbox URL** | Preview host, workflow run URL, or worker endpoint | Users and agents reconnect to the **same** runnable surface you rolled back. |
| **Agent run metadata** | Run id, session id, tool trace pointer | Audit, support, and coordinated retries across orchestration steps. |
| **Rollback / promotion status** | e.g. preview vs promoted, failed restore, orphaned branch cleanup | Fleet hygiene (avoid **`main (old)`** storage leaks; know which snapshot is “live” for billing). |

The scripts in **`examples/api-scripts/`** intentionally stay **Management API-sized**: they implement the **Neon slice** (projects, branches, snapshots, restore, transfer, consumption, Auth admin endpoints). They do **not** replace your meta-database schema, workflow engine, or deployment pipeline.

**Generic Neon usage** (connection strings, serverless driver, Drizzle, basic branching tutorials, Neon Auth app integration) lives in the **`neon-postgres`** skill and **[Neon documentation](https://neon.com/docs)**, use those first; use this repository for **fleet and control-plane** orchestration on top.

---

## Mapping Neon calls to your compound ledger

The Management API shapes below match the **`@neondatabase/api-client`** scripts under **`examples/api-scripts/scripts/`**. They are **one slice** of a checkpoint, you still persist the non-Neon dimensions in your platform store.

| Concern | Minimal API surface (Neon) | Script in this folder |
| ------- | -------------------------- | --------------------- |
| Create logical snapshot | `POST .../branches/{id}/snapshot` | [`snapshot.ts`](../snapshot.ts) |
| Restore snapshot onto a branch | `POST .../snapshots/{id}/restore` | [`restore-snapshot.ts`](../restore-snapshot.ts) |
| End-to-end demo (root snapshot → branch → restore) | Same operations composed | [`versioning-flow.ts`](../versioning-flow.ts) |
| Provision tenant project + enable branch Auth (admin) | `createProject`, branch Auth enable | [`create-project-with-auth.ts`](../create-project-with-auth.ts) |

For **full-stack orchestration** (pairing Neon ids with Git/deploy/secrets in one workflow), see one reference implementation in **[andrelandgraf/aileen](https://github.com/andrelandgraf/aileen)** and the walkthrough **[How to Build a Full-Stack AI Agent](https://neon.com/blog/how-to-build-a-full-stack-ai-agent)**, use it as a **worked example**, not as the only architecture.

---

## Example: where a reference stack stores compound rows

In **[aileen](https://github.com/andrelandgraf/aileen)**, **`project_versions`** rows combine **`neon_snapshot_id`** with **`gitCommitHash`** (and related metadata); workflows pair **`getLatestCommitHash`** with **`createNeonSnapshot`** before inserting the row ([`src/lib/workflows.ts`](https://github.com/andrelandgraf/aileen/blob/main/src/lib/workflows.ts), [`src/lib/steps.ts`](https://github.com/andrelandgraf/aileen/blob/main/src/lib/steps.ts)). Restore paths reset source control (see **[How to Build a Full-Stack AI Agent](https://neon.com/blog/how-to-build-a-full-stack-ai-agent)**) and apply the Neon snapshot in order ([`versions/route.ts`](https://github.com/andrelandgraf/aileen/blob/main/src/app/api/v1/projects/%5BprojectId%5D/versions/route.ts)). Your platform may use different hosts (CI artifact, container image digest), the **compound record** pattern still applies.

---

## Further reading

- **`neon-postgres`** (agent-skills), drivers, ORMs, Auth for **applications**, generic branching and connection patterns.
- [AI database versioning](https://neon.com/docs/ai/ai-database-versioning), Neon product semantics for snapshots and restore.
- [Checkpoints for agents with Neon Snapshots](https://neon.com/blog/checkpoints-for-agents-with-neon-snapshots)
- Product HTTP examples (your **app** API, not `console.neon.tech`): [`application-rest-api/CURL_REFERENCE.md`](application-rest-api/CURL_REFERENCE.md)
