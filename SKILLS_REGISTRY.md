# 📚 SKILLS REGISTRY

> The master manifest. Mewtwo reads this on every run to know what agents exist.
>
> **Format:** one entry per skill. Namespaced as `family:skill-name`. Each entry declares inputs, outputs, and contract compliance.
>
> **Rule:** add a new skill → add an entry here. No entry = Mewtwo can't see it.

---

## claudenotes (agent factory for personal notes)

- **Repo:** https://github.com/DimmMak/claudenotes
- **Role:** A factory of 9 specialized agents for personal note-taking.
- **Integration:** Sub-skills are invoked via the `notes` orchestrator (or directly by name in namespaced form).

### claudenotes:notes
- Purpose: orchestrator within claudenotes (routes to capture/sort/etc.)
- Inputs: user intent
- Outputs: delegation to a sub-skill
- Risk tier: 🟢
- Contract: ✓ reports, ✓ archives, ✓ fails-loud, ✓ reversible

### claudenotes:capture
- Purpose: quick brain-dump, no friction
- Inputs: raw text
- Outputs: timestamped entry in `inbox/{date}-capture.md`
- Risk tier: 🟢
- Contract: ✓ reports, ✓ archives (inbox append), ✓ fails-loud, ✓ reversible

### claudenotes:sort
- Purpose: route inbox entries to hierarchy
- Inputs: inbox files
- Outputs: entries moved to proper folders, inbox drained
- Risk tier: 🟡
- Contract: ✓ reports, ✓ archives (move preserves original via git), ✓ fails-loud, ✓ reversible

### claudenotes:connect
- Purpose: find semantic cross-links between notes
- Inputs: source file
- Outputs: suggested bidirectional links
- Risk tier: 🟡
- Contract: ✓ reports, ✓ archives (shows before/after), ✓ fails-loud, ✓ reversible

### claudenotes:distill
- Purpose: compress verbose notes (3:1 default)
- Inputs: target file
- Outputs: distilled file, original archived to `archive/distill/`
- Risk tier: 🟡
- Contract: ✓ reports, ✓ archives, ✓ fails-loud, ✓ reversible

### claudenotes:recall
- Purpose: first-letter recall drill on notes
- Inputs: note corpus
- Outputs: drill session + updated weak-items tracking
- Risk tier: 🟢
- Contract: ✓ reports, ✓ archives (session logged), ✓ fails-loud, ✓ reversible (doesn't modify sources)

### claudenotes:map
- Purpose: regenerate MAP.md sitemap
- Inputs: repo state
- Outputs: overwritten MAP.md
- Risk tier: 🟢
- Contract: ✓ reports, ✓ archives (git tracks), ✓ fails-loud, ✓ reversible

### claudenotes:evolve
- Purpose: observe patterns → propose new PREFERENCES rules
- Inputs: recent activity
- Outputs: proposed rules (user confirms before write)
- Risk tier: 🟡
- Contract: ✓ reports, ✓ archives (PREFERENCES is append-only), ✓ fails-loud, ✓ reversible

### claudenotes:cowatch
- Purpose: live study buddy for browser lectures (uses Tampermonkey pipeline)
- Inputs: browser tab + user cues ("look", "thoughts", etc.)
- Outputs: reactions + suggested captures
- Risk tier: 🟢
- Contract: ✓ reports, ✓ archives (no writes without user paste), ✓ fails-loud, ✓ reversible

---

## courserafied (course → queryable knowledge base)

- **Repo:** https://github.com/DimmMak/courserafied
- **Role:** Turn any course material into 6 categorized .md files.

### courserafied:init
- Purpose: scaffold a new course directory from `_template`
- Inputs: course-name (kebab-case)
- Outputs: `courses/{name}/` with 7 empty files
- Risk tier: 🟡
- Contract: ✓ reports, ✓ archives (refuses to overwrite), ✓ fails-loud, ✓ reversible

### courserafied:ingest
- Purpose: parse transcript → write to 6 categorized files
- Inputs: raw transcript + course context
- Outputs: appended entries across SUMMARIES/GLOSSARY/PATTERNS/EXAMPLES/QUOTES/EXAM-PREP
- Risk tier: 🟡
- Contract: ✓ reports (per-file counts), ✓ archives (INDEX regenerates), ✓ fails-loud, ✓ reversible

### courserafied:query
- Purpose: find where a term is documented
- Inputs: term
- Outputs: file + section path
- Risk tier: 🟢
- Contract: ✓ reports, ✓ archives (read-only), ✓ fails-loud, ✓ reversible (no writes)

### courserafied:validate
- Purpose: check integrity (broken links, missing fields, duplicate entries)
- Inputs: course directory
- Outputs: report
- Risk tier: 🟢
- Contract: ✓ reports, ✓ archives (read-only), ✓ fails-loud, ✓ reversible

### courserafied:stats
- Purpose: entry counts per file, last updated
- Inputs: course directory
- Outputs: stats summary
- Risk tier: 🟢
- Contract: ✓ reports, ✓ archives (read-only), ✓ fails-loud, ✓ reversible

---

## transmute (laziness → discipline)

- **Repo:** https://github.com/DimmMak/transmute
- **Role:** Multi-persona psychological diagnostic + personalized protocol generator.

### transmute:diagnose
- Purpose: run 4-persona interview (behavioral, CBT, performance, neuroscience)
- Inputs: conversation with user
- Outputs: `profiles/{user}.md` with full diagnosis
- Risk tier: 🟡
- Contract: ✓ reports, ✓ archives (versions previous profile), ✓ fails-loud, ✓ reversible

### transmute:protocol
- Purpose: generate personalized daily protocol from profile
- Inputs: user profile
- Outputs: `profiles/{user}-protocol.md` + shipped personal `.skill`
- Risk tier: 🔴
- Contract: ✓ reports, ✓ archives (versions previous), ✓ fails-loud, 🟡 reversible (writes to `~/.claude/skills/`)

---

## cowatch (live lecture study buddy — standalone)

- **Repo:** https://github.com/DimmMak/cowatch
- **Role:** Browser-MCP + Tampermonkey pipeline for live lecture co-watching.
- **Note:** claudenotes:cowatch is a wrapper around this.

### cowatch:react
- Purpose: read live transcript + respond to cue
- Inputs: browser tab + user cue
- Outputs: reaction text
- Risk tier: 🟢
- Contract: ✓ reports, ✓ archives (read-only until user pastes), ✓ fails-loud, ✓ reversible

### cowatch:end
- Purpose: end-of-lecture flow (top takeaways + suggested captures)
- Inputs: full transcript
- Outputs: formatted takeaways + capture commands
- Risk tier: 🟢
- Contract: ✓ reports, ✓ archives (no writes unless user runs captures), ✓ fails-loud, ✓ reversible

---

## promptlatro (prompt pattern roguelike)

- **Repo:** https://github.com/DimmMak/promptlatro
- **Role:** Roguelike deckbuilder that teaches prompt patterns through play.

### promptlatro:play
- Purpose: run a game session
- Inputs: difficulty choice
- Outputs: gameplay + learning moments
- Risk tier: 🟢
- Contract: ✓ reports, ✓ archives (session stats tracked in-memory), ✓ fails-loud, ✓ reversible

---

## royal-rumble (multi-legend stock analysis)

- **Repo:** https://github.com/DimmMak/royal-rumble
- **Role:** 8 legendary investors analyze a stock from their specific pillar.

### royal-rumble:analyze
- Purpose: run a stock through all 8 legend analyses
- Inputs: ticker symbol + financial data
- Outputs: 8 analyses + Judge synthesis
- Risk tier: 🟡
- Contract: ✓ reports, ✓ archives (analysis logged), ✓ fails-loud, ✓ reversible

---

## coderecall (first-letter recall drills)

- **Repo:** https://github.com/DimmMak/coderecall
- **Role:** SQL/Python drill game using BibleMemory Pro mechanic.

### coderecall:drill
- Purpose: run a recall drill session
- Inputs: level (guided/recall/memory), section focus
- Outputs: drill session + accuracy tracking
- Risk tier: 🟢
- Contract: ✓ reports, ✓ archives (session stats), ✓ fails-loud, ✓ reversible

---

## 🧬 Registry metadata

- Last updated: 2026-04-16
- Total skills: 22 across 7 families
- Contract-compliant: 22 / 22 ✅
- Legend:
  - 🟢 Low risk — auto-execute
  - 🟡 Medium risk — confirmed batch
  - 🔴 High risk — confirm per action
