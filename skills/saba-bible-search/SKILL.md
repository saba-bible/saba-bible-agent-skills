---
name: saba-bible-search
description: Find Bible verses on a topic, idea, or phrase with the Saba MCP search tools, preferring search_vector (keyword plus semantic search over the ULT) and using search_lexical only for exact quotes, other Bibles, or when the index is missing. Use when the user asks for "verses about X", "where does the Bible say...", "find the verse that says...", wants a half-remembered passage located, or needs cross-references for a study. Every hit is read in context before it is reported; no web search, no verses recited from memory.
---

# Saba Bible Search

Find verses with the Saba MCP and report only what you read. `search_vector` first; `search_lexical` when the query is an exact quote, targets a Bible other than the ULT, or the index is not installed.

## The grounding rule

**Allowed sources**: output of the Saba MCP tools (`search_vector`, `search_lexical`, `read`, `list_resources`, `install`, `check_index`), and files already in the user's repository.

**Not allowed**: web search or fetch, other MCP servers, and verses or references recited from memory. A reference you happen to remember is a **hypothesis**: check it with `read` and, if it holds, report it labeled "direct lookup", not as a search result.

**Citations**: every reported verse carries its resource id and reference: `[en_ult PSA 23:1]`. Never report a verse you did not `read` in this session.

**No Saba tools?** Stop. Tell the user to install the MCP server (`npm install -g saba-bible-mcp`, then add `{"mcpServers":{"saba":{"command":"saba-bible-mcp"}}}` to their agent's MCP config). Do not answer from memory in the meantime.

## Workflow

```
- [ ] 1. Classify the query
- [ ] 2. Search (vector first)
- [ ] 3. Widen with rephrasings and a lexical pass
- [ ] 4. Read every hit you will report
- [ ] 5. Check the user's own notes
- [ ] 6. Report
```

### 1. Classify the query

| The user gave... | Do this |
|---|---|
| A topic, idea, or question ("verses about hope", "God comforting mourners") | `search_vector` |
| Exact words, a quote, or "the verse that says..." | `search_lexical` with the quoted words |
| A Bible other than the ULT (ESV they downloaded, `hbo_uhb`, `el-x-koine_ugnt`) | `search_lexical` with `resource` set; the vector index covers the ULT only |
| A bare reference ("Romans 8:28") | Not a search: `read` it. If they want its meaning, use `saba-bible-exegesis` |

### 2. Search, vector first

Call `search_vector` with the query as the user phrased it, `limit` 20 (raise to 30 for broad topics).

If it errors because the index is not installed: tell the user `search-en-ult` is about 220 MB and `install ["search-en-ult"]` unless they decline. If they decline, or while it downloads, run `search_lexical` instead and **label the results lexical-only** in the report. Never fall back silently.

### 3. Widen

- Rephrase once or twice in the ULT's register and run `search_vector` again. The ULT is literal: "counted as righteous" not "justified", "steadfast love" not "mercy" in many Psalms. Let the first hits teach you the wording.
- Take a distinctive 2 to 4 word phrase from a good hit and run `search_lexical` with it to catch verses that use the same wording.
- For cross-references to a passage the user named, `read` the passage first, then search with its ideas and its distinctive phrases.

Merge hits and drop duplicates. Search hits are candidates, not results.

### 4. Read every hit you will report

For each candidate you intend to report, `read` it on `en_ult` with a verse or two either side. Drop candidates that in context are about something else. Keep the ones that answer the query, and note in one line why each does, from what you read.

If the user has another Bible installed (`list_resources` shows it), you may `read` the kept verses there too so they see familiar wording; cite that id.

### 5. Check the user's own notes

Search the repo for the topic's terms. If the user already collected verses on it, include them under their own heading, cited by path, and check each against `read` before listing it as Scripture.

### 6. Report

```markdown
# <Query>

## Verses
1. **<REF>** <ULT text> [en_ult <REF>]
   <One line: why it answers the query, from context.>
2. ...

## From your notes
- <path>: <verses the user already collected> [notes/...]

## Direct lookup
- <REF> <ULT text> [en_ult <REF>]  (checked by read, not surfaced by search)

## Searched
- search_vector: "<query>", "<rephrasing>"
- search_lexical: "<phrase>" (<resource>)
- <Lexical-only, if the vector index was unavailable.>
```

Group the Verses section by sub-theme when there are more than ten. Order by relevance as judged from reading, not by raw search rank. If nothing survives step 4, say so and list the queries you ran.

## Guardrails

- The vector index is ULT-only. Do not claim a topic is "not in the Bible" from an empty vector result; try rephrasings and a lexical pass first, then say "not found by the searches run".
- Do not pad the list with verses you remember. Direct lookups go in their own section, verified by `read`.
- Do not interpret. Say why a verse matched; leave meaning to `saba-bible-exegesis` and doctrine to `saba-bible-theology`.
- Long lists: cap at what the user asked for; default 10 to 15 verified verses.
