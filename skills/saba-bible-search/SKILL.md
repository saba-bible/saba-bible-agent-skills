---
name: saba-bible-search
description: Search the Bible for verses. Use when the user asks for verses on a topic, an exact or half-remembered phrase, or cross-references to a passage.
---

# Saba Bible Search

Report verses you have read. A search hit is a **candidate** until that read.

If the `saba` tools are absent from your tool list, follow Prerequisite, then start step 1.

## The grounding rule

**Allowed sources**: output of the Saba MCP tools (`search_vector`, `search_lexical`, `read`, `list_resources`, `install`), and files already in the user's repository.

**Not allowed**: web search or fetch, other MCP servers, and verses recited from memory.

**Citations**: every reported verse carries its resource id and reference, `[en_ult PSA 23:1]`. A verse enters the report only after you `read` it in this session.

**Gaps**: when these searches do not find it, say "not found by the searches run".

## Workflow

### 1. Classify the query

| The user gave... | Do this |
|---|---|
| A topic, idea, or question ("verses about hope") | `search_vector` |
| Exact words, a quote, or a half-remembered line | `search_lexical` with those words |
| A specific Bible (`eng_esv`, `hbo_uhb`, `el-x-koine_ugnt`) | `search_lexical` with `resource` set |
| A bare reference ("Romans 8:28") | `read` it. For its meaning, use `saba-bible-exegesis` |

A reference you remember is a **direct lookup**: `read` it, and if it holds, report it under Direct lookup.

**Done when** the query sits in one row and that row's tool is the one you will call.

### 2. Search

When the row says `search_vector`, call it with the user's words, `limit` 20 (30 for a broad topic). Call it straight away. It errors if the model is missing, and that error is the signal to install.

`search_vector` searches every Search index already on the machine. `search-en-ult` is the model plus the ULT index. Westminster and Schaff are separate ids (`search-en-wcf`, `search-en-wlc`, `search-en-wsc`, `search-en-schaff-nicea`, `search-en-schaff-chalcedon`, `search-en-schaff-heidelberg`); they appear in the hits only after they are installed.

On the error "Topic search model is not downloaded":

1. Tell the user `search-en-ult` is about 220 MB, downloaded once.
2. Call `install` with `{"ids": ["search-en-ult"]}`. Wait for it to return.
3. Call `search_vector` again with the same query.

If the user declines, or the tool returns any other error, call `search_lexical` and label the results **lexical-only**. Say what the other error was.

When the row says `search_lexical`, call that and skip the install path.

**Done when** the chosen tool has returned candidates, or a declined or failed vector search has lexical-only candidates in hand.

### 3. Widen

- Rephrase a topic once or twice in the ULT's register and run `search_vector` again ("counted as righteous", "steadfast love"). Let the first candidates teach you the wording.
- Take a distinctive 2 to 4 word phrase from a strong candidate and run `search_lexical` with it.
- For cross-references, `read` the named passage first, then search its ideas and its distinctive phrases.

Merge candidates and drop duplicates.

**Done when** one rephrasing or one phrase search has been merged into the candidate list. A bare reference is already done.

### 4. Read every candidate you will report

`read` each one on its Bible with a verse or two either side. A ULT hit is `en_ult`. A hit that carries a locus is read with `read_commentary` on its resource. Drop a candidate that in context is about something else, and note why in one line. Keep a one-line reason, from that read, for each one you keep.

When `list_resources` shows another Bible installed, you may `read` the kept verses there too so the user sees familiar wording. Cite that id.

Report as many as the user asked for. Otherwise keep 10 to 15.

**Done when** every kept candidate has that read and a one-line reason, every dropped candidate has a one-line reason, and the kept set is within the cap.

### 5. Check the user's own notes

Search the repo for the topic's terms. Verses the user already collected go under their own heading, cited by path, each one `read` before it is listed as Scripture.

**Done when** the repo search has been run, and each verse in those notes has been read or the search found nothing.

### 6. Report

The line under each verse says why it matched. Meaning belongs to `saba-bible-exegesis`. Doctrine belongs to `saba-bible-theology`.

Order by relevance from the reads. Group Verses by sub-theme when there are more than ten. An empty kept set says "not found by the searches run" and lists the queries.

```markdown
# <Query>

## Verses
1. **<REF>** <text> [<resource> <REF>]
   <One line: why it answers the query, from the read.>
2. ...

## From your notes
- <path>: <verses the user already collected> [notes/...]

## Direct lookup
- <REF> <text> [<resource> <REF>]

## Searched
- search_vector: "<query>", "<rephrasing>"
- search_lexical: "<phrase>" (<resource>)
- <Lexical-only, when vector search was declined or failed.>
```

**Done when** every section is present or marked empty, and every verse line carries a citation from a read in this session.

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
