---
name: saba-bible-theology
description: Grounded theological reasoning on a doctrine or question using only Saba MCP resources (Scripture found by search and read in context, the Westminster Confession and Catechisms' Scripture proofs, Calvin, Matthew Henry, Keil and Delitzsch, Translation Notes) plus the user's own files in the repo. Use when the user asks what the Bible teaches about a topic, asks a doctrinal or systematic theology question, wants Scripture support for a confessional statement, or wants a theological claim tested against Scripture. Never web search and never reason from background theology; every claim is cited to a Saba resource or a repo file.
---

# Saba Bible Theology

Answer a theological question from Scripture and the confessional and commentary witnesses that the Saba MCP can read, and from nothing else. The output separates four things a reader must be able to tell apart: what the texts say, what follows from them, what the confessions and commentators say, and what the resources leave open.

## The grounding rule

**Allowed sources**

- Output of the Saba MCP tools: `search_vector`, `search_lexical`, `read`, `read_commentary` (and `list_resources` / `install` to get them ready).
- Files already in the user's repository (notes, position papers, prior studies). Read them with the file tools.

**Not allowed**

- Web search, web fetch, or any other MCP server.
- Your own recollection of systematic theologies, confessions, church history, denominational positions, or what theologians have said. If a Saba resource does not say it, you do not say it.
- Original-language lexical or grammatical claims from memory. Quote the Hebrew or Greek that `read` returned if it helps; any gloss or parsing must come from a note (`en_tn`, `en_kd`, `en_calvin`).
- Technical theological terms the sources do not use. If you need a term such as "justification" or "covenant of grace", tie it to a text, a confessional proof line, or a commentator that uses it, and cite that.

**What you may bring yourself**: reading comprehension and logical inference over what the sources say. Every inference is written out as an inference ("A says X; B says Y; taken together...") so the reader can reject it.

**Citations**: every claim ends with a bracket: `[en_ult ROM 3:28]`, `[en_wcf ROM 3:28]`, `[en_calvin GAL 2:16]`, `[notes/justification.md]`. No citation, no claim.

**Gaps**: when no installed resource addresses part of the question, write "Not covered by the installed Saba resources" and, if a listed-but-uninstalled resource might, name its id. Do not fill the gap.

**No Saba tools?** Stop. Tell the user to install the MCP server (`npm install -g saba-bible-mcp`, then add `{"mcpServers":{"saba":{"command":"saba-bible-mcp"}}}` to their agent's MCP config). Do not answer from memory in the meantime.

## Workflow

Copy this checklist into your working notes and tick it off.

```
- [ ] 1. Frame the question without answering it
- [ ] 2. Make sure the resources are installed
- [ ] 3. Gather Scripture by search, then read every hit in context
- [ ] 4. Read the confessional proofs on the key passages
- [ ] 5. Read the commentaries on the key passages
- [ ] 6. Read the user's own notes in the repo
- [ ] 7. Reason, then write up (template below)
```

### 1. Frame the question

Restate the question in one sentence and list the sub-questions it contains (two to five). Note any terms in the user's wording that will need a source before you can use them. **Do not draft an answer yet.** The answer is built from what steps 3 to 6 return.

If the user gave a single passage and asked what it means, this is the wrong skill: use `saba-bible-exegesis`.

### 2. Make sure the resources are installed

Call `list_resources`. Then call `install` with the ids you need that show `installed: false`:

- Always: `en_ult`, `en_ust`, `en_wcf`, `en_wlc`, `en_wsc`, `en_calvin`, `en_mhc`, `en_tn`.
- If the key passages are Old Testament: `en_kd`, `hbo_uhb`.
- If the key passages are New Testament: `el-x-koine_ugnt`.
- `search-en-ult` for `search_vector` (about 220 MB; say so before installing it). Without it, gather with `search_lexical` only.

Do not install `all`. If the user has downloaded a copyrighted Bible (`eng_esv`, `eng_niv2011`, ...) it appears in `list_resources` as installed; you may read it too, cited by id.

### 3. Gather Scripture

- `search_vector` with the question in plain words, then again with two or three rephrasings and with each sub-question. Use `limit` 20 to 30.
- `search_lexical` with the key terms in the user's wording and in the ULT's wording as you discover it (`justified`, `counted as righteous`).
- Also search for the **counter-case**: phrasings a reader would use to argue the other way. A theology that only searched for its conclusion is not grounded.

Search hits are candidates, not evidence. For every hit you intend to use, `read` it on `en_ult` **with surrounding context** (the paragraph, at least a few verses either side) and, for the central passages, on `en_ust` as well. Drop hits that in context are about something else. Keep a list of the passages that survived: these are the **key passages** for steps 4 and 5.

### 4. Read the confessional proofs

For each key passage, call `read_commentary` on `en_wcf`, `en_wlc`, and `en_wsc`. Each returned line is the Confession section, or the Catechism question and answer, that cites that verse as a proof text. This shows how the Westminster Standards use the passage; it is the only route into the confessional texts through the MCP (the `book/wcf`, `book/wlc`, `book/wsc`, and creed ids are listed but not readable yet).

An empty result means the Standards do not cite that verse. Report it as such; it is a data point, not a failure.

### 5. Read the commentaries

For each key passage, call `read_commentary` on `en_calvin`, `en_mhc`, `en_tn`, and `en_kd` (OT only). Record what each says that bears on the question, close to their words, with the verse tag. Record disagreements between them as disagreements.

### 6. Read the user's own notes

Search the repo for the topic's terms and for the key passages' references. Read what you find. Cite it by path and treat it as **the user's stated position or prior work**, kept distinct from Scripture and from the Saba witnesses in the write-up. If the user's notes assert something the gathered texts do not support, say so plainly and cite both.

### 7. Reason, then write up

Build the answer only from steps 3 to 6. Use this template. Keep the sections; drop a subsection only when it is genuinely empty, and say so.

```markdown
# <Question, restated>

## Short answer
<Two to four sentences. Only what the sections below support. If the resources do not support a confident answer, say that here.>

## Scriptural basis
### <Sub-question 1>
- <ref>: <what the text says, in context, close to the ULT wording> [en_ult ...]
- ...
### <Sub-question 2>
- ...

## Counter-texts and tensions
- <ref>: <what a reader could set against the answer, from having read it> [en_ult ...]
  <How the sources handle it, if they do> [en_calvin ...] [en_wlc ...]

## Confessional witness
- <ref>: <Confession section or Catechism Q&A that cites it> [en_wcf ...] [en_wlc ...] [en_wsc ...]
- (or) The Westminster Standards do not cite any of the key passages.

## Commentators
- Calvin on <ref>: <close summary> [en_calvin ...]
- Matthew Henry on <ref>: ... [en_mhc ...]
- Keil and Delitzsch on <ref>: ... [en_kd ...]

## Your notes
- <path>: <the user's stated position or prior study> [notes/...]

## Reasoning
<The chain from texts to answer, written as numbered inferences. Each step names the texts it rests on. Mark each step as either "stated" (a text or witness says it) or "inferred" (you derived it).>

## Not covered
- <Part of the question no installed resource addresses. Name a listed resource id if one might.>

## Sources
<Every resource id and reference span read in this session, one per line.>
```

## Guardrails

- No proof-texting. A verse enters the basis only after you read it in context in this session.
- Do not let the Short answer say more than the Reasoning supports. Write the Reasoning first.
- Where the confessions, Calvin, Matthew Henry, or Keil and Delitzsch differ from one another or from what the texts appear to say, report it. Do not adjudicate with an outside authority.
- Do not supply historical development of a doctrine, names of theologians, or denominational labels unless a Saba source supplies them.
- Do not treat the user's notes as Scripture, and do not treat the Westminster Standards as Scripture: keep the three layers (text, witness, user) visibly separate.
- Broad questions: work sub-question by sub-question, each with its own searches and reads, rather than one search for the whole.
