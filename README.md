# editable-handbooks

An **Editable Handbook** is a skill-shaped directory of atomic markdown entries on a single topic that agents both read and write.

```
my-handbook/
  SKILL.md
  entries/
    a-concept.md
    something-else.md
    …
```

It's a memory format that composes with existing skill conventions and requires no harness changes — any skill-aware harness can install a handbook as a skill.

The standard lives in one file: [`template/SKILL.md`](template/SKILL.md). Its body is the bootstrap protocol every handbook shares verbatim; only the frontmatter (`name`, `description`) varies per handbook. The body teaches an agent how to find information (three-tier disclosure via `tree` + grep + `Related:` lines), how to add information (read-before-edit, one file per concept), and what not to do (rename, create synonyms, delete silently).

## Motivation

- **Composable.** Handbooks are installable as skills, so any skill-aware harness picks them up without modification. Multiple handbooks coexist in a project; the agent activates the ones relevant to the current task.

- **Atomic updates.** A single bash command adds, updates, or removes a single entry. One markdown file per concept; no cross-file state to keep in sync.

- **Internally consistent.** Consistency emerges without a background compaction pass. `Related:` lines form the link graph, grep is the index, and read-before-edit (enforced by every harness) means each write sees existing terminology before
 extending it. Forward references are legitimate — a `Related:` term grep can't find yet is a signal, not an error, and likely points to the next entry to write.

- **Self-describing.** Progressive disclosure in three tiers: frontmatter metadata sits in the skill catalog, the agent runs `tree entries/` on activation, and reads individual entries on demand. Relevant entries surface as the task evolves.

- **Curated and learned content coexist.** A handbook can start empty or fork an existing one. Either way, it accretes project-specific marginalia and new entries as it's used.

## Creating a new handbook

1. Pick a kebab-case name (e.g. `rust-async-handbook`) and a description. The description should explain what the handbook covers, which concepts belong (and optionally which don't, to prevent drift), when an agent should browse it, and when an agent should update it. It's injected into both the skill catalog (tier 1, to trigger activation) and the body of `SKILL.md` (tier 2, to orient the activated agent about scope), so write it to be useful in both contexts.

2. Create the directory and materialize the template:

   ```bash
   name=rust-async-handbook
   description="Idioms, gotchas, and decision trees for async Rust. In scope: tokio, async traits, Send/Sync constraints, cancellation, pinning. Out of scope: non-async Rust. Browse when debugging or designing async code; update when you discover a new gotcha worth preserving."
   mkdir -p "$name/entries"
   t=$(<template/SKILL.md)
   t=${t//placeholder-name/$name}
   t=${t//placeholder-description/$description}
   printf '%s\n' "$t" > "$name/SKILL.md"
   ```

   The bash pattern-substitution form (`${var//pattern/replacement}`) is literal, not regex, so the name and description can contain `|`, `&`, `/`, `\`, `*`, and other shell/sed metacharacters without escaping.

3. Install `$name/` wherever your harness picks up skills
   (`~/.claude/skills/`, `.agents/skills/`, etc.).

Your handbook starts empty. It grows as the agent encounters information worth keeping, one `entries/<concept>.md` file at a time.
