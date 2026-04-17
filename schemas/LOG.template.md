# 📝 Mewtwo Run Log

> **Run ID:** {RUN_ID}
> **Started:** {YYYY-MM-DDTHH:MM:SS}
> **Ended:** {YYYY-MM-DDTHH:MM:SS}
> **Duration:** {N} minutes
> **Final state:** SUCCESS / PARTIAL / FAILED

---

## Intent (as received)

> {exact user input that triggered this run}

---

## Plan (as generated)

{Full plan from PLAN.template.md embedded here}

---

## User Confirmations

| Step | Action | Tier | User response | Timestamp |
|------|--------|------|---------------|-----------|
| Plan | Confirm orchestration plan | 🟡 | y | {HH:MM:SS} |
| 3 | Write to {path} | 🟡 | (covered by plan confirmation) | — |
| 5 | Ship .skill to ~/.claude/skills/ | 🔴 | y | {HH:MM:SS} |

---

## Execution Trace

### Step 1: `family:skill` — {action} 🟢
- **Started:** {HH:MM:SS}
- **Completed:** {HH:MM:SS}
- **Result:** {report from the skill}
- **Files touched:** {list}
- **Archived originals:** {list of archive paths}

### Step 2: `family:skill` — {action} 🟡
- **Started:** {HH:MM:SS}
- **Completed:** {HH:MM:SS}
- **Result:** {report}
- **Files touched:** {list}
- **Archived originals:** {list}

### Step N: ...

---

## Artifacts Produced

### Files written / modified
- `{path1}` — {1-line description}
- `{path2}` — {description}

### Files archived (originals preserved at)
- `{path1}` → `{archive path1}`
- `{path2}` → `{archive path2}`

### New skills shipped
- `{skill path}` — {1-line description}

---

## Errors (if any)

### At step {N}: `family:skill`
- **Error message:** {exact}
- **Context:** {what was happening}
- **User choice:** Retry / Skip / Abort / Detail
- **Recovery action taken:** {what actually happened next}

---

## Reversal Instructions

If the user later wants to undo this run:

1. {step-by-step undo for file writes — paths to archives}
2. {step-by-step undo for new skills shipped — rm paths}
3. {step-by-step undo for external side effects — if any}

If any action was IRREVERSIBLE, note it here:
- ⚠️ {action} cannot be undone because {reason}

---

## Post-run Validation

- [ ] All declared artifacts exist at stated paths
- [ ] All archived originals exist at stated paths
- [ ] No orphaned temp files
- [ ] Contract compliance maintained across run
