# 📜 mewtwo Changelog

## v0.2.0 — 2026-04-18

**World-Class Overhaul shipped.** Part of the fleet-wide upgrade to tree+plugin+unix architecture.

- 🌳 **Tree:** `domain:` field added to frontmatter (general)
- 🎮 **Plugin:** `capabilities:` block declares reads / writes / calls / cannot
- 🐧 **Unix:** `unix_contract:` block declares data_format / schema_version / stdin_support / stdout_format / composable_with
- 🛡️ Schema v0.3 validation required at install (via `future-proof/scripts/validate-skill.py`)
- 🔗 Install converted to symlink pattern (kills drift between Desktop source and live install)
- 🏷️ Tagged at `v-2026-04-18-world-class` for rollback

See `memory/project_world_class_architecture.md` for the full model.

---


## v1.0.0 — 2026-04-16 — Initial Release

### 🧬 Shipped
- **`SKILL.md`** — the orchestrator with 7-step execution flow:
  - STEP 0: Pre-flight (load registry, contract, failure modes)
  - STEP 1: Intent decomposition 🟢
  - STEP 2: Plan generation 🟡 (confirmation gate)
  - STEP 3: Tiered execution 🟢🟡🔴
  - STEP 4: Contract enforcement (per skill call)
  - STEP 5: Mid-run failure handling (stop, don't cascade)
  - STEP 6: Synthesis & final report
  - STEP 7: Log finalization

- **`SKILLS_REGISTRY.md`** — manifest seeded with 22 skills across 7 families:
  - claudenotes (9), cowatch (2), courserafied (5), transmute (2), promptlatro (1), royal-rumble (1), coderecall (1)

- **`CONTRACT.md`** — 5 rules every orchestrated skill must follow:
  1. REPORT (no silent success)
  2. ARCHIVE (no silent delete/overwrite)
  3. FAIL LOUD (no silent errors)
  4. BE REVERSIBLE (undo path documented)
  5. DECLARE IN REGISTRY (no hidden skills)

- **`FAILURE_MODES.md`** — 10 cataloged failure patterns (F01-F10) with detection + recovery playbook

- **Schemas in `schemas/`:**
  - `PLAN.template.md` — orchestration plan format
  - `LOG.template.md` — audit log entry format
  - `SKILL_ENTRY.template.md` — registry entry format

- **README.md** — pitch, install, usage examples, durability promises
- **CONTRIBUTING.md** — how to add new skills to the library
- **LICENSE** — MIT

### 🛡️ Durability promises
- Plain markdown only (no DB, greppable forever)
- Every run logged permanently to `logs/{run-id}.md`
- Every write archived (via skill contract)
- Every failure surfaced (via skill contract)
- Tiered automation (🟢 auto / 🟡 confirmed batch / 🔴 per-action)
- Contract enforcement (skills without declared compliance are refused)

### 💡 Why this exists
Born from the realization that a library of specialized `.skill` agents is more powerful when composed by a supervisor than invoked individually. Without a router, the user has to remember every skill. With a router, they remember one entry point.

The name Mewtwo extends the Pokémon metaphor from claudenotes: Mew routes within one family; Mewtwo routes across all of them.

🧬🃏⚡
