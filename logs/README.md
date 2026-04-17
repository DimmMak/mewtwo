# 📝 logs/

Per-run audit trails. Each Mewtwo run writes a permanent log here following `schemas/LOG.template.md`.

**Format:** `mewtwo-{YYYY-MM-DDTHH-MM}.md`

**Gitignore rule:** logs are personal — gitignored by default. If you want to publish a specific run (e.g., as a debugging example), manually remove its gitignore entry.

**Retention:** never delete. Logs are the audit trail that makes durability real.
