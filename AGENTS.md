Skills live in `skills/<name>/SKILL.md`, one folder per skill, flat (no bucket folders until there are enough skills to need them).

Every skill must appear in three places: the `## Skills` list in `README.md` (name linked to its `SKILL.md`, one-line description), the `skills` array in `.claude-plugin/plugin.json`, and its own `agents/openai.yaml` beside `SKILL.md` (Codex picker metadata: `interface.display_name`, `interface.short_description`).

Every skill in this repo is **model-invoked**: omit `disable-model-invocation` from the frontmatter and omit the `policy` block from `agents/openai.yaml`. The `description` is model-facing and keeps its trigger phrasing ("Use when the user asks...") so auto-invocation fires.

Every skill carries the grounding rule from `README.md` (Saba MCP output and repo files only; no web, no memory; cite every claim; report gaps). Do not weaken it in one skill without changing it in all of them and in `README.md`.

Resource ids in skills must match the Saba catalog (`en_ult`, `en_tn`, `en_calvin`, `hbo_uhb`, ...). Do not invent ids. `read` is for Bibles, `read_commentary` for verse-tagged helps; lexicons, grammars, books, and stories are not readable through the MCP yet, and skills must not pretend otherwise.

Each `SKILL.md` stays under 500 lines and self-contained: no `../other-skill/` links. If two skills need the same reference material, duplicate the few lines rather than adding a third file both point at.

No em-dashes in prose anywhere in this repo. Rewrite with a comma, colon, period, parentheses, or a conjunction.
