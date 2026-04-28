# 🧬 mewtwo — Architecture

> Why mewtwo is structured the way it is. Each section answers a "why this and not that" question.

---

## Why an orchestrator at all?

Skills are sharp tools. A skill that ships a memo is good at shipping memos; a skill that runs a stock-analysis committee is good at that. But a real user task ("rumble AAPL, draft a memo, drop it in the vault") spans 3 skills — and the user shouldn't have to manually wire one's output to the next.

mewtwo is the wiring layer. It takes high-level intent ("AAPL memo, end-to-end"), decomposes it into sub-tasks, picks skills from the registry, plans the execution, confirms once, then runs.

**The alternative considered and rejected:** chained slash-commands (`.rumble AAPL && .journalist memo AAPL && .ideas append`). Three problems:
1. Each skill has its own confirmation flow — chained calls re-prompt for the same approval
2. Failure handling is per-skill, not per-chain — failures cascade silently
3. No audit trail spans the chain — debugging mid-pipeline failures requires re-running each skill manually

mewtwo solves all three: one confirmation, one failure handler, one audit log.

---

## Why durability-first design?

The system needs to make sense in 10 years. That ruled out:

- Binary state (databases, pickles, cached objects) — opaque, lock-in, lost when the tool changes
- Tightly coupled skills — a registry entry is the API surface; skills can change internals freely
- Magic conventions — every rule is in `CONTRACT.md` or `FAILURE_MODES.md`; nothing is "just understood"

What's in: plain markdown logs, a flat registry file, explicit contract checkmarks, append-only audit trails.

If mewtwo disappears tomorrow, the registry + logs + skills are still legible. **Plain text wins time.**

---

## Why tiered automation (🟢/🟡/🔴)?

Confirmation fatigue kills automation. If every step requires a `y`, users stop reading and rubber-stamp; if no step requires confirmation, users wake up to surprises. The middle path: tier by reversibility.

| Tier | Meaning | Confirmation policy |
|---|---|---|
| 🟢 | Read-only OR skill-managed scratch writes | Auto-execute, log result |
| 🟡 | Writes to user-visible project files | One confirmation covers all 🟡 in a plan |
| 🔴 | Irreversible (git push, external API, `~/.claude/skills/` writes) | Per-action explicit `y` required |

This collapses N confirmations into 1 (for medium-risk batched work) but preserves K explicit confirmations for the things that actually need them. Confirmation budget gets spent where it matters.

---

## Why contract enforcement (and why soft-fail at scale)?

v0.2.0 had strict enforcement: any skill missing a contract checkmark → mewtwo refuses to call it. At fleet scale (47+ skills), most legitimate skills lacked full declarations — mewtwo became unusable.

v0.2.1 fix: soft-fail. Three states:
1. **All 4 checkmarks present** → proceed
2. **1-3 checkmarks missing** → emit WARNING + require `--allow-uncontracted` flag
3. **Skill not in registry at all** → hard refuse (preserved from v0.2)

The third state is the real wall. Anything else is a graduated-response. Pattern matches the broader fleet principle: enforce structurally where you can, surface clearly where you can't.

---

## Why "loud" failure (and why the 3-channel definition)?

v0.2.0 said "every failure is loud." But "loud" was undefined — was it a tone? An emoji? A log entry?

v0.2.1 fix (per forensic HIGH #1): three required channels.

1. **Final-line emit** — last visible line of the response is `🛑 FAILED at Step N: {skill:action}`. The user can't scroll past it.
2. **Banner block** — distinct visual format with 🛑 header + recovery options table.
3. **Log marker** — `logs/{RUN_ID}.md` final line is `STATUS: FAILED` (or `PARTIAL`). Greppable across all logs.

If any channel is missing, the failure is silent by definition. Loudness is an output-shape contract, not a vibe.

---

## Why self-validate at PRE-FLIGHT?

The original v0.2 contract enforcement audited every other skill but exempted mewtwo itself. The orchestrator could violate its own contract without anyone noticing — a self-reference paradox.

v0.2.1 fix (per forensic CRIT #2): a self-entry in `SKILLS_REGISTRY.md` declaring all 4 checkmarks for mewtwo. STEP 0 PRE-FLIGHT runs `mewtwo:self-validate` against that entry BEFORE reading any other registry entries. If a checkmark is missing, mewtwo refuses to orchestrate.

The auditor must clear its own bar before policing others. Same pattern shipped in `snes-fit/dimensions/00_self/` for parallel reasons.

---

## Why append-only logs?

Logs are forensic evidence. If mewtwo writes a log entry then later edits it, the audit trail is gone. Every line in `logs/{RUN_ID}.md` is committed at the moment it happens; nothing rewrites it.

Failure case: a step fails mid-execution. mewtwo writes `STEP N: FAILED` and stops. The user retries, succeeds. The log shows BOTH the failure and the retry — not a clean "always succeeded" rewrite. That's the value: future-you (or a debugging agent) can see what actually happened.

---

## Why this isn't `.home`

`.home` routes between domains: fund / learning / general / desk. mewtwo orchestrates within a task: skill-A → skill-B → skill-C.

If you ask mewtwo "what's on my plate today across the fund and learning work?" — that's a `.home` question. Wrong tool.
If you ask `.home` "rumble AAPL, write the memo, append to vault" — that's a mewtwo question. Wrong tool.

The two compose: `.home` shows you the fund domain, you invoke `.mewtwo [intent]` for a multi-skill task within it.

---

## Decision history

| 🟣 Decision | 🟣 Date | 🟣 Outcome |
|---|---|---|
| Build mewtwo as orchestrator family | 2026-04 | Built |
| Contract-checkmarks per-skill enforcement | 2026-04 | Built (v0.2.0 strict; v0.2.1 soft-fail) |
| Tiered automation 🟢/🟡/🔴 | 2026-04 | Built |
| Append-only log discipline | 2026-04 | Built |
| Self-validate at PRE-FLIGHT | 2026-04-28 | Built (forensic CRIT #2 fix) |
| Soft-fail for partial-contract skills | 2026-04-28 | Built (forensic HIGH #3 fix) |
| Loud-failure 3-channel definition | 2026-04-28 | Built (forensic HIGH #1 fix) |
| Worked example in SKILL.md | 2026-04-28 | Built (forensic MED #2 fix) |
| `.mewtwo` dot-prefix as canonical (was `/mewtwo`) | 2026-04-28 | Standardized (forensic MED #1 fix) |
