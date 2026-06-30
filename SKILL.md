---
name: latex
description: >
  Governs how Claude writes, edits, reviews, and gives advice about LaTeX.
  Use this skill for ANY task involving .tex files — no exceptions. This
  includes: producing new .tex files of any type (worksheets, formula sheets,
  handouts, reports, problem sets, exams, multi-file projects); editing or
  auditing existing LaTeX source; reviewing someone's LaTeX for correctness or
  style; and giving LaTeX advice or recommendations without producing a file
  (e.g. "should I use align or equation here?", "what's the right way to
  typeset units?", "how do I structure a multi-chapter project?"). Trigger
  whenever the user mentions LaTeX, .tex files, pdflatex, XeLaTeX, LuaLaTeX,
  Overleaf, TeX Live, MiKTeX, compiling a document, or asks Claude to write
  math, tables, or structured technical documents in LaTeX. Does NOT encode
  opinions about information density, whitespace, or document class — those
  belong in project-level philosophy files.
---

# LaTeX Skill

## Purpose and Scope

This skill defines how Claude writes LaTeX. It is project-agnostic: the rules
here apply to a terse formula sheet and a verbose lecture handout equally.
Where a rule must be adapted for a specific project, that adaptation lives in
the project's own `project_philosophy.md`, which takes precedence over this
skill for that project only.

This skill governs both **file production** and **LaTeX advice**. When
reviewing, explaining, or recommending LaTeX without producing a file, the
same principles apply. Advising someone to use `eqnarray` is wrong for the
same reason writing it is wrong.

**What this skill governs:**
- File header format and versioning
- Package import conventions
- Math environments and notation
- Table conventions
- Inline commenting discipline
- File structure and modularity (`\input` vs. `\include`)
- File naming (applies to all projects, not only multi-file ones)
- Bibliography and citation workflow (BibTeX vs. biblatex/biber)
- Symbol selection and disambiguation
- Antipatterns to avoid — consult `antipatterns.md` *before* generating, not
  only when reviewing
- Compile warning philosophy
- Verification workflow (chktex, full compile, KNOWN WARNINGS lifecycle)

**What this skill does NOT govern:**
- Document class selection
- Font choices
- Page geometry or margin widths
- Information density or whitespace preferences
- Project-specific macro libraries
- Any opinion about what a document should *contain*

---

## Systems-Level Framing

A `.tex` file is source code. It will be read by humans, diffed, versioned,
compiled by machines, and handed to collaborators who weren't in the room when
decisions were made. Every convention in this skill exists to serve one or more
of these four goals:

1. **Readability** — a future reader (including future you) can understand
   intent without reverse-engineering it from output.
2. **Maintainability** — changes are local, diff-friendly, and don't ripple
   unexpectedly through the document.
3. **Portability** — the file compiles cleanly in standard distributions
   (TeX Live, MiKTeX) without undocumented dependencies.
4. **Correctness** — the typeset output faithfully represents the intended
   mathematics and structure, with no silent errors hiding in unread warnings.

When a rule in this skill seems arbitrary, trace it back to one of these four
goals. If a project-level decision conflicts with this skill, ask which goals
are being traded and whether that trade is intentional.

Rules below are tagged [R], [M], [P], or [C] to indicate which goal(s) they
primarily serve.

---

## Reference Files

`SKILL.md` is a routing document. The detail lives in the reference files
below. How much to read depends on what's being done — see **Proportional
Response** immediately below the file table.

| File | Contents |
|------|----------|
| `references/headers.md` | File header format, versioning scheme, known-warnings field |
| `references/packages.md` | Package import conventions, grouping, load order |
| `references/math.md` | Math environments, notation, units, labeling, physics notation packages |
| `references/tables.md` | booktabs style, tabularx, siunitx, caption placement |
| `references/comments.md` | Inline commenting discipline, TODOs, sign conventions |
| `references/structure.md` | `\input` vs `\include`, standalone vs modular files, custom commands, collaborator communication |
| `references/naming.md` | File naming conventions for .tex, .bib, and figure files |
| `references/antipatterns.md` | Explicit list of things never to do, with rationale |
| `references/warnings.md` | Compile warning philosophy, triage, and documentation |
| `references/compilation.md` | Claude's verification workflow: chktex, full compile, log parsing, KNOWN WARNINGS updates |
| `references/symbols.md` | Common math/physics/engineering symbols, semantic meaning, correct commands |
| `references/bibliography.md` | BibTeX vs biblatex/biber, .bib conventions, citation styles, migration |

---

## Proportional Response

Not every task warrants reading the full reference set. Unnecessary reads cost
time and tokens without improving output. Match the effort to the scope.

**Tier 1 — New file generation**
Read `antipatterns.md` first (always), then the full set of files relevant to
what the document contains. Use the routing guide below. Run chktex after
generating.

**Tier 2 — Edits and advice**
This covers both surgical edits to existing source *and* LaTeX advice without
producing a file. Both involve reasoning about specific LaTeX decisions rather
than building something from scratch — the same proportional reading applies.
Read `antipatterns.md` + the one or two reference files directly relevant to
the specific change or question. Run chktex only if the edit changes compiled
output (same threshold as VERSION increments).

**Tier 3 — Comment, header, or metadata-only changes**
Changes that don't affect compiled output: updating PURPOSE, resolving a TODO,
adding a KNOWN WARNINGS entry, fixing a comment. No reference file reads
required. No chktex.

When in doubt about which tier applies, default to Tier 2.

---

## Routing Guide

Which reference files to read for common tasks:

- *Starting a new .tex file from scratch* → `headers.md`, `packages.md`,
  `naming.md`, `structure.md`
- *Writing or editing math* → `math.md`
- *Writing or editing a table* → `tables.md`
- *Adding comments or TODOs* → `comments.md`
- *Setting up a multi-file project* → `structure.md`, `naming.md`
- *Naming any file (standalone or multi-file)* → `naming.md`
- *Defining or reviewing custom commands / macros* → `structure.md`
- *Looking up a symbol or choosing between symbol variants* → `symbols.md`
- *Setting up citations, a bibliography, or choosing between biblatex and bibtex* → `bibliography.md`
- *Setting up theorem, proof, lemma, or definition environments* → `math.md`
- *Physics notation packages (`physics`, `physics2`)* → `math.md`
- *Before generating any LaTeX output* → scan `antipatterns.md` first
- *Reviewing or auditing existing LaTeX* → `antipatterns.md`, `warnings.md`
- *Running chktex or deciding whether to compile* → `compilation.md`
- *Updating the KNOWN WARNINGS block after verification* → `compilation.md`, `headers.md`
- *Unsure* → read `antipatterns.md` first; it covers the highest-impact issues
  in the smallest space

---

## Diff Discipline

When making edits to an existing `.tex` file, prefer surgical changes over
full rewrites. A change that touches three lines should produce a three-line
diff, not a re-emission of the entire file. This is not just housekeeping —
minimal diffs preserve version history legibility and make review tractable.

Apply this principle to comment additions too: when a known-warnings entry is
added to a file header, or a TODO is resolved, those are diffs, not rewrites.

---

## Project Philosophy Files

Each project that uses this skill should maintain a `project_philosophy.md`
at the project root. This file is the bridge between this skill's universal
rules and the specific decisions a project has made. Its primary audience is
any future collaborator — human or AI — who needs to get up to speed without
asking questions.

**Minimum expected contents:**

- Project name, author(s), and one-paragraph purpose statement
- Document class choice and rationale
- Compiler and distribution if non-default (e.g., LuaLaTeX, MiKTeX)
- Project-specific packages beyond this skill's defaults, with rationale
- Directory and file naming conventions if they deviate from `naming.md`
- Whitespace, density, and layout preferences
- Any deliberate deviation from this skill, with justification
- Domain conventions in force: sign conventions, unit systems, notation
  choices that affect how math and tables are written throughout the project
- Custom command inventory: what is defined in `macros.tex` and why

**Every item above must be answered explicitly.** If a project has no requirement for a given item, write `N/A — not applicable` with a brief reason (e.g., `N/A — single standalone file, no directory structure to define`). A missing item is ambiguous; `N/A` with a reason is not.

**Decisions this skill explicitly leaves to `project_philosophy.md`:**

The following are open by design and must be resolved per project: document
class, font selection, page geometry, information density, custom label
schemes, TODO/HACK variation labels beyond the defaults, and any versioning
scheme other than `YYYYMMDD.NNN`.

If no `project_philosophy.md` exists, apply this skill's defaults without
deviation and note its absence to the collaborator.
