# Scripts

Per the **[Agent Skills layout](https://agentskills.io/specification#optional-directories)**, `scripts/` holds **executable helpers** bundled with a skill.

This repository keeps **runnable Console API examples** at the **repository root** so partners discover them next to docs and lint tooling:

**→ [`examples/api-scripts/`](../../../examples/api-scripts/)**

Use that folder for runnable **`examples/api-scripts/scripts/`** (TypeScript → **`dist/scripts/`**), env templates, and full command lists ([**MANAGEMENT_API_SAMPLES.md**](../../../examples/api-scripts/MANAGEMENT_API_SAMPLES.md)).

Agents implementing automation should **read or execute** those paths relative to the cloned repo, not expect copies under `skills/…/scripts/`.
