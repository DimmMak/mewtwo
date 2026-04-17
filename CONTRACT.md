# 🛡️ Skill Contract

> Every skill that wants to be orchestrated by Mewtwo must declare compliance with these five rules. No exceptions.
>
> The contract is what makes full automation durable — the user trusts the system because every skill behaves the same way.

---

## The 5 Rules

### 1. REPORT — No silent success

Every skill invocation ends with an explicit report of what was done.

```
✅ GOOD:
   "Wrote 3 entries to GLOSSARY.md. See lines 15-42."

❌ BAD:
   [skill returns nothing, user doesn't know what happened]
```

Even read-only operations report ("searched 14 files, found 2 matches").

---

### 2. ARCHIVE — No silent delete or overwrite

Any action that replaces, modifies, or deletes user content must archive the previous state first.

```
✅ GOOD:
   distill target.md
   → copies target.md → archive/distill/2026-04-16-target.md
   → then overwrites target.md with distilled version
   → reports both paths

❌ BAD:
   [silently overwrites target.md, original is gone forever]
```

Git history counts as an archive for tracked files. Append-only files (inboxes, logs) are exempt because nothing is replaced.

---

### 3. FAIL LOUD — No silent error swallowing

Errors must surface with:
- What was attempted
- Why it failed
- A suggested fix

```
✅ GOOD:
   "🛑 Failed to write to PATTERNS.md — file is read-only.
    Fix: chmod +w PATTERNS.md, then re-run."

❌ BAD:
   [try/catch swallows the error, skill exits "successfully"]
```

---

### 4. BE REVERSIBLE — Every write has an undo path

For every action that modifies state, the skill must either:
- Reverse the action on demand, OR
- Document the exact manual steps to reverse it

If neither is possible, the skill is flagged 🔴 IRREVERSIBLE in the registry and requires explicit per-action confirmation.

```
✅ GOOD (reversible):
   sort → moved 3 entries, archived original inbox file, undo = restore from archive

🟡 ACCEPTABLE (manual reverse documented):
   transmute:protocol ships a .skill to ~/.claude/skills/
   Undo = rm ~/.claude/skills/{name}.skill

🔴 IRREVERSIBLE (must be marked):
   git push origin main → once pushed, others may pull
```

---

### 5. DECLARE IN REGISTRY — No hidden skills

Every skill gets an entry in `SKILLS_REGISTRY.md` with:
- Purpose (1 sentence)
- Inputs (what it consumes)
- Outputs (what it produces, including file paths)
- Risk tier (🟢 🟡 🔴)
- Contract compliance (all 4 of the above, explicitly stated)

Skills without a registry entry are invisible to Mewtwo. On purpose. If you want automation, you commit to the contract.

---

## Contract declaration format

```markdown
### {family}:{skill-name}
- Purpose: {1 sentence}
- Inputs: {what it consumes}
- Outputs: {what it produces — include file paths}
- Risk tier: 🟢 / 🟡 / 🔴
- Contract: ✓ reports, ✓ archives, ✓ fails-loud, ✓ reversible
```

All four checkmarks required. If any are missing, replace with ✗ and Mewtwo will refuse to call the skill.

---

## Validation

Mewtwo can check contract compliance automatically:

```
/mewtwo --validate
  → reads SKILLS_REGISTRY.md
  → confirms every entry has the 4 checkmarks
  → flags any ✗ or missing fields
  → reports skills that cannot be safely orchestrated
```

Run this after adding new skills. Run it monthly as a maintenance check.

---

## Why this contract exists

**Automation without trust = chaos.** The contract is what makes it possible to say "run the whole pipeline automatically" — because every skill behaves predictably:

- It tells you what it did ✅
- It saved your previous state ✅
- It tells you if something broke ✅
- You can always undo it ✅

Take any of those four away and full automation becomes dangerous. With all four, it's boring-reliable.

**Boring-reliable is the goal.** Boring = predictable. Reliable = you can trust the system enough to automate it.

🛡️🃏
