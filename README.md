# Saba Bible Agent Skills

Agent skills for grounded Bible study on [Saba](https://app.saba.bible) resources.

Every skill here reads Scripture, notes, and commentaries through the Saba MCP server and cites what it read. No web search. No answers from the model's memory. If a Saba resource does not cover something, the skill says so instead of filling the gap.

Works with **Cursor** (primary), **Claude Code**, **Codex**, and any agent that reads `SKILL.md` files.

## Prerequisite: the Saba MCP server

The skills call the `saba` MCP tools (`list_resources`, `install`, `read`, `read_commentary`, `search_vector`, `search_lexical`). Install the server once:

```bash
npm install -g saba-bible-mcp
```

Then register it with your agent. Cursor: Settings, MCP, or `.cursor/mcp.json`. Claude Code: `claude mcp add saba -- saba-bible-mcp`. Codex: `~/.codex/config.toml`.

```json
{
  "mcpServers": {
    "saba": {
      "command": "saba-bible-mcp"
    }
  }
}
```

Bible content is downloaded on first use: the skills call `install` for the resources they need. The ULT search index is about 220 MB; the rest is a few MB each.

## Installation

Two routes. **[skills.sh](https://skills.sh/saba-bible/saba-bible-agent-skills)** copies editable skill files into your project or home directory, for any agent. **The Claude Code plugin** installs the set as a managed bundle. Pick one; installing both gives you every skill twice.

### Cursor

```bash
npx skills@latest add saba-bible/saba-bible-agent-skills -a cursor
```

Project install lands in `.agents/skills/` (Cursor reads it). Add `-g` for `~/.cursor/skills/`, available in every project.

### Codex, and other agents

```bash
npx skills@latest add saba-bible/saba-bible-agent-skills
```

The installer asks which skills to take and which agents to install them on. Pass `-a codex`, `-a claude-code`, or `-a '*'` to skip the prompt.

### Claude Code

Either the same installer with `-a claude-code`, or the plugin:

```
/plugin marketplace add saba-bible/saba-bible-agent-skills
/plugin install saba-bible-skills@saba-bible
```

### Manual

Copy a folder from `skills/` into your agent's skill directory (`.cursor/skills/`, `.claude/skills/`, `.codex/skills/`, or `.agents/skills/`). Each folder is self-contained.

## Skills

All skills are **model-invoked**: the agent reaches for them on its own when a question fits, and you can also name them directly.

- **[saba-bible-search](./skills/saba-bible-search/SKILL.md)**: Find verses on a topic, idea, or phrase. Prefers `search_vector` (keyword plus semantic search over the ULT), uses `search_lexical` for exact quotes and other Bibles, reads every hit in context before reporting it, and never pads the list from memory.
- **[saba-bible-exegesis](./skills/saba-bible-exegesis/SKILL.md)**: Exegete a passage from the text out. Reads ULT, UST, and the Hebrew or Greek, pulls Translation Notes and the commentaries on the span, finds intertext by search, folds in your own notes from the repo, and writes up observations, synthesis, and gaps with a citation on every claim.
- **[saba-bible-theology](./skills/saba-bible-theology/SKILL.md)**: Reason about a doctrine or theological question from Scripture gathered by search, the Westminster Standards' Scripture proofs, and the commentaries. Separates what the texts say, what follows from them, what the confessions say, and what the resources leave open.

## The grounding rule

Each skill carries the same contract:

1. **Allowed sources**: output of the Saba MCP tools, and files already in the user's repository.
2. **Not allowed**: web search or fetch, other MCP servers, and the model's own recollection of commentaries, lexicons, scholarship, or original-language meanings.
3. **Every interpretive claim is cited** to a Saba resource id and reference (`[en_tn ROM 8:28]`, `[en_calvin JHN 1:1]`) or a repo path (`[notes/romans.md]`).
4. **Gaps are reported, not filled.** "Not covered by the installed Saba resources" is a valid answer.
5. **If the Saba tools are not available**, the skill stops and tells the user how to install the MCP server. It does not fall back to memory.

## What the skills can read

| Kind | Ids | Tool |
|------|-----|------|
| English Bibles | `en_ult` (literal), `en_ust` (simplified); ESV, NIV, etc. only if the user has downloaded them via `download_ipns` | `read`, `search_lexical` |
| Original texts | `hbo_uhb` (Hebrew), `el-x-koine_ugnt` (Greek NT), `el-x-koine_sr`, `grc_tr`, `grc_lxx` (Septuagint), Targums | `read`, `search_lexical` |
| Verse notes | `en_tn` (Translation Notes), `en_tq` (Translation Questions), `en_mhc` (Matthew Henry), `en_calvin` (Calvin), `en_kd` (Keil and Delitzsch, OT) | `read_commentary` |
| Confessional proofs | `en_wcf`, `en_wlc`, `en_wsc` (Westminster Confession, Larger and Shorter Catechism, by proof text) | `read_commentary` |
| Topic search | ULT search index `search-en-ult` | `search_vector` |

Lexicons (`en_tw`, `bundled/bdb`, `bundled/thayer`, LSJ), grammars, the confession and catechism texts as books, and Open Bible Stories are listed by `list_resources` but not yet readable through the MCP. The skills do not substitute memory for them.

## Repository layout

```
skills/
  saba-bible-search/
    SKILL.md            the skill
    agents/openai.yaml  Codex UI metadata
  saba-bible-exegesis/
    SKILL.md
    agents/openai.yaml
  saba-bible-theology/
    SKILL.md
    agents/openai.yaml
.claude-plugin/
  plugin.json           Claude Code plugin manifest (lists every skill)
  marketplace.json      lets the repo act as its own single-plugin marketplace
AGENTS.md               conventions for agents editing this repo
```

Modeled on [mattpocock/skills](https://github.com/mattpocock/skills).
