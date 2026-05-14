# References

Doc index for `skills/neon-postgres-agent-platforms/references/`.
For clone, install, and quick start, see the [root README](../../../README.md).

## Reading order

1. **[MANAGEMENT_API_SAMPLES.md](MANAGEMENT_API_SAMPLES.md)** — env vars and `npm run …` commands for each script.
2. **[COMPOUND_CHECKPOINTS_FOR_AGENT_PLATFORMS.md](COMPOUND_CHECKPOINTS_FOR_AGENT_PLATFORMS.md)** — what belongs in a compound checkpoint **record** (dimensions: revision, Neon ids, secrets, URLs, metadata).
3. **[CHECKPOINT_ORCHESTRATION_PATTERN.md](CHECKPOINT_ORCHESTRATION_PATTERN.md)** — how meta DB, workflows, Neon API calls, and **restore ordering** usually fit together. Read **after** (2) when you need architecture, not before.

Optional: **[application-rest-api/](application-rest-api/)** (curl against **your** app API, not Neon console), **[pricing-and-plan-features.md](pricing-and-plan-features.md)**, **[LICENSE](../../../LICENSE)**.

## Symlinked TypeScript (canonical: `../../../scripts/`)

Each **`*.ts`** here is a symlink to the same file under **`scripts/`** so assistants can open samples beside the skill.

| Symlink | Purpose (short) |
| ------- | ---------------- |
| [auth-users.ts](auth-users.ts) | Neon Auth REST admin (`meta`, `create`, `delete`). |
| [branch.ts](branch.ts) | List or create branches. |
| [consumption-query.ts](consumption-query.ts) | Consumption **v2** per project/org. |
| [create-project-with-auth.ts](create-project-with-auth.ts) | Create project + enable Neon Auth on the branch. |
| [create-project.ts](create-project.ts) | Create project; wait on operations. |
| [delete-branch.ts](delete-branch.ts) | Delete a branch by id (e.g. orphaned `main (old)`). |
| [delete-project.ts](delete-project.ts) | Delete a project (destructive). |
| [delete-snapshot.ts](delete-snapshot.ts) | Delete one snapshot; polls operations. |
| [list-projects.ts](list-projects.ts) | List projects (ids + names). |
| [list-snapshots.ts](list-snapshots.ts) | List snapshots for `NEON_PROJECT_ID`. |
| [promote-safe-production.ts](promote-safe-production.ts) | Safe promote / bootstrap / rollback flows. |
| [rename-snapshot.ts](rename-snapshot.ts) | PATCH rename snapshot. |
| [restore-snapshot.ts](restore-snapshot.ts) | Apply snapshot to a branch. |
| [snapshot.ts](snapshot.ts) | Create logical snapshot on default branch. |
| [transfer-project.ts](transfer-project.ts) | Move project between orgs (personal key). |
| [versioning-flow.ts](versioning-flow.ts) | Snapshot → branch → restore demo. |
| [utils.ts](utils.ts) | Shared polling helpers for async Management API ops. |

**Run:** from **`scripts/`**, **`npm run build`** (or any **`npm run <sample>`**, which runs **`build`** first) emits **`dist/scripts/*.js`**. Then **`node dist/scripts/<name>.js`** or **`npm run …`**.

**Windows:** `git config core.symlinks true` (or Developer Mode) so symlinks resolve after clone.
