---
name: "placeholder-name"
description: "Editable Handbook: placeholder-description"
---

# Editable Handbook: placeholder-name

placeholder-description

A directory of atomic markdown entries on a single topic that you are expected to
both read and write. Unlike other skills, handbooks grow in place as you encounter
information worth keeping.

## Reading

This handbook exposes three tiers of detail, loaded progressively:

1. **Skill frontmatter** (`name`, `description`) — already in your skill catalog.
2. **Table of contents — run `tree entries/` once, immediately on activation.** Each
   filename _is_ its concept (`oauth-flow.md` = the `oauth-flow` concept). Do this
   before your first query; everything below depends on knowing the shape.
3. **Individual entries** — read them when you need the content.

Between (2) and (3), use grep to jump straight to concepts:

- `grep -rli <concept> entries/` — list entries mentioning the concept.
- `grep -riC 3 <concept> entries/` — snippet with surrounding lines when that's enough.
- Follow `Related:` lines at the bottom of each entry by grepping each term.

Empty grep result = the concept is not yet in the handbook. That's a valid signal
you may be about to create a new entry.

## Writing

Before writing _anything_, grep first: `grep -rli <concept> entries/`.

**If the concept already has an entry**, read the file in full, then edit in place.
Preserve the existing `Related:` line and append new references rather than
rewriting it.

**If no entry exists**, create `entries/<concept>.md`. The filename must be the same
identifier you would grep for — lowercase-kebab-case (`oauth-flow.md`, not
`OAuth Flow.md` or `oauthFlow.md`). The filename _is_ the concept.

## Entry shape

    # OAuth Flow

    <prose, code, lists — whatever serves the concept>

    Related: pkce, refresh-tokens, csrf-mitigation

- `# H1` is the human-readable concept name.
- The trailing `Related:` line is required and lists kebab-case concept identifiers,
  comma-separated. At least one reference is required; if nothing plausible already
  exists, use a forward reference to what the related concept _would_ be called.
- A `Related:` target that grep doesn't find is a _forward reference_. That's fine —
  it just means nobody has written that entry yet.

## Do not

- **Rename entry files.** `Related:` lines reference filenames as free text; renames
  silently break back-references. To replace an entry, write a new one and edit the
  old to say "Superseded by `<new-concept>`."
- **Create synonyms.** Before writing `oauth2.md`, grep for `oauth`. If
  `oauth-flow.md` exists, update it instead of branching.
- **Delete entries silently.** Edit in place; don't remove content that other
  entries link to.

## Why this shape

Grep is the index. `Related:` lines are the link graph. Read-before-edit is enforced
by the harness anyway, so every write sees existing terminology — which is what
keeps the concept space from drifting. No database, no embeddings, no registry; just
markdown and shell tools you already have.
