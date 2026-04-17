# 🚨 Failure Modes Catalog

> Known ways things go wrong in multi-skill orchestration, with the standard recovery path for each.
>
> This file is mandatory reading for Mewtwo at the start of every run. Patterns are added here as real failures are encountered.

---

## 🛑 F01 — Missing skill in registry

**Pattern:** Mewtwo generates a plan that references a skill not declared in `SKILLS_REGISTRY.md`.

**Why it happens:** User installed a skill without updating the registry, or registry is stale.

**Detection:** Plan generation step — skill name doesn't match any registry entry.

**Recovery:**
1. Refuse to execute the plan.
2. Tell the user which skill is missing.
3. Offer: "Add entry to registry now? [y/n]" — if y, prompt for purpose/inputs/outputs/risk/contract.
4. Re-validate registry, then retry plan.

**Don't:** silently skip the step, or try to guess the skill's behavior.

---

## 🛑 F02 — Contract declaration missing

**Pattern:** A skill in the registry has an entry but missing one or more of the 4 contract checkmarks.

**Why it happens:** Entry was added in a rush, or skill's behavior changed and contract wasn't re-verified.

**Detection:** Pre-flight contract check fails for that entry.

**Recovery:**
1. Refuse to include the skill in any plan.
2. Report: `🛑 CONTRACT VIOLATION — {family}:{skill} missing: reports / archives / fails-loud / reversible`
3. Suggest: either update the entry to declare compliance, OR invoke the skill manually outside Mewtwo (bypasses safety).

**Don't:** trust a skill without declared compliance.

---

## 🛑 F03 — Mid-run skill failure

**Pattern:** Step N fails (file not found, API error, permission denied, etc.) while steps N+1 through M are still pending.

**Why it happens:** Any runtime error from a skill.

**Detection:** The failing skill returns an error per F04 (fail-loud rule).

**Recovery:**
1. **STOP the chain immediately.** Do not run N+1.
2. Log the error with full context.
3. Show user:
   ```
   🛑 Step N failed: {error}
   Steps 1..N-1 completed.
   Steps N+1..M are pending.
   
   Options:
     a) Retry step N
     b) Skip step N, continue with N+1
     c) Abort run (partial state preserved in log)
     d) Show error details
   ```
4. Wait for explicit user choice. Never continue silently.

---

## 🛑 F04 — Silent skill success (contract violation at runtime)

**Pattern:** A skill returns "done" but produced no visible report.

**Why it happens:** Bug in the skill, or skill was invoked incorrectly.

**Detection:** Mewtwo expects a report after every skill call. Absence = violation.

**Recovery:**
1. Log as a contract violation.
2. Refuse to treat the step as complete.
3. Ask user to investigate the skill or re-run manually.
4. Optionally downgrade the skill's trust in the registry (mark "🟡 unstable").

---

## 🛑 F05 — Plan approved but user changes mind mid-execution

**Pattern:** User said "y" at plan stage, but cancels at a 🔴 per-action gate.

**Why it happens:** User realizes they don't want the irreversible action after all.

**Detection:** User answers "n" to a 🔴 confirmation.

**Recovery:**
1. Skip the 🔴 step.
2. Ask: "Continue with remaining steps, or abort run?"
3. If continue → remaining steps proceed (partial run).
4. If abort → stop, log partial state, show what was completed + what's reversible.

**Don't:** force the user to commit to the whole plan. Per-action gates are the whole point.

---

## 🛑 F06 — Circular dependency between skills

**Pattern:** Plan has skill A's output feeding into skill B, but B's output also feeds A. Infinite loop potential.

**Why it happens:** Mewtwo's decomposition produced a cycle (rare but possible).

**Detection:** Pre-execution graph check on the plan.

**Recovery:**
1. Refuse to execute.
2. Show the cycle explicitly: `A → B → A`
3. Ask user to choose: "break the cycle by skipping which step?"

---

## 🛑 F07 — Registry and reality diverged

**Pattern:** Registry says skill X produces files at path `Y/`, but skill actually writes to path `Z/`.

**Why it happens:** Skill evolved, registry wasn't updated.

**Detection:** Post-execution artifact check — files expected at declared paths aren't there, OR are at different paths.

**Recovery:**
1. Log the discrepancy.
2. Show user: "registry says {path}, found at {actual_path}"
3. Offer: "Update registry entry to match? [y/n]"

---

## 🛑 F08 — Two skills modify the same file concurrently

**Pattern:** Step N and Step M both try to write to `target.md` in the same run.

**Why it happens:** Decomposition error, or user explicitly asked for it.

**Detection:** Pre-execution check for overlapping output paths.

**Recovery:**
1. Flag at plan stage — don't wait for execution to fail.
2. Show user: "Steps {N} and {M} both write to {path}. This will cause one to overwrite the other."
3. Ask: serialize them (N then M) / only run one / abort plan?

---

## 🛑 F09 — Archive directory full or unwritable

**Pattern:** Skill tries to archive original, but archive location fails.

**Why it happens:** Disk full, permissions changed, directory renamed.

**Detection:** Archive write returns error.

**Recovery:**
1. **DO NOT overwrite the original.** Contract requires archive-before-overwrite.
2. Abort the step.
3. Report: "Could not archive to {path}, so refusing to modify {target}."
4. Suggest fix (free disk space, fix permissions).

---

## 🛑 F10 — User asked for something outside the skill library's capabilities

**Pattern:** Request like "build me a custom iOS app" — no skill covers this.

**Why it happens:** Mewtwo's library is bounded by the skills in the registry.

**Detection:** Plan generation can't find a skill match for all sub-tasks.

**Recovery:**
1. Identify which sub-tasks are covered vs uncovered.
2. Report: "I can do {A, B, C} of this request. Missing skills: {D, E}."
3. Offer: "Proceed with partial? / Skip and suggest skill specs? / Abort?"

---

## ➕ Adding new failure modes

When a new failure pattern is encountered IRL:
1. Add a new `F##` entry following the format
2. Document: pattern, why, detection, recovery, don't
3. Commit the update to this file

Failure modes compound into reliability over time. **Every documented failure is a future success.**

🚨🛡️
