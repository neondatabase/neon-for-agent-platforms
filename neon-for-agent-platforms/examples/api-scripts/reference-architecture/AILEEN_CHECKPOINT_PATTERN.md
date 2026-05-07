# Aileen: Neon provisioning + checkpoint / versioning pattern

This note maps **[andrelandgraf/aileen](https://github.com/andrelandgraf/aileen)** (and the walkthrough **[How to Build a Full-Stack AI Agent](https://neon.com/blog/how-to-build-a-full-stack-ai-agent)**) to **minimal Neon API surface** and to the scripts in **this folder**.

Aileen is a **full application**: control-plane Postgres (meta DB), Freestyle (git + dev server), Stack/Neon Auth, Assistant UI, Mastra, Vercel Workflows. There is **no single “checkpoint script”** in that repo, checkpointing is **orchestration** that pairs **Git commit** with **Neon snapshot id** and stores both in **`project_versions`**.

---

## Where the Neon + snapshot code lives (Aileen)

| Concern | Primary file(s) | What it does |
|--------|------------------|--------------|
| **REST calls to Neon** (create project, branches, snapshot, restore, connection URI, ops polling) | [`src/lib/neon.ts`](https://github.com/andrelandgraf/aileen/blob/main/src/lib/neon.ts) | `NeonService`: `createProject`, `initNeonAuth` (Stack auth via `POST /projects/auth/create`), `getProductionBranch`, `createSnapshot`, `applySnapshot`, `waitForOperationsToSettle`, etc. |
| **Meta DB: version row = git hash + snapshot id** | [`src/lib/steps.ts`](https://github.com/andrelandgraf/aileen/blob/main/src/lib/steps.ts) | `createNeonSnapshot`, `createCheckpointVersion`, `createInitialVersion`, `copyProjectSecrets`, Drizzle inserts into `project_versions` / `project_secrets`. |
| **Checkpoint workflow (parallel commit + snapshot)** | [`src/lib/workflows.ts`](https://github.com/andrelandgraf/aileen/blob/main/src/lib/workflows.ts) | `createManualCheckpoint`: `Promise.all([ getLatestCommitHash(repoId), createNeonSnapshot(neonProjectId) ])` then `createCheckpointVersion(...)`. |
| **HTTP entry: user-triggered checkpoint** | [`src/app/api/v1/projects/[projectId]/checkpoint/route.ts`](https://github.com/andrelandgraf/aileen/blob/main/src/app/api/v1/projects/%5BprojectId%5D/checkpoint/route.ts) | `POST` → `start(createManualCheckpoint, [...])` (Vercel Workflow). |
| **Restore “full stack version”** | [`src/app/api/v1/projects/[projectId]/versions/route.ts`](https://github.com/andrelandgraf/aileen/blob/main/src/app/api/v1/projects/%5BprojectId%5D/versions/route.ts) | `POST`: load version → decrypt secrets → Freestyle git reset to **`gitCommitHash`** → `neonService.applySnapshot(neonProjectId, neonSnapshotId, mainBranchId)` → update `currentDevVersionId`. |

---

## Neon API shapes (same as our thin scripts)

Aileen’s [`NeonService.createSnapshot`](https://github.com/andrelandgraf/aileen/blob/main/src/lib/neon.ts) / [`applySnapshot`](https://github.com/andrelandgraf/aileen/blob/main/src/lib/neon.ts) match the blog snippets:

- **Create snapshot:** `POST /projects/{project_id}/branches/{production_branch_id}/snapshot` with JSON `{ timestamp, name }`.
- **Restore:** `POST /projects/{project_id}/snapshots/{snapshot_id}/restore` with JSON `{ name, finalize_restore, target_branch_id }`, then **wait for operations** returned in the response.

In **this repo**, the same operations are exposed as:

| Aileen | Here |
|--------|------|
| `createSnapshot` | [`snapshot.ts`](../snapshot.ts) |
| `applySnapshot` | [`restore-snapshot.ts`](../restore-snapshot.ts) |
| End-to-end branching + snapshots + restore demo | [`versioning-flow.ts`](../versioning-flow.ts) |
| Provision project + enable Auth (Better Auth) | [`create-project-with-auth.ts`](../create-project-with-auth.ts) |

**Auth note:** Aileen uses **Stack** (`auth_provider: "stack"`, `POST /projects/auth/create`). Agent Program samples often use **`better_auth`** on the branch (`createNeonAuth` via **`@neondatabase/api-client`** in [`scripts/create-project-with-auth.ts`](../scripts/create-project-with-auth.ts)). Product choice differs; Neon Auth APIs are documented separately.

---

## What agent platforms still implement themselves

1. **Meta database**, store `neon_snapshot_id`, `git_commit_hash` (or app bundle id), optional `assistant_message_id`, timestamps (see Aileen `project_versions`).
2. **When to snapshot**, e.g. after each agent commit or user “Create checkpoint” (Aileen: workflow + route).
3. **Restore UX**, lookup version → restore Git (or deploy) → **`applySnapshot`** to **`main`** (or a preview branch id).

This folder intentionally stays **API-sized**; use Aileen + the blog as the **full-stack reference** for checkpointing UI and meta-schema.

---

## Further reading

- [Checkpoints for agents with Neon Snapshots](https://neon.com/blog/checkpoints-for-agents-with-neon-snapshots) (linked from the full-stack post)
- [AI database versioning](https://neon.com/docs/ai/ai-database-versioning)
- Same HTTP routes as **`curl`** templates (localhost / placeholders): [`application-rest-api/README.md`](application-rest-api/README.md)
