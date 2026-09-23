---
name: saba-bible-theology
description: Theology from Scripture. Use when the user asks what the Bible teaches, wants a claim tested against Scripture, or wants the Westminster proof texts for a statement.
---

# Saba Bible Theology

Answer from four layers a reader can tell apart: what the texts say, what follows from them, what the confessions and commentators say, and what the resources leave open.

If the `saba` tools are absent from your tool list, follow Prerequisite, then start step 1.

## The grounding rule

**Allowed sources**

- Output of the Saba MCP tools: `search_vector`, `search_lexical`, `read`, `read_commentary`, `list_resources`, `install`.
- Files already in the user's repository (notes, position papers, prior studies).

**Not allowed**: web search or fetch, any other MCP server, and recollection of systematic theologies, confessions, church history, denominational positions, or what theologians have said.

**What you bring**: reading comprehension and logical inference over what the sources say. Each inference is written as an inference ("A says X; B says Y; taken together...") so the reader can reject it.

**Citations**: every claim ends with a bracket: `[en_ult ROM 3:28]`, `[en_wcf ROM 3:28]`, `[en_calvin GAL 2:16]`, `[notes/justification.md]`. A claim enters the write-up only with its bracket.

**Gaps**: when no installed resource addresses part of the question, write "Not covered by the installed Saba resources" and name a listed resource id when one might. A term such as "justification" or "covenant of grace" is used when a text, a confessional proof line, or a commentator uses it, and that use is cited.

## Workflow

### 1. Frame the question

The only text this step produces is one restated question and the sub-questions it contains (two to five), including any term that needs a source before it can be used.

A single passage whose meaning is the question belongs to `saba-bible-exegesis`.

**Done when** that question and those sub-questions are written, and no answer sentence exists yet.

### 2. Make sure the resources are installed

Call `list_resources`. Then `install` the ids below that show `installed: false`.

- Always: `en_ult`, `en_ust`, `en_wcf`, `en_wlc`, `en_wsc`, `en_calvin`, `en_mhc`, `en_tn`.
- Old Testament key passages: `en_kd`, `hbo_uhb`.
- New Testament key passages: `el-x-koine_ugnt`.

Pass those ids. Leave `all` unused; it downloads every free resource. A copyrighted Bible already installed (`eng_esv`, `eng_niv2011`, ...) is read and cited by its id.

**Done when** each id in the matching list is installed.

### 3. Gather Scripture

Search each sub-question on its own, with its own reads.

- `search_vector` with the question in plain words, then with two or three rephrasings and with each sub-question. `limit` 20 to 30. It searches every Search index already installed.
- `search_lexical` with the key terms in the user's wording and in the ULT's wording (`justified`, `counted as righteous`).
- Search the **counter-case**: phrasings a reader would use to argue the other way.

On the error "Topic search model is not downloaded":

1. Tell the user `search-en-ult` is about 220 MB, downloaded once.
2. Call `install` with `{"ids": ["search-en-ult"]}`. Wait for it to return.
3. Call `search_vector` again with the same query.

If the user declines, gather with `search_lexical` only and label the gather lexical-only.

A hit is a candidate until you `read` it. `read` each candidate you will use on `en_ult` with surrounding context (the paragraph, at least a few verses either side), and the central passages on `en_ust` as well. A hit with a locus is read with `read_commentary` on its resource. Drop a candidate that in context is about something else. The passages that survive are the **key passages** for steps 4 and 5.

**Done when** each sub-question and the counter-case have a search, and every key passage has its context read.

### 4. Read the confessional proofs

For each key passage, `read_commentary` on `en_wcf`, `en_wlc`, and `en_wsc`. Each line is the Confession section, or the Catechism question and answer, that cites that verse. This is the route into the Westminster Standards (`book/wcf`, `book/wlc`, `book/wsc`, and the creed ids are listed and not readable yet).

An empty result means the Standards do not cite that verse. Record it that way.

**Done when** each key passage has those three calls, and each empty result is recorded.

### 5. Read the commentaries

For each key passage, `read_commentary` on `en_calvin`, `en_mhc`, `en_tn`, and `en_kd` (OT only). Record what bears on the question, close to their words, with the verse tag. Record a disagreement between them as a disagreement.

**Done when** each key passage has those calls, and each disagreement is recorded.

### 6. Read the user's own notes

Search the repo for the topic's terms and for the key passages. Cite each note by path under Your notes, as the user's stated position. Scripture, the Saba witnesses, and the user stay in their own sections. When a note asserts something the gathered texts do not support, say so and cite both.

**Done when** the repo search has been run and each matching note has been read, or the search found nothing.

### 7. Reason, then write up

Fill **Reasoning** before **Short answer**. The Short answer contains only sentences that trace to a numbered inference. Names of theologians, denominational labels, and the history of a doctrine appear only when a Saba source supplies them.

Keep the sections. Mark a section empty when it is empty.

```markdown
# <Question, restated>

## Reasoning
<Numbered inferences from steps 3 to 6. Each names the texts it rests on and is marked "stated" (a text or witness says it) or "inferred" (you derived it).>

## Scriptural basis
### <Sub-question>
- <ref>: <what the text says, in context, close to the ULT> [en_ult ...]

## Counter-texts and tensions
- <ref>: <what a reader could set against the answer, from the read> [en_ult ...]
  <How the sources handle it, when they do> [en_calvin ...] [en_wlc ...]

## Confessional witness
- <ref>: <Confession section or Catechism Q&A that cites it> [en_wcf ...] [en_wlc ...] [en_wsc ...]
- (or) The Westminster Standards do not cite any of the key passages.

## Commentators
- Calvin on <ref>: <close summary> [en_calvin ...]
- Matthew Henry on <ref>: ... [en_mhc ...]
- Keil and Delitzsch on <ref>: ... [en_kd ...]

## Your notes
- <path>: <the user's stated position> [notes/...]

## Short answer
<Two to four sentences, each traceable to a numbered inference. When the resources do not support a confident answer, say that.>

## Not covered
- <Part of the question no installed resource addresses. Name a listed resource id when one might.>

## Sources
<Every resource id and reference span read in this session, one per line.>
```

**Done when** Reasoning is filled before Short answer, every section is present or marked empty, every claim has its citation bracket, and every Short answer sentence traces to a numbered inference.

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
