# Knowledge Wiki — Master Index

**Start here.** This is the master index of every wiki page and how they relate.
It implements Andrej Karpathy's LLM-wiki pattern: `raw/` (frozen sources) →
`wiki/` (processed knowledge) → this index, with `log.md` recording each ingest.

## The three layers

- **`raw/`** — immutable source documents. Never edit; treat as frozen inputs.
- **`wiki/`** — processed knowledge. One markdown page per concept, cross-linked
  with `[[page-name]]` (the filename without `.md`).
- **`index.md`** (this file) — the map: every page, its one-line summary, and the
  relationship map below.
- **`log.md`** — append-only log of every ingest (a raw source turned into wiki pages).

## How to search (answer a question)

1. Scan the page table below.
2. Open the relevant `wiki/*.md` page and follow its `[[links]]`.
3. Open `raw/` or the code only to verify a detail the wiki is missing.

## How to update (after a change)

1. Find the affected page(s) via this index.
2. Update the page to match the new source of truth (code wins; if they disagree,
   the wiki is stale — fix it).
3. Update this index if a page was added/removed or a relationship changed.
4. Append one line to `log.md`.

## Pages

| Page | Summary |
|---|---|
| _none yet_ | Add wiki pages here as they're created. |

## Relationship map

_Describe how the pages connect as the wiki grows (which concepts depend on which)._
