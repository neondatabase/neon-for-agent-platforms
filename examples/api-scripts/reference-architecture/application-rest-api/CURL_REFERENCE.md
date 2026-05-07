# Application REST API: curl reference

These **`curl`** examples are for an **application’s own HTTP API** (your product server), **not** Neon’s Management API. They were aligned with one **reference full-stack agent app** ([andrelandgraf/aileen](https://github.com/andrelandgraf/aileen)) so partner engineers can see how **compound checkpoints** (HTTP surface + Neon orchestration behind it) look in practice, see **[`../COMPOUND_CHECKPOINTS_FOR_AGENT_PLATFORMS.md`](../COMPOUND_CHECKPOINTS_FOR_AGENT_PLATFORMS.md)** for what a platform-owned checkpoint should bind together beyond a Neon branch id.

Use **`APP_BASE_URL=http://localhost:3000`** (or your dev URL). Do **not** point these at Neon **`console.neon.tech`**; control-plane scripts are documented in **[README](../../../../README.md)** and **[MANAGEMENT_API_SAMPLES.md](../../MANAGEMENT_API_SAMPLES.md)**.

## Auth

That reference stack uses **Stack Auth** (`stackServerApp.getUser()`): authenticated routes expect a **browser session cookie**, not `NEON_API_KEY`. For `curl`, paste a `Cookie` header from DevTools after signing in (keep it out of git), or expect **`401 Unauthorized`** when omitting it, still useful to verify the route exists.

## Sources (exact paths in the reference app)

| Method | Path | Source file |
|--------|------|-------------|
| `GET` | `/api/v1/models` | [`src/app/api/v1/models/route.ts`](https://github.com/andrelandgraf/aileen/blob/main/src/app/api/v1/models/route.ts) |
| `POST` | `/api/v1/projects` | [`src/app/api/v1/projects/route.ts`](https://github.com/andrelandgraf/aileen/blob/main/src/app/api/v1/projects/route.ts) |
| `GET` | `/api/v1/projects/{projectId}/versions` | [`src/app/api/v1/projects/[projectId]/versions/route.ts`](https://github.com/andrelandgraf/aileen/blob/main/src/app/api/v1/projects/%5BprojectId%5D/versions/route.ts) |
| `POST` | `/api/v1/projects/{projectId}/checkpoint` | [`src/app/api/v1/projects/[projectId]/checkpoint/route.ts`](https://github.com/andrelandgraf/aileen/blob/main/src/app/api/v1/projects/%5BprojectId%5D/checkpoint/route.ts) |
| `POST` | `/api/v1/projects/{projectId}/versions` | Same `versions/route.ts` (`POST` restores a version) |

There is **no** dedicated `/health` route in that upstream tree; add one in your app if you need probes, or treat **`GET /api/v1/models`** as an authenticated liveness check against your stack.

## Run

From the **repository root**:

```bash
cd examples/api-scripts/reference-architecture/application-rest-api
chmod +x curl-examples.sh   # once
./curl-examples.sh
```

Optional env (see [`.env.example`](.env.example)): `APP_BASE_URL`, `APP_PROJECT_ID`, `APP_VERSION_ID`, `STACK_SESSION_COOKIE`.
