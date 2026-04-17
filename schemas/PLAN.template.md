# 🧬 Mewtwo Orchestration Plan

> **Run ID:** {RUN_ID}
> **Created:** {YYYY-MM-DDTHH:MM}
> **Intent:** {1-sentence restatement of what the user asked for}

---

## Decomposition

{2-4 sentences describing how the intent breaks into sub-tasks}

---

## Execution Plan

| # | Tier | Skill | Action | Inputs | Outputs |
|---|------|-------|--------|--------|---------|
| 1 | 🟢 | `family:skill` | {what it does this run} | {files / data} | {what it produces} |
| 2 | 🟢 | `family:skill` | {...} | {...} | {...} |
| 3 | 🟡 | `family:skill` | {...} | {...} | writes to `{path}` |
| 4 | 🔴 | `family:skill` | {...} | {...} | IRREVERSIBLE: {detail} |

---

## Risk Budget

```
🟢 auto-execute:        {N} steps    (no confirmation)
🟡 confirmed batch:     {M} steps    (one confirmation covers all)
🔴 per-action confirm:  {K} steps    (each requires explicit y)
```

---

## Estimated Time

~{minutes} total

---

## Artifacts Expected

After successful run, the following artifacts will exist:

- `{path1}` — {description}
- `{path2}` — {description}
- `logs/{RUN_ID}.md` — full audit trail

---

## Contract Compliance Check

All skills in this plan have been verified against `CONTRACT.md`:

- [ ] family:skill-1 → ✓ declared
- [ ] family:skill-2 → ✓ declared
- [ ] family:skill-3 → ✓ declared
- [ ] family:skill-4 → ✓ declared

---

## User Decision

```
Confirm plan as-is?              [y]
Abort (nothing will run)?        [n]
Modify before running?           [m]
Show more detail on a step?      [?{step-number}]
```

---

## Plan State

- [x] Created
- [ ] Confirmed by user
- [ ] Executed
- [ ] Archived to logs
