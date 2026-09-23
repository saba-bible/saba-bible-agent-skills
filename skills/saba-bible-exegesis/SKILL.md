---
name: saba-bible-exegesis
description: Exegesis of one Bible passage. Use when the user asks what a specific verse or chapter means, including its wording.
---

# Saba Bible Exegesis

Exegete a passage from the text out. Every observation cites the resource and reference it came from.

If the `saba` tools are absent from your tool list, follow Prerequisite, then start step 1.

## The grounding rule

**Allowed sources**

- Output of the Saba MCP tools: `read`, `read_commentary`, `search_vector`, `search_lexical`, `list_resources`, `install`.
- Files already in the user's repository (notes, outlines, prior studies).

**Not allowed**: web search or fetch, any other MCP server, and recollection of commentaries, lexicons, scholarship, or what a Hebrew or Greek word means.

**What you bring**: reading comprehension of the English and reasoning over what the sources say (sequence, contrast, repetition, who is speaking to whom). Label it as your observation of the text, cited to the verses you read.

**Citations**: every interpretive claim ends with a bracket: `[en_tn ROM 8:28]`, `[en_calvin ROM 8:28-30]`, `[en_ult ROM 8:18-39]`, `[notes/romans.md]`. A claim enters the write-up only with its bracket.

**Gaps**: when no installed resource addresses something, write "Not covered by the installed Saba resources" and name a listed resource id when one might.

## Workflow

### 1. Fix the passage

State the reference in USFM (`ROM 8:28-30`). The **context span** is the whole chapter, or the paragraph when the user gave a long passage. A long passage is exegeted in paragraph units, each with its own reads.

A topic with no reference belongs to `saba-bible-theology`.

**Done when** the reference is in USFM and the context span is named, as one chapter or a list of paragraph units.

### 2. Make sure the resources are installed

Call `list_resources`. Then `install` the ids below that show `installed: false`.

- Always: `en_ult`, `en_ust`, `en_tn`, `en_tq`.
- Old Testament: `hbo_uhb`, `en_kd`, `en_mhc`, `en_calvin`.
- New Testament: `el-x-koine_ugnt`, `en_mhc`, `en_calvin`.

Pass those ids. Leave `all` unused; it downloads every free resource. A copyrighted Bible already installed (`eng_esv`, `eng_niv2011`, ...) is read and cited by its id.

**Done when** each id in the matching list is installed.

### 3. Read the text

- `read` `en_ult` for the **context span**, and note where the **passage** sits in it.
- `read` `en_ust` for the passage. Where UST renders differently from ULT, record the difference.
- `read` the original (`hbo_uhb` or `el-x-koine_ugnt`) for the passage. Quote it as returned.

Quote the Hebrew or Greek. A gloss, parsing, or semantic claim comes from a note: `en_tn` glosses words; `en_kd` and `en_calvin` discuss the original.

**Done when** `en_ult` (context span), `en_ust` (passage), and the original (passage) have each been read or named as not installed.

### 4. Read the notes and commentaries

Call `read_commentary` on the passage for `en_tn`, `en_tq`, `en_mhc`, `en_calvin`, and `en_kd` (OT only). Record "no notes" when a resource returns nothing. Keep each line's verse tag in the citation.

`en_tn` is the source for word-level and figure-of-speech observations. `en_tq` is the check that the synthesis covers what the passage is about.

Where Matthew Henry, Calvin, and Keil and Delitzsch disagree, report both. A summary stays close to their words and cites the tag.

**Done when** each of those ids has notes or a recorded "no notes".

### 5. Find intertext

- `search_lexical` with a distinctive 2 to 4 word phrase from the ULT.
- `search_lexical` with `resource` set to the original and the exact form quoted in step 3. Report it as a form match: the same written string.
- `search_vector` with the passage's idea in plain words. It searches every Search index already installed.

On the error "Topic search model is not downloaded":

1. Tell the user `search-en-ult` is about 220 MB, downloaded once.
2. Call `install` with `{"ids": ["search-en-ult"]}`. Wait for it to return.
3. Call `search_vector` again with the same query.

If the user declines, the intertext is lexical-only and the write-up says the index was not installed.

A hit is a candidate until you `read` it in its own context. Drop a candidate that is about something else.

**Done when** the phrase search, the form search, and the vector search have each returned or the declined index is named, and every intertext line has a context read.

### 6. Read the user's own notes

Search the repo for the reference (`Romans 8`, `Rom 8:28`, `ROM 8`) and for the passage's key terms. Cite each note by path as the user's prior work, beside the Saba resources. When a note and the Saba resources differ, report both.

**Done when** the repo search has been run and each matching note has been read, or the search found nothing.

### 7. Write up

Historical, cultural, or geographic background appears only when a note supplies it. A doctrinal framework appears only when a source uses it, or when the user's notes use it and the write-up attributes it to them. The synthesis is built from the sections above.

Keep the sections. Mark a section empty when it is empty.

```markdown
# <Reference> (<ULT>)

## Text
<ULT, one verse per line, as returned by read>

## Context
<Where the passage sits in the chapter: what precedes, what follows, who is speaking to whom.> [en_ult ...]

## Observations, verse by verse
### <ref>
- ULT / UST: <difference in rendering, if any> [en_ult ...] [en_ust ...]
- Original: <quoted form> ... <what a note says about it> [en_tn ...]
- Notes: <en_tn / en_tq points> [en_tn ...]
- Commentary: <Matthew Henry, Calvin, Keil and Delitzsch, each on its own line> [en_mhc ...] [en_calvin ...] [en_kd ...]

## Intertext
- <ref>: <what it shares with the passage, from the read> [en_ult ...]

## Your notes
- <path>: <what the user already wrote> [notes/...]

## Synthesis
<What the passage says in the author's argument, built from the sections above. Where commentators differ, both are here.>

## Not covered
- <Question no installed resource answers. Name a listed resource id when one might.>

## Sources
<Every resource id and reference span read in this session, one per line.>
```

**Done when** every section is present or marked empty, and every interpretive claim has its citation bracket.

## Prerequisite: the Saba MCP server

Install and register the server, then continue the workflow:

```bash
npm i -g saba-bible-mcp
```

Then add it to the agent's MCP config (Cursor: `.cursor/mcp.json` or Settings, MCP; Claude Code: `claude mcp add saba -- saba-bible-mcp`; Codex: `~/.codex/config.toml`) and ask the user to reload MCP servers:

```json
{ "mcpServers": { "saba": { "command": "saba-bible-mcp" } } }
```

The agent client spawns `saba-bible-mcp`. It speaks stdio JSON-RPC, so leave it to the client. Bible content is not in the package; `install` downloads it on first use.
