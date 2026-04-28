# 🧬 mewtwo — Schema

> Schemas mewtwo reads or writes. Stable across minor versions; breaking changes require a major-version bump.

**Schema version:** 0.1.0
**Last reviewed:** 2026-04-28

---

## Run log (`logs/{RUN_ID}.md`)

Append-only markdown audit trail. One file per run. Path format: `logs/mewtwo-{YYYY-MM-DDTHH-MM-SS}.md`.

**Required sections** (in order, per `schemas/LOG.template.md`):

1. **Frontmatter block** — Run ID, started/ended timestamps, duration, final state (`SUCCESS` / `PARTIAL` / `FAILED`)
2. **Intent (as received)** — exact user input that triggered the run
3. **Plan (as generated)** — full plan from `schemas/PLAN.template.md` embedded
4. **User Confirmations** — table of (step, action, tier, response, timestamp)
5. **Execution Trace** — per-step block: started, completed, result, files touched, archived originals
6. **Artifacts Produced** — files written/modified, files archived, new skills shipped
7. **Errors (if any)** — error message, context, user choice (Retry/Skip/Abort/Detail), recovery action taken
8. **Reversal Instructions** — step-by-step undo paths; ⚠️ flag for irreversible actions
9. **Post-run Validation** — checklist confirming declared artifacts/archives exist

**Final-line invariant:** `STATUS: SUCCESS` / `STATUS: PARTIAL` / `STATUS: FAILED`. This is the greppable marker for cross-log audits.

---

## Plan (`schemas/PLAN.template.md`)

The plan emitted at STEP 2 (PLAN GENERATION). Embedded in the log file before user confirmation.

**Required sections:**

1. Run ID, created timestamp, intent (1-sentence restatement)
2. **Decomposition** — 2-4 sentences mapping intent to sub-tasks
3. **Execution Plan** — table of (#, tier, skill, action, inputs, outputs)
4. **Risk Budget** — count of 🟢 auto-execute, 🟡 confirmed batch, 🔴 per-action
5. **Estimated Time** — `~{minutes} total`
6. **Artifacts Expected** — paths the plan promises to produce
7. **Contract Compliance Check** — checklist of every skill referenced; all 4 checkmarks declared, OR explicit WARNING with `--allow-uncontracted`
8. **User Decision** — `y` / `n` / `m` (modify) / `?{step-number}` (detail)
9. **Plan State** — created → confirmed → executed → archived

---

## Registry entry (`schemas/SKILL_ENTRY.template.md`)

Every skill in `SKILLS_REGISTRY.md` follows this shape.

**Required fields:**

- **Purpose** — 1 sentence
- **Inputs** — what the skill consumes (files, user text, other skill outputs)
- **Outputs** — exact file paths where applicable
- **Risk tier** — 🟢 / 🟡 / 🔴 (per the tier rules in CONTRACT.md and ARCHITECTURE.md)
- **Contract** — 4 checkmarks: ✓ reports, ✓ archives, ✓ fails-loud, ✓ reversible

**Validation rules** (enforced by `mewtwo --validate`):

1. All 4 checkmarks must be present (✓) or explicitly broken (✗ with reason)
2. Risk tier must be exactly one of 🟢 🟡 🔴
3. Inputs and outputs must be non-empty
4. Purpose must be a single sentence (no paragraphs)
5. Skill name must be kebab-case
6. Family block must exist with both Repo and Role

Failure of any rule flags the skill as unsafe for orchestration (hard-refuse path).

---

## Self-validate output

Emitted by `mewtwo:self-validate` (run at STEP 0 PRE-FLIGHT before any other registry read).

**Output shape:**

```
PASS — mewtwo self-contract intact (4/4 checkmarks)
```

OR

```
🛑 SELF-CONTRACT VIOLATION
   mewtwo missing: {comma-separated list of failing checkmarks}
   Cannot orchestrate until self-contract is restored.
   Fix: edit SKILLS_REGISTRY.md → mewtwo entry → restore the missing ✓ marks.
```

The PASS path emits to stdout; the violation path emits to stderr and exits with non-zero code.

---

## Versioning policy

- **Patch (0.1.x → 0.1.y)**: bug fixes, no schema changes
- **Minor (0.x.0 → 0.(x+1).0)**: additive fields (new optional sections)
- **Major (x.0.0 → (x+1).0.0)**: breaking changes (renames, removals, required-field additions)

Bump `schema_version` at the top of this file when any schema changes. Skills that consume mewtwo's logs (none currently) should pin their expected schema version.
