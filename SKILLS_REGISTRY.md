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

## claude-sessions (conversation archive + natural-language search)

- **Repo:** https://github.com/DimmMak/claude-sessions
- **Role:** Archives Claude Code JSONL sessions as searchable markdown. Provides natural-language search across entire conversation history.

### time-machine:search
- Purpose: natural-language search across archived sessions (read-only)
- Inputs: query string (e.g. "when did we first mention X", "evolution of Y", "decide about Z")
- Outputs: dates + session paths + context summary with citations
- Risk tier: 🟢
- Contract: ✓ reports, ✓ archives (read-only, no writes), ✓ fails-loud, ✓ reversible

### archive:now
- Purpose: archive the current active session to the claude-sessions repo
- Inputs: (none; auto-detects latest JSONL)
- Outputs: `sessions/{date}/{slug}.md` + regenerated indexes + git push + bundle backup
- Risk tier: 🟡
- Contract: ✓ reports, ✓ archives (originals preserved, idle check enforced), ✓ fails-loud, ✓ reversible (git history)

### archive:backlog
- Purpose: bulk-import all historical JSONL files from ~/.claude/projects/
- Inputs: optional `--since YYYY-MM-DD`
- Outputs: many new session files + regenerated indexes + git push
- Risk tier: 🟡
- Contract: ✓ reports, ✓ archives (dry-run preview first), ✓ fails-loud, ✓ reversible

### archive:redact
- Purpose: find-replace across all archived sessions (for PII scrubbing)
- Inputs: needle string, replacement string, optional `--force-push`
- Outputs: modified session files; optionally rewritten git history
- Risk tier: 🔴 (git history rewrite is IRREVERSIBLE once pushed)
- Contract: ✓ reports, ✓ archives (dry-run preview), ✓ fails-loud, 🟡 reversible (git history scrub is one-way)

### archive:refresh
- Purpose: regenerate INDEX.md + by-date/ + by-topic/ deterministically
- Inputs: (none)
- Outputs: overwritten index files (no source data changes)
- Risk tier: 🟢
- Contract: ✓ reports, ✓ archives (deterministic output), ✓ fails-loud, ✓ reversible

### archive:status
- Purpose: show archive stats (session count, dates, tags, size, latest)
- Inputs: (none)
- Outputs: stats summary (read-only)
- Risk tier: 🟢
- Contract: ✓ reports, ✓ archives (read-only), ✓ fails-loud, ✓ reversible

---

## command-center (morning dashboard + interest learning loop)

- **Repo:** `command-center/` (local)
- **Role:** 4-panel Cowork artifact — stock news, geopolitics, Gmail, to-dos — refreshed daily at 9am, ranked by a live interest model that learns from user engagement.
- **Integration:** Fund ops tier. Reads from blue-hill-capital/watchlist.md, writes engagement log consumed by pattern-observer.

### command-center:cc
- Purpose: open/refresh the dashboard artifact
- Inputs: (none; reads data/interests.md + data/*.json)
- Outputs: rendered 4-panel artifact in Cowork sidebar
- Risk tier: 🟢
- Contract: ✓ reports, ✓ archives (engagement log append-only), ✓ fails-loud, ✓ reversible

### command-center:fetch-stocks
- Purpose: scheduled 9am morning pull of stock news per interests.md
- Inputs: interests.md stock signals
- Outputs: data/stock-news.json
- Risk tier: 🟢
- Contract: ✓ reports, ✓ archives (7-day dedupe history), ✓ fails-loud, ✓ reversible

### command-center:fetch-geo
- Purpose: scheduled 9am morning pull of geopolitics news per interests.md
- Inputs: interests.md geopolitics signals
- Outputs: data/geopolitics-news.json
- Risk tier: 🟢
- Contract: ✓ reports, ✓ archives (7-day dedupe history), ✓ fails-loud, ✓ reversible

### command-center:retro
- Purpose: weekly — pattern-observer reads engagement log, proposes interest-weight updates
- Inputs: data/engagement-log.jsonl
- Outputs: proposed diff to interests.md (user confirms before write)
- Risk tier: 🟡
- Contract: ✓ reports, ✓ archives (diff preview), ✓ fails-loud, ✓ reversible

---

## desk-ops (computer-use family for Mac automation)

- **Repo:** `desk-ops/` (local)
- **Role:** Family of native-macOS automation skills. All members obey propose → confirm → execute → log → undo.
- **Integration:** Sibling to the fund's `*-desk` family. Called by mewtwo for any native-app task.

### desk-ops:file-mover
- Purpose: rule-based file moves across folders
- Inputs: YAML config, source directories
- Outputs: moved files, logs/YYYY-MM-DD.md with undo commands
- Risk tier: 🟡
- Contract: ✓ reports, ✓ archives (dry-run default), ✓ fails-loud, ✓ reversible

### desk-ops:finder-cleanup
- Purpose: triage report for Downloads/Desktop/etc.
- Inputs: folder path
- Outputs: markdown classification report (never moves)
- Risk tier: 🟢
- Contract: ✓ reports, ✓ archives (read-only), ✓ fails-loud, ✓ reversible (no-op)

### desk-ops:shortcuts-runner
- Purpose: fire macOS Shortcuts via `shortcuts` CLI
- Inputs: shortcut name, optional input text
- Outputs: shortcut runs, log entry
- Risk tier: 🟢
- Contract: ✓ reports, ✓ archives (log entry), ✓ fails-loud, ✓ reversible (depends on shortcut)

### desk-ops:photos-organizer (stub)
- Purpose: (v0.2) album creation + dedupe in Photos.app
- Status: SKILL.md spec only
- Risk tier: 🟡

### desk-ops:form-filler (stub)
- Purpose: (v0.2) native-app form filling via computer-use
- Status: SKILL.md spec only
- Risk tier: 🟡 (never fills password/signature/payment)

---

## mewtwo (orchestrator — self-entry, added per forensic CRIT #2 2026-04-28)

> The orchestrator must declare its own contract. Without this entry, the auditor of contracts is itself unaudited (self-reference paradox). Every other skill in this registry is policed by mewtwo; mewtwo is policed by this entry.

### mewtwo:orchestrate
- Purpose: decompose user intent into sub-tasks, select skills, plan + confirm + execute with audit trail
- Inputs: high-level user intent (natural language or `.mewtwo [intent]`)
- Outputs: `mewtwo/logs/{RUN_ID}.md` (full audit trail), plus whatever artifacts the called skills produce
- Risk tier: 🟡 (mewtwo itself is medium-risk — orchestration touches multiple skills, but each step is gated by its own tier)
- Contract: ✓ reports (final synthesis at STEP 6), ✓ archives (every called skill must archive per its own contract; mewtwo logs the archive path), ✓ fails-loud (STEP 5 mid-run failure handling — never silently continues), ✓ reversible (every artifact path is logged; reversal instructions in `--undo` subcommand)

### mewtwo:validate
- Purpose: check registry integrity + contract compliance for all skills
- Inputs: this file (`SKILLS_REGISTRY.md`)
- Outputs: list of contract violations (skills with ✗ or missing checkmarks)
- Risk tier: 🟢 (read-only)
- Contract: ✓ reports, ✓ archives (read-only — N/A), ✓ fails-loud, ✓ reversible (read-only — N/A)

### mewtwo:self-validate
- Purpose: assert that mewtwo's own entry above declares all 4 checkmarks. Closes the self-reference paradox: if the orchestrator's entry is ever edited to remove a checkmark, this skill emits a critical violation against itself
- Inputs: this entry's checkmark line
- Outputs: PASS or `🛑 SELF-CONTRACT VIOLATION — mewtwo missing: {missing}`
- Risk tier: 🟢
- Contract: ✓ reports, ✓ archives (N/A), ✓ fails-loud, ✓ reversible (N/A)
- **Run cadence:** at the start of every mewtwo invocation (STEP 0 PRE-FLIGHT), before reading any other registry entry. If self-validate fails, mewtwo refuses to orchestrate.

---

## 🧬 Registry metadata

- Last updated: 2026-04-28 (v5 — added mewtwo self-entry per forensic CRIT #2)
- Total skills: 39 across 10 families (+ mewtwo self)
- Contract-compliant: 39 / 39 ✅
- Legend:
  - 🟢 Low risk — auto-execute
  - 🟡 Medium risk — confirmed batch
  - 🔴 High risk — confirm per action
