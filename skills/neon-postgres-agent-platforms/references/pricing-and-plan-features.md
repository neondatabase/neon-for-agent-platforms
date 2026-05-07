# Pricing, consumption, and Agent Plan feature matrix

Read this file when the user needs **line-item rates**, **quota numbers**, the **full Agent Plan entitlement list**, or **consumption API** field guidance, not for a high-level “what is the Agent Program” answer (use `SKILL.md` for that).

Always re-verify numbers on live **[Agent Plan](https://neon.com/docs/introduction/agent-plan)** and **[consumption](https://neon.com/docs/guides/consumption-metrics)** docs before quoting.

---

## Cost implications and tracking

Use when a partner asks about **pricing**, **cost optimization**, or **consumption monitoring** for their fleet.

### Pricing (paid org only; free org is $0)

- **Compute**: $0.106/CU-hour (roughly half of standard Scale pricing at $0.222)
- **Storage**: $0.35/GB-month
- **Instant restore (PITR)**: $0.20/GB-month for change history
- **Snapshots**: $0.09/GB-month (after Beta, starting May 1, 2026)
- **Extra branches**: $1.50/branch-month (beyond plan allowance)
- **Public network transfer**: 100 GB included, then $0.10/GB
- **Initial credits**: up to **$25,000** for the paid org

### Cost optimization levers

- **Scale-to-zero**: Most tenant databases are idle most of the time. Set `suspend_timeout_seconds` aggressively (300s for free, 60s-300s for paid).
- **Autoscaling caps**: Set `autoscaling_limit_max_cu` per project to prevent runaway compute. Free: max 2 CU. Paid: up to 16 CU.
- **Per-project quotas**: Configure `active_time_seconds`, `logical_size_bytes`, `compute_time_seconds`, `data_transfer_bytes` on project creation or via PATCH.
- **Branch cleanup**: Delete old dev branches and orphaned `(old)` branches after restores.
- **Snapshot rotation**: Delete snapshots no longer needed; use `expires_at` for automatic cleanup.

### Consumption tracking

- On **usage-based plans** (Launch, Scale, Agent, Enterprise), use `GET /api/v2/consumption_history/v2/projects` for metrics that match billing (`compute_unit_seconds`, branch storage byte-hours, transfer bytes, etc.). Legacy endpoints return different fields, see Neon’s consumption docs.
- Poll about every **15 minutes** (Neon’s update cadence). Polling does **not** wake suspended computes.
- Alert users at **80%** and **95%** of their quota to prevent unexpected suspension.
- Routing index (Auth `/users` vs Postgres roles vs consumption): [REST_API_META.md](https://github.com/neondatabase/neon-for-agent-platforms/blob/main/docs/REST_API_META.md) in this repo.

**Links:** [Agent Plan](https://neon.com/docs/introduction/agent-plan.md) · [Consumption metrics](https://neon.com/docs/guides/consumption-metrics.md) · [Consumption limits](https://neon.com/docs/guides/consumption-limits.md) · [Cost optimization](https://neon.com/docs/introduction/cost-optimization.md)

---

## Agent Plan features summary

Use when a partner asks **what's included** in the Agent Plan. Do not invent numbers, verify against live docs. This summary reflects the docs as of April 2026.

### Database and compute

- Up to **30,000 projects per org** (can request increases via [agents@neon.tech](mailto:agents@neon.tech))
- **25 branches per project** (paid), 10 (free)
- Autoscaling up to **16 CU** (paid) or **2 CU** (free); fixed computes up to 56 CU on paid
- **Scale to zero**: configurable 1 min to always-on (paid); 5 min fixed (free)
- Up to **16 TB** logical data per branch

### APIs and services

- **Neon Auth**: managed auth built on Better Auth (up to 1M MAU on paid)
- **Data API**: PostgREST-compatible REST API for direct DB access
- **Management API**: full CRUD for projects, branches, snapshots, quotas
- **Higher rate limits** on both Management and Data APIs for Agent Plan

### Versioning and recovery

- **Instant restore** with up to **30-day** restore window (paid), 1-day (free)
- **10 manual snapshots** per project (paid), 1 (free)
- Copy-on-write branching for dev environments

### Security and compliance

- SOC 2, SOC 3, ISO 27001, ISO 27701, GDPR, CCPA
- **HIPAA** included (contact Neon to enable)
- **Protected branches**, **IP Allow**, **Private Networking** (AWS PrivateLink)

### Monitoring

- **14-day** monitoring retention (paid)
- **Metrics/logs export** to Datadog or OTel-compatible platforms

### Not included

- **Automated backup schedules** are not available on the Agent Plan (build your own via API)

**Links:** [Plans](https://neon.com/docs/introduction/plans.md) · [Agent Plan](https://neon.com/docs/introduction/agent-plan.md)