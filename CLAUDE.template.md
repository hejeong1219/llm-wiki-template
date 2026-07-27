# LLM Wiki — [YOUR FIELD]

A personal knowledge base of [YOUR FIELD] papers, following [Karpathy's LLM Wiki pattern](https://gist.github.com/karpathy/1dd0294ef9567971c1e4348a90d69285):

```
Original PDF → sources/*.md (LLM summary) → wiki/{category}/*.md (final page)
```

**Language policy**: All wiki content is in English. Conversation can be in any language.

---

## THE FOUR RULES (do not violate)

These rules are the core of the system. They prevent hallucination and keep every claim traceable.

1. **No web search.** Never use `WebSearch` or `WebFetch` to fill gaps. The point of this wiki is that every answer is grounded in papers we actually have.
2. **Answer from the wiki first.** Use `sources/` and `wiki/` as the only sources of truth.
3. **If the wiki is insufficient, re-read the PDF.** Go to `papers/{author}-{year}-{words}.pdf` and extract more detail with `pypdf`. Then update the wiki.
4. **If the wiki has no paper on the topic, say so.** Tell the user *"I don't have a paper on this — please give me the PDF."* Do not improvise.

These rules apply to **every** response, including overview pages: cite only papers that exist in the wiki.

---

## Repository Structure

```
your-llm-wiki/
├── CLAUDE.md               # This file
├── index.md                # Page catalog
├── log.md                  # Append-only change log
├── papers/                 # Original PDFs (cp, never symlink)
│   └── {author}-{year}-{title-5-words}.pdf
├── sources/                # PDF summaries (English)
│   └── {author}-{year}-{title-5-words}.md
├── wiki/                   # Wiki pages (English)
│   ├── {category}/
│   ├── concepts/           # Reusable concepts and definitions
│   ├── methods/            # Reusable method cards
│   ├── topics/             # Question-centered topic hubs
│   │   └── {question-slug}/
│   │       └── agenda.md   # Current judgment and next reading
│   ├── analyses/           # Question-driven comparisons and judgments
│   └── overviews/          # Synthesis pages (where compounding happens)
└── materials/              # Optional lab context, not scientific evidence
    ├── writing-style/
    ├── journals/
    ├── lectures/
    └── meetings/
```

## File Naming Convention

All three tiers (PDF, source, wiki) share the same stem:

```
{first-author-lastname}-{year}-{first-5-title-words}.{ext}
```

- Lowercase, special chars stripped, spaces → `-`
- Year is 4 digits
- Consortium papers: use consortium name (e.g. `1000-genomes-project-2015-...`)

Example: `pollard-2006-an-rna-gene-expressed-during.pdf`

## Categories

> **Edit this section.** Define 5–10 categories that match your research domain. Start small; split when one category passes ~500 files.

| Category | Includes |
|---|---|
| `[your-category-1]` | [what kind of papers go here] |
| `[your-category-2]` | [...] |
| `[your-category-3]` | [...] |
| `concepts` | Key methods, algorithms explained generically |
| `methods` | Reusable method cards |
| `topics` | Question-centered topic hubs and agendas |
| `analyses` | Comparisons and evidence-based judgments |
| `overviews` | Synthesis pages spanning multiple papers |
| `other` | Cross-cutting, miscellaneous |

Tip: classify by **method**, not topic. A methylation paper studying a phenotype goes to `methylation` (or your method-aligned category), not the phenotype's category.

---

## Adding a New Paper

### Step 1 — Copy PDF to `papers/` and extract text

Use `pypdf` (pure Python, no Java required):

```bash
pip3 install pypdf

python3 -c "
import pypdf, sys
reader = pypdf.PdfReader(sys.argv[1])
text = ''
for page in reader.pages[:15]:
    t = page.extract_text()
    if t: text += t + '\n'
    if len(text) > 12000: break
print(text[:12000])
" "/path/to/paper.pdf"
```

### Step 2 — Write `sources/{stem}.md`

```yaml
---
title: "Paper Title"
authors: Author List
year: YYYY
doi: DOI
category: [your-category]
pdf_path: /full/path/to/papers/{stem}.pdf
pdf_filename: {stem}.pdf
source_collection: external
---

## One-line Summary
## 1. Document Information
## 2. Key Contributions
## 3. Methodology and Architecture
## 4. Key Results and Benchmarks
## 5. Limitations and Future Work
## 6. Related Work
## 7. Glossary
```

### Step 3 — Write `wiki/{category}/{stem}.md`

```yaml
---
title: "Paper Title"
authors: Author list
year: YYYY
doi: DOI
source: {stem}.md
category: [your-category]
pdf_path: /full/path/to/papers/{stem}.pdf
pdf_filename: {stem}.pdf
source_collection: external
tags: []
---

## Summary
## Key Contributions
## Methodology and Architecture
## Results
## Related Papers
- [[category/page]] — relationship
```

### Step 4 — Update `index.md`

Add a one-line entry under the right category.

### Step 5 — Update `log.md`

Append a short entry describing what changed and why.

---

## PDF Management Rules

- **Always copy, never symlink.** `cp` from external locations into `papers/`.
- `pdf_path` always points inside `papers/`. Never use `~/Downloads/` or other external paths.
- `pdf_filename` must match `basename(pdf_path)`.

## Knowledge Compounding

The most valuable pages are not individual paper summaries — they are `wiki/overviews/` pages that synthesize across papers. When a question is answered well, save the answer:

> "Save this as an overview page in `wiki/overviews/`"

Each conversation should produce 5–15 new or updated wiki pages. Over time the wiki becomes a searchable, cross-referenced knowledge graph that future conversations draw from.

## Additional Wiki Page Types

- `wiki/concepts/`: recurring concepts and definitions
- `wiki/methods/`: method cards describing inputs, operations, outputs, and limitations
- `wiki/topics/`: pages that connect sources around a research question
- `wiki/topics/{question-slug}/agenda.md`: current judgment, decision rules, hold-back, and next reading
- `wiki/analyses/`: question-driven comparisons and evidence-based judgments
- `materials/`: optional writing, journal, lecture, and meeting context; never use it as scientific evidence

Create or update only the page types that gain useful information from the source.

## Initial Wiki Setup

When the user asks to build a new LLM Wiki from this template:

1. Ask for the user's research field and the 5–10 categories they want to use.
2. Replace `[YOUR FIELD]` and the category placeholders only after the user answers.
3. Save this rule file as `CLAUDE.md` when using Claude Code or `AGENTS.md` when using Codex.
4. Create the fixed folders shown under Repository Structure:
   - `papers/`
   - `sources/`
   - `wiki/concepts/`
   - `wiki/methods/`
   - `wiki/topics/`
   - `wiki/analyses/`
   - `wiki/overviews/`
   - `materials/writing-style/`
   - `materials/journals/`
   - `materials/lectures/`
   - `materials/meetings/`
5. Create empty `index.md` and `log.md` files without inventing paper entries or scientific content.
6. Do not create a literal `{category}` or `{question-slug}` folder. Create those folders only after the category or research question is known.
7. Report the completed folder structure and identify any folders intentionally deferred until papers or research questions are provided.

## Browsing with Obsidian

For visual navigation, the user can install [Obsidian](https://obsidian.md/) (free, Mac/Windows/Linux) and open the wiki folder as a Vault. Native support for `[[wikilinks]]`, graph view, and full-text search. Recommend this whenever the user asks how to read or browse the wiki — Obsidian only reads files, so it does not interfere with the agent's edits.

---

## Design Principles

- **3-tier**: Raw PDF (immutable) → sources/*.md → wiki/**/*.md
- **English only** in wiki content (RAG-friendly)
- **Obsidian compatible**: `[[wikilinks]]`, plain markdown
- **Consistent YAML**: every file has title, authors, year, doi, category, pdf_path, pdf_filename, source_collection
- **Traceable evidence**: preserve page, section, figure, table, or supplement locations when available; use `not reported` for missing details
- **Change history**: update `log.md` when a meaningful page or judgment changes
- **No web search**: rule #1 above

When in doubt, follow rule #1.
