# 🤝 Contributing to mewtwo

---

## 🧱 Adding a new skill to the orchestrator

1. Build your `.skill` following all 5 [contract rules](CONTRACT.md)
2. Add a registry entry to `SKILLS_REGISTRY.md` using [`schemas/SKILL_ENTRY.template.md`](schemas/SKILL_ENTRY.template.md)
3. Run `/mewtwo --validate` to confirm contract compliance
4. Submit PR

The registry entry is what makes your skill orchestrable. Without it, Mewtwo can't see your skill (by design — registration is consent).

---

## 🚨 Adding a new failure mode

When you encounter a real failure pattern:
1. Add an entry to `FAILURE_MODES.md` following the `F##` format
2. Document: pattern, why, detection, recovery, don't
3. PR it — failure documentation is durability work

---

## 📋 Adding a new schema

If you find a gap in the orchestration format (e.g., need a new type of log entry, new plan subtype):
1. Create `schemas/{NAME}.template.md` following existing templates
2. Update `SKILL.md` to reference it
3. PR with rationale

---

## 🐛 Bug reports

Open an issue with:
- Run ID where the bug appeared
- Relevant log excerpt (`logs/{run-id}.md`)
- Expected vs actual behavior
- Contract compliance status of involved skills

---

## 📜 Code style

- All docs are markdown with functional emojis paired
- Registry entries must be complete (all 4 contract items, honest risk tier)
- Never remove failure mode entries (only add)
- Never delete log files (they're the permanent audit trail)

---

## 🚫 We reject PRs that...

- Break the contract (add silent writes, swallow errors, etc.)
- Lock Mewtwo into a specific data format (DB, proprietary encoding)
- Remove the confirmation gates (🟡 and 🔴 tiers are sacred)
- Hide skills from the registry (registration IS consent)
- Introduce circular dependencies between core docs

---

🧬🃏 Thanks for contributing.
