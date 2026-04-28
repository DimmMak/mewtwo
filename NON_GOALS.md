# 🧬 mewtwo — Non-Goals

What mewtwo will **never** do. Each entry is a structural commitment, not a preference.

| 🟣 # | 🟣 Non-goal | 🟣 Why |
|---|---|---|
| 1 | No skill execution without contract validation | Per `CONTRACT.md` — skills missing all 4 checkmarks emit WARNING (soft-fail) or hard-refuse (no registry entry at all). Closes the assert-vs-enforce gap. |
| 2 | No 🔴 step without explicit per-action confirmation | Tiered automation is the whole point. 🔴 = irreversible; one batch confirmation never covers a 🔴. |
| 3 | No silent step skip | Every skip is logged + reported. If mewtwo skips a step, the user sees it in the final synthesis block. |
| 4 | No silent error swallowing | Every failure is loud per the 3-channel definition (final-line emit + 🛑 banner + `STATUS: FAILED` log marker). All three required, not just one. |
| 5 | No registry-bypass execution | Skills not in `SKILLS_REGISTRY.md` cannot be orchestrated. Add an entry first, then call. |
| 6 | No log deletion or rewrite | Logs are permanent audit trail. Append-only by design. Even partial-failure runs preserve their state. |
| 7 | No cascade through failure | If step N fails, mewtwo STOPs the chain. Does not run N+1 silently. User chooses retry / skip / abort. |
| 8 | No self-validation skip | At STEP 0 PRE-FLIGHT, mewtwo runs `mewtwo:self-validate` against its own registry entry. If self-contract is broken, mewtwo refuses to orchestrate. Closes the self-reference paradox. |
| 9 | No top-level domain routing | mewtwo is not `.home`. Routing between fund / learning / general anchors lives elsewhere. mewtwo is the orchestrator within a domain, not across them. |
| 10 | No skill creation | Building a new skill is `snes-builder`. mewtwo orchestrates existing skills; it does not author them. |
| 11 | No cross-skill memory state | Each run is self-contained. mewtwo does not remember prior runs except via the audit log. State lives in skills, not in mewtwo. |
| 12 | No external network calls | mewtwo orchestrates local skills. Skills themselves may call APIs (per their own contracts); mewtwo's coordination layer is offline. |
| 13 | No undo of irreversible actions | If a step is 🔴 IRREVERSIBLE (e.g., `git push origin main`), mewtwo cannot undo it. Documented in the run log under "Reversal Instructions" with the constraint stated explicitly. |
| 14 | No ANY skill call without registry entry | Even partial-contract skills (with WARNING flag) require a registry entry. The entry IS the integration point — without it, mewtwo cannot reason about the skill. |

These are invariants. Changing any of them requires a major version bump and a migration plan.
