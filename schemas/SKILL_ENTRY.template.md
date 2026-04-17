# 📋 Registry Entry Template

> Use this format when adding a new skill to `SKILLS_REGISTRY.md`.

---

## {family} ({1-sentence description of the family})

- **Repo:** {https://github.com/...}
- **Role:** {2-3 sentence description of what this family does}
- **Integration notes:** {how sub-skills relate, any wrapper behavior}

### {family}:{skill-name}
- **Purpose:** {1 sentence — what problem does this solve?}
- **Inputs:** {what the skill consumes — files, user text, other skill outputs}
- **Outputs:** {what the skill produces — include EXACT file paths where applicable}
- **Risk tier:** 🟢 / 🟡 / 🔴
  - 🟢 = read-only OR writes only to skill-managed files
  - 🟡 = writes that affect user-visible project files
  - 🔴 = irreversible side effects (git push, external APIs, ~/.claude/skills/ writes)
- **Contract:** 
  - ✓ reports (explicit end-of-run report)
  - ✓ archives (originals preserved before overwrite)
  - ✓ fails-loud (errors surface with fix suggestion)
  - ✓ reversible (or documented reverse path)

---

## Rules for registry entries

1. **One entry per skill.** No bundling.
2. **Use kebab-case skill names** to match `.skill` filename conventions.
3. **All four contract items required.** Missing one → Mewtwo refuses to orchestrate.
4. **Risk tier is honest.** Err high-risk, not low. 🔴 needing confirmation is safer than 🟢 that surprises the user.
5. **Include file paths.** "Outputs: structured data" is not enough. "Outputs: entries in `courses/{name}/GLOSSARY.md`" is.
6. **Update on skill changes.** If a skill evolves to write a new file type, the registry entry must evolve too.

---

## Validation

Run `/mewtwo --validate` after adding a new entry. Mewtwo checks:
- [ ] All four contract items present
- [ ] Risk tier is one of 🟢 🟡 🔴
- [ ] Inputs and outputs are non-empty
- [ ] Purpose is a single sentence (not a paragraph)
- [ ] Skill name is kebab-case
- [ ] Family block exists and has Repo + Role

Any failure = the skill is flagged as unsafe for orchestration.
