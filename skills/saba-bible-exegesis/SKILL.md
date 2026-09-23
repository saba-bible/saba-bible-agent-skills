---
name: saba-bible-exegesis
description: Grounded exegesis of a Bible passage using only Saba MCP resources (ULT, UST, Hebrew and Greek texts, Translation Notes, Translation Questions, Matthew Henry, Calvin, Keil and Delitzsch) plus the user's own files in the repo. Use when the user asks what a passage means, asks for exegesis or a verse-by-verse study, asks about the wording or structure of a specific reference, or asks "what does the text say" about a verse or chapter. Never web search and never fill gaps from background knowledge; every claim is cited to a Saba resource or a repo file.
---

# Saba Bible Exegesis

Exegete a passage from the text out, using only what the Saba MCP returns and what the user has already written in this repo. The output is a study a reader can check: every observation points at the resource and reference it came from.

## The grounding rule

**Allowed sources**

- Output of the Saba MCP tools: `read`, `read_commentary`, `search_vector`, `search_lexical` (and `list_resources` / `install` to get them ready).
- Files already in the user's repository (notes, outlines, prior studies). Read them with the file tools.

**Not allowed**

- Web search, web fetch, or any other MCP server.
- Your own recollection of commentaries, lexicons, scholarship, historical background, or what a Hebrew or Greek word "means". If a Saba resource does not say it, you do not say it.
- Original-language morphology or lexical claims from memory. You may **quote** the Hebrew or Greek that `read` returned and you may **compare forms** across verses (same string, different string, found by `search_lexical` on the original text). Any gloss, parsing, or semantic claim must come from a note (`en_tn` glosses and explains words; `en_kd` and `en_calvin` discuss the original).

**What you may bring yourself**: reading comprehension of the English and reasoning over what the sources say (sequence, contrast, repetition, who is speaking to whom). Label it as your observation of the text, cited to the verses you read.

**Citations**: every interpretive claim ends with a bracket: `[en_tn ROM 8:28]`, `[en_calvin ROM 8:28-30]`, `[en_ult ROM 8:18-39]`, `[notes/romans.md]`. No citation, no claim.

**Gaps**: when no installed resource addresses something, write "Not covered by the installed Saba resources" and, if a listed-but-uninstalled resource might, name its id. Do not fill the gap.

**No Saba tools?** Stop. Tell the user to install the MCP server (`npm install -g saba-bible-mcp`, then add `{"mcpServers":{"saba":{"command":"saba-bible-mcp"}}}` to their agent's MCP config). Do not answer from memory in the meantime.

## Workflow

Copy this checklist into your working notes and tick it off.

```
- [ ] 1. Fix the passage and its context span
- [ ] 2. Make sure the resources are installed
- [ ] 3. Read the text (ULT, UST, original)
- [ ] 4. Read the notes and commentaries on the span
- [ ] 5. Find intertext by search, then read the hits
- [ ] 6. Read the user's own notes in the repo
- [ ] 7. Write up (template below)
```

### 1. Fix the passage

State the reference in USFM form (`ROM 8:28-30`). Decide the **context span** to read around it: the whole chapter by default, or the paragraph unit if the user gave a long passage. If the user gave a topic rather than a reference, this is the wrong skill: theological questions belong to `saba-bible-theology`.

### 2. Make sure the resources are installed

Call `list_resources`. Then call `install` with the ids you need that show `installed: false`:

- Always: `en_ult`, `en_ust`, `en_tn`, `en_tq`.
- Old Testament passage: `hbo_uhb`, `en_kd`, `en_mhc`, `en_calvin`.
- New Testament passage: `el-x-koine_ugnt`, `en_mhc`, `en_calvin`.
- Only if you will use `search_vector` in step 5: `search-en-ult` (about 220 MB; say so before installing it).

Do not install `all`. If the user has downloaded a copyrighted Bible (`eng_esv`, `eng_niv2011`, ...) it appears in `list_resources` as installed; read it too, and cite it by id.

### 3. Read the text

- `read` on `en_ult` for the **context span**, then note where the passage sits in it.
- `read` on `en_ust` for the **passage**. Where UST renders differently from ULT, that difference is a data point about meaning: record it.
- `read` on the original (`hbo_uhb` or `el-x-koine_ugnt`) for the **passage**. Quote it as returned.

Read before you cite. Never cite a verse you did not read in this session.

### 4. Read the notes and commentaries

Call `read_commentary` on the **passage** span for each of: `en_tn`, `en_tq`, `en_mhc`, `en_calvin`, and `en_kd` (OT only). An empty result is normal; record "no notes" for that resource. Each returned line begins with its verse tag; keep that tag in your citation.

`en_tn` is the main source for word-level and figure-of-speech observations (it often quotes the original word and glosses it). `en_tq` gives the questions the text is expected to answer: useful for checking your synthesis covers what the passage is about.

### 5. Find intertext

- `search_lexical` with a distinctive **phrase** from the ULT text (2 to 4 words) to find where else the ULT uses that wording.
- `search_lexical` with `resource` set to the original text and the **exact form** you quoted in step 3, to find other occurrences of that form. This is a form match, not a lemma match; say so when you report it.
- `search_vector` with the passage's **idea** in plain words, only if the index is installed.

Search hits are candidates, not evidence. `read` each hit you intend to use, in its own context, before citing it. Drop hits that turn out to be about something else.

### 6. Read the user's own notes

Search the repo for the reference in the forms people write it (`Romans 8`, `Rom 8:28`, `ROM 8`) and for the passage's key terms. Read what you find. Cite it by path and treat it as **the user's prior work**, kept distinct from Saba resources in the write-up. Do not treat a user note as settling a question that the Saba resources leave open; report both.

### 7. Write up

Use this template. Keep the sections; drop a subsection only when it is genuinely empty, and say so.

```markdown
# <Reference> (<ULT>)

## Text
<ULT, one verse per line, as returned by read>

## Context
<Where the passage sits in the chapter: what precedes, what follows, who is speaking to whom. Cite the verses you read.> [en_ult ...]

## Observations, verse by verse
### <ref>
- ULT / UST: <difference in rendering, if any> [en_ult ...] [en_ust ...]
- Original: <quoted form> ... <what a note says about it> [en_tn ...]
- Notes: <en_tn / en_tq points> [en_tn ...]
- Commentary: <Matthew Henry, Calvin, Keil and Delitzsch, each on its own line> [en_mhc ...] [en_calvin ...] [en_kd ...]

## Intertext
- <ref>: <what it shares with the passage and why it matters, from having read it> [en_ult ...]

## Your notes
- <path>: <what the user already wrote on this passage> [notes/...]

## Synthesis
<What the passage says, in the flow of the author's argument, built only from the sections above. Where commentators differ, present both and do not adjudicate from outside the sources.>

## Not covered
- <Question the reader would ask that no installed resource answers. Name a listed resource id if one might.>

## Sources
<Every resource id and reference span read in this session, one per line.>
```

## Guardrails

- Quote sources; do not let paraphrase drift into your own view. If you summarize a commentator, keep it close to their words and cite the tag.
- Where Matthew Henry, Calvin, and Keil and Delitzsch disagree, report the disagreement. Do not resolve it with a fourth opinion.
- Do not introduce historical, cultural, or geographic background unless a note supplies it.
- Do not import a doctrinal framework the text and notes do not use. If the user's notes use one, attribute it to them.
- Long passages: exegete in units (paragraphs), each with its own reads, rather than skimming the whole.
