# 🧬🃏 mewtwo

> **The master orchestrator for your `.skill` library.** Receives high-level intent, decomposes into sub-tasks, selects from a namespaced skill registry, proposes an execution plan, confirms once, and executes with tiered automation + full audit trail.
>
> **Durability-first design.** Every run is logged as markdown. Every write is archived. Every failure is loud. No database lock-in, no silent operations, no cascading failures.

```
YOU                        MEWTWO                     YOUR SKILL LIBRARY
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
"Build me a Carl Jung  ──→  Decompose  ──→  courserafied:init
 knowledge base with        Match                  ↓
 an interactive tutor        Plan          courserafied:ingest
 .skill"                    Confirm                ↓
                            Execute         transmute:persona
                            Report                 ↓
                                           ship ~/.claude/skills/jung.skill
                                                  ↓
                                            ✅ done + logged
```

---

## 🎯 Why this exists

You have an army of specialized `.skill` agents (claudenotes, cowatch, courserafied, transmute, promptlatro, royal-rumble, coderecall). Each is great at ONE thing. But real requests span multiple skills.

**Mewtwo = the conductor.** You tell it what you want. It decides which specialists to call, in what order, with what inputs. You confirm the plan once. It executes and logs everything.

Without Mewtwo, you have to remember 22 skills and compose them manually. With Mewtwo, you remember one entry point.

---

## 🃏 Why "Mewtwo"

Because your `.skill` library already has **Mew** (the `/notes` orchestrator in claudenotes — routes WITHIN one system). Mewtwo is Mew's upgraded, lab-engineered form — routes ACROSS all systems.

```
Pokédex #151 — Mew    → within-family router (claudenotes only)
Pokédex #150 — Mewtwo → cross-family router (entire library)
```

---

## 🛡️ Durability promises

| Promise | Implementation |
|---|---|
| Plain text forever | Registry + logs + contracts are all markdown. No DB. |
| No silent operations | Contract Rule 1: every skill reports what it did |
| No silent deletes | Contract Rule 2: every write archives the original |
| No silent errors | Contract Rule 3: failures surface with fix suggestions |
| Every write reversible | Contract Rule 4: undo path documented (or action marked irreversible) |
| Full audit trail | Every run logs to `logs/{run-id}.md` permanently |
| Tiered safety | 🟢 auto / 🟡 confirmed batch / 🔴 per-action |

---

## 🚀 Install

```bash
# Copy into your skills directory
cp -r mewtwo ~/.claude/skills/
# Or zip as .skill bundle:
cd .. && zip -r ~/.claude/skills/mewtwo.skill mewtwo/
```

Then in any Claude Code session:
```
/mewtwo [high-level intent]
```

---

## 🎮 Usage examples

```bash
# Full orchestration (default)
/mewtwo build a knowledge base on Stoic philosophy with an interactive tutor

# Dry run — plan only, no execution
/mewtwo --plan-only generate a daily discipline protocol from my current habits

# Registry summary
/mewtwo --registry

# Previous run audit trail
/mewtwo --log mewtwo-2026-04-16T14-30

# Undo instructions for a previous run
/mewtwo --undo mewtwo-2026-04-16T14-30

# Validate all registered skills comply with contract
/mewtwo --validate
```

---

## 📂 Repo layout

```
mewtwo/
├── README.md                    # This file
├── SKILL.md                     # The orchestrator (read by Claude)
├── SKILLS_REGISTRY.md           # Manifest of all available skills
├── CONTRACT.md                  # 5 rules every skill must follow
├── FAILURE_MODES.md             # Known failures + recovery playbook
├── schemas/
│   ├── PLAN.template.md         # Orchestration plan format
│   ├── LOG.template.md          # Audit log entry format
│   └── SKILL_ENTRY.template.md  # Registry entry format
├── logs/                        # Run logs (gitignored — personal data)
├── examples/                    # Sample orchestration plans + logs
├── CHANGELOG.md
├── CONTRIBUTING.md
├── LICENSE
└── .gitignore
```

---

## 🔗 Skill library (what Mewtwo orchestrates)

Current registry spans 29 skills across 8 families:

- **claudenotes** (9 skills) — agent factory for personal notes
- **cowatch** (2 skills) — live lecture study buddy
- **courserafied** (5 skills) — course → queryable knowledge base
- **transmute** (2 skills) — laziness → discipline protocol generator
- **promptlatro** (1 skill) — prompt pattern roguelike
- **royal-rumble** (1 skill) — multi-legend stock analysis
- **coderecall** (1 skill) — first-letter recall drills
- **claude-sessions** (7 skills) — archive + search conversation history

See [`SKILLS_REGISTRY.md`](SKILLS_REGISTRY.md) for full entries.

---

## 🛠️ Adding a new skill to the library

1. Build your `.skill` following the [contract rules](CONTRACT.md)
2. Add an entry to `SKILLS_REGISTRY.md` using [`schemas/SKILL_ENTRY.template.md`](schemas/SKILL_ENTRY.template.md)
3. Run `/mewtwo --validate` to confirm contract compliance
4. Your skill is now orchestrable

If you skip step 2, Mewtwo can't see your skill. On purpose. Registration IS consent to be orchestrated.

---

## 📜 License

MIT — do whatever you want with it.

---

## 🃏 Built by

[@DimmMak](https://github.com/DimmMak) — the closing piece in a multi-repo exploration of `.skill`-based agent factories.

**Sister projects (the 7 families Mewtwo orchestrates):**
- [claudenotes](https://github.com/DimmMak/claudenotes) — agent factory for notes
- [cowatch](https://github.com/DimmMak/cowatch) — live study buddy
- [courserafied](https://github.com/DimmMak/courserafied) — course → knowledge base
- [transmute](https://github.com/DimmMak/transmute) — discipline protocol generator
- [promptlatro](https://github.com/DimmMak/promptlatro) — prompt-engineering roguelike
- [royal-rumble](https://github.com/DimmMak/royal-rumble) — multi-legend stock analysis
- [coderecall](https://github.com/DimmMak/coderecall) — first-letter recall drilling

🧬🃏⚡
