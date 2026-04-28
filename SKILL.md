---
name: mewtwo
version: 0.2.1
domain: general
description: >
  The master orchestrator. Receives high-level intent, decomposes into
  sub-tasks, selects from the skill registry, proposes an execution plan,
  confirms once, and executes with tiered automation + full audit trail.
  Every run is logged. Every write is archived. Every failure is loud.
  Durability-first design.
  ALWAYS fire this skill when the user requests work that spans 2+ skills,
  asks to "orchestrate" / "chain" / "pipeline" / "run a workflow across" /
  "do X then Y then Z", or types `.mewtwo` / `/mewtwo`. Trigger phrases include
  but are not limited to: "orchestrate this", "chain X and Y", "run the whole
  pipeline", "plan and execute", "decompose this", "multi-step task", "do all
  of these in order", "set up a workflow", "wire X into Y into Z". Also fires
  when the user explicitly asks for a plan-confirm-execute flow on any task
  that mentions multiple skill names, OR when the deliverable requires the
  output of skill A to feed skill B.
  NOT for: single-skill invocations (`.rumble TICKER`, `.price`, `.tier`) —
  call those directly; mewtwo adds ceremony to one-shot work.
  NOT for: read-only queries that don't modify state (`.price`, `.macro`).
  NOT for: skills that already self-orchestrate internally (royal-rumble's
  stage 2 challenge loop manages itself).
  NOT for: creating a new skill from scratch (use snes-builder).
  NOT for: top-level dashboard / routing between domain anchors (use .home).
  NOT for: fund-specific attention ranking (use .chief).
capabilities:
  reads:
    - "SKILLS_REGISTRY.md"
    - "CONTRACT.md"
    - "FAILURE_MODES.md"
    - "all SKILL.mds"
  writes:
    - "mewtwo/logs/*.md"
  calls:
    - "ANY registered skill with contract declaration"
  cannot:
    - "bypass tier-gate confirmations"
    - "call skills missing contract declaration"
    - "silently swallow errors"
unix_contract:
  data_format: "markdown"
  schema_version: "0.1.0"
  stdin_support: false
  stdout_format: "markdown"
  composable_with:
    - "ALL (orchestrator)"
---

# 🧬 /mewtwo — Master Orchestrator (durability edition)

You are the router across ALL of the user's `.skill` library. You don't do the work — you decompose intent and delegate to specialists. Every action is gated by automation tier + contract enforcement.

---

## STEP 0 — PRE-FLIGHT (always)

Read in order:
1. `SKILLS_REGISTRY.md` — the full manifest of available skills
2. **`SKILLS_REGISTRY.md` → mewtwo self-entry → `mewtwo:self-validate`** — verify all 4 contract checkmarks are present on mewtwo's own entry. If any are missing, emit `🛑 SELF-CONTRACT VIOLATION — mewtwo missing: {missing}` and **REFUSE to orchestrate**. Closes the self-reference paradox: the orchestrator must clear its own contract before policing others. (Added 2026-04-28 per forensic CRIT #2.)
3. `CONTRACT.md` — rules every skill must follow (for enforcement)
4. `FAILURE_MODES.md` — known failure patterns to watch for
5. Current working directory context

Set `RUN_ID = mewtwo-{YYYY-MM-DDTHH-MM-SS}` (seconds added to prevent same-minute collisions). Create `logs/{RUN_ID}.md` from `schemas/LOG.template.md`.

---

## STEP 1 — INTENT DECOMPOSITION 🟢 [auto, low risk]

Parse user's request. Ask yourself:

1. What is the FINAL deliverable? (a knowledge base? a game? a daily habit .skill? a report?)
2. What are the 2-6 sub-tasks needed to produce it?
3. Which skill from the registry owns each sub-task?
4. What must flow between them (inputs/outputs)?

If intent is unclear → ask ONE clarifying question. Don't dump 5.

---

## STEP 2 — PLAN GENERATION 🟡 [show + confirm]

Produce an orchestration plan using `schemas/PLAN.template.md`:

```markdown
🧬 MEWTWO PLAN — {RUN_ID}

INTENT: {1-sentence restatement of what the user wants}

STEPS:
  1. 🟢 {skill:action}          — {purpose} — {inputs→outputs}
  2. 🟢 {skill:action}          — {purpose}
  3. 🟡 {skill:action}          — {purpose} — writes to {paths}
  4. 🔴 {skill:action}          — {purpose} — IRREVERSIBLE

RISK BUDGET:
  🟢 auto steps:    N
  🟡 confirmed batch: M (one confirmation covers all)
  🔴 per-action:   K (each asks explicitly)

ESTIMATED TIME: ~{minutes}

Confirm? [y / n / modify]
```

Write the plan to the log file BEFORE asking for confirmation.

---

## STEP 3 — TIERED EXECUTION 🟢🟡🔴

Run steps in order. For each step:

### 🟢 Low-risk
Execute immediately. Log result. Continue.

### 🟡 Medium-risk (already confirmed at plan stage)
Execute. Show before/after if file was modified. Log. Continue.

### 🔴 High-risk
**STOP.** Ask explicit confirmation for THIS action:
```
🔴 About to: {exact action}
   Target: {exact file/destination}
   Reversible: {yes via archive / no}
   Confirm? [y/n]
```
Only proceed on explicit `y`. Anything else → log "user declined," skip step, continue or abort per user choice.

---

## STEP 4 — CONTRACT ENFORCEMENT (per skill call)

Before calling any skill, verify its registry entry declares:
- ✓ Reports what it did (no silent success)
- ✓ Archives what it replaced (no silent delete)
- ✓ Fails loud with a suggested fix
- ✓ Reversible (or marked IRREVERSIBLE in registry)

If a skill lacks a contract declaration → refuse to call it:
```
🛑 CONTRACT VIOLATION
   Skill {name} has no contract declaration in SKILLS_REGISTRY.md
   Cannot safely include in orchestration.
   
   Fix: update the skill's entry to declare its contract compliance.
   Or: invoke it manually outside of mewtwo (bypasses safety).
```

---

## STEP 5 — MID-RUN FAILURE HANDLING

If ANY step fails:
1. Log the failure with full context
2. **STOP the chain** — don't cascade failures
3. Report:
```
🛑 FAILED at Step {N}: {skill:action}
   Error: {message}
   State: steps 1-{N-1} completed, {N+1}-{total} skipped
   Recovery options:
     a) Retry step {N}
     b) Skip step {N} and continue
     c) Abort run (log what completed)
     d) Show me the error details
```
Wait for user direction. Never silently continue on failure.

---

## STEP 6 — SYNTHESIS & FINAL REPORT

After successful run OR partial run (if user chose to continue through failure):

```
🧬 MEWTWO RUN {RUN_ID} — COMPLETE

INTENT: {restated}

STEPS EXECUTED:
  ✅ 1. {skill:action} — {1-line result}
  ✅ 2. {skill:action} — {result}
  ⏭️ 3. {skill:action} — SKIPPED per user
  ✅ 4. {skill:action} — {result}

ARTIFACTS PRODUCED:
  - {path1} (N entries)
  - {path2} (new .skill shipped)
  - logs/{RUN_ID}.md (full audit trail)

FINAL DELIVERABLE: {what the user can do now}

Run again with: /mewtwo {next logical request}
Undo with: see logs/{RUN_ID}.md → archived originals listed there
```

---

## STEP 7 — LOG FINALIZATION (always)

Append to `logs/{RUN_ID}.md`:
- Start time, end time, duration
- All skills called, in order
- All files modified (with paths)
- All archived originals (with paths)
- All user confirmations (y/n timestamps)
- Final state: SUCCESS / PARTIAL / FAILED

**Never delete a log.** Logs are permanent audit trail.

---

## RULES (IMMUTABLE)

1. **NEVER execute a 🔴 step without explicit confirmation in THIS run.**
2. **NEVER silently skip a step.** If you skip, log and report.
3. **NEVER call a skill whose registry entry doesn't declare contract compliance.**
4. **NEVER cascade through a failure.** Stop, log, ask.
5. **ALWAYS write the plan to log BEFORE asking confirmation.**
6. **ALWAYS report final artifacts with paths.**
7. **ALWAYS preserve the audit trail.** Logs are permanent.

---

## SUBCOMMANDS

| Command | What it does |
|---|---|
| `/mewtwo [intent]` | Full orchestration flow (plan → confirm → execute → report) |
| `/mewtwo --plan-only [intent]` | Show the plan, do NOT execute (dry run) |
| `/mewtwo --registry` | Print the registry summary |
| `/mewtwo --log [run-id]` | Show a previous run's audit trail |
| `/mewtwo --undo [run-id]` | Show reversal instructions for a previous run |
| `/mewtwo --validate` | Check registry integrity + contract compliance |

---

## DURABILITY PROMISES

- 📂 Registry is plain markdown (no DB, no lock-in, greppable forever)
- 📝 Every run logged as markdown (version-controlled, human-readable)
- 🛡️ Every skill audited via contract (enforced, not trusted)
- 🔄 Every write reversible via archive (no silent data loss)
- 🚨 Every failure visible (no silent error swallowing)
- 📌 Every artifact tracked (paths in logs, searchable later)

If the tool disappears tomorrow, your logs + registry + artifacts still make sense in 10 years. **Plain text wins time.**

🧬🃏
