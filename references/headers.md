# File Headers

Every `.tex` file produced by this skill begins with a comment block header.
No exceptions. A file without a header is a file that will confuse its next
reader — which may be you, six months from now. [R, M]

---

## Required Header Fields

```latex
% ============================================================
% FILE:     <filename.tex>
% PROJECT:  <project name or "standalone">
% VERSION:  <YYYYMMDD.NNN>
% CREATED:  <YYYYMMDD>
% AUTHOR:   <name or handle>
% PURPOSE:  <one or two sentences: what this file is and what it produces>
% REQUIRES: <see REQUIRES field definition below>
% ============================================================
% KNOWN WARNINGS:
%   <unknown - new doc>
% ============================================================
```

Every field is mandatory. If a field is genuinely unknown at creation time,
write `<unknown>` — do not omit the line.

---

## Field Definitions

**FILE** — The actual filename, including extension. Redundant with the
filesystem, but valuable when files are copied, printed, or viewed out of
context. [R]

**PROJECT** — The name of the containing project. Use `"standalone"` if the
file is a self-contained document with no siblings. [R, M]

**VERSION** — See versioning section below. [M]

**CREATED** — The date the file was first written, in `YYYYMMDD` format.
Unlike `VERSION`, this field never changes after initial creation. It records
the file's origin independently of version history, which matters when a file
is read out of context, copied outside the repository, or the project has no
version control. Use the author's local date, consistent with `VERSION`
convention. [R]

**AUTHOR** — The person responsible for the file's content. Not a copyright
claim — a contact point. Use a name, username, or institutional handle. [R]

**PURPOSE** — The most important field. Write what this file *does*, not what
it *is*. Bad: `"Lecture notes."` Good: `"Produces the Week 3 handout covering
Gauss's Law; intended for print at 100%, two-sided."` [R]

**REQUIRES** — Lists dependencies needed to compile this file. The meaning
of this field differs by file type:

- *Root files* (`main.tex`): list the build environment — compiler,
  distribution, and any non-standard `.sty` files or fonts a standard
  distribution would not include. Do not restate the preamble; packages
  are visible and documented there. If only standard CTAN packages are
  used, write `pdflatex, MiKTeX (or TeX Live 2023+); standard CTAN
  packages only`. This tells a new collaborator everything they need to
  get the project compiling without reading the preamble.

- *Standalone files:* any package, font, or external file that a standard
  TeX Live installation would not include. Write `"none"` if only standard
  packages are used. This field lets a new collaborator get the file
  compiling without reading the entire preamble. [P, R]

- *Content files* (`\input`-ed or `\include`-d by a root): list only what
  *this file introduces* — not the full preamble, just its additions. This
  makes the root file auditable: every package in the preamble should be
  traceable to at least one file's `REQUIRES` field. Write `"none"` if this
  file introduces no new dependencies — meaning you checked, not that you
  skipped the field.

  See the Non-Standalone Files section below and `structure.md` for detail.

---

## Versioning

**Format:** `YYYYMMDD.NNN`

- `YYYYMMDD` — ISO 8601 date of the most recent edit
- `NNN` — zero-padded revision counter, reset to `001` each new date

**Examples:**
```
20260507.001   % first edit on May 7, 2026
20260507.002   % second edit same day
20260508.001   % first edit the following day
```

This scheme is:
- Lexicographically sortable without a parser
- Human-readable at a glance
- Unambiguous across time zones (use the author's local date)

Projects may substitute a different versioning scheme (e.g., semantic
versioning tied to a course unit) as long as it is documented in
`project_philosophy.md` and applied consistently across all files in the
project. [M]

**When to increment the version:**
- Increment on every edit session that changes compiled output
- Do not increment for comment-only changes that don't affect the PDF
- When in doubt, increment — false positives are cheap, missed versions are not

---

## Known Warnings

The `KNOWN WARNINGS` block documents the verification status of compile-time
warnings. Its purpose is to make one distinction visible at a glance: *"we
know what the warnings are"* vs. *"we don't."* [C, R]

### The three valid states

**New or revised — unverified:**

```latex
% KNOWN WARNINGS:
%   <unknown - new doc>
```

```latex
% KNOWN WARNINGS:
%   <unknown - new revision>
```

These are the correct defaults when Claude produces or modifies a file without
running verification. `<unknown - new doc>` appears on first creation.
`<unknown - new revision>` replaces any existing warnings content whenever
an edit changes compiled output (see trigger rule below). Both states signal
clearly: *this file has not been checked yet.*

**Verified clean:**

```latex
% KNOWN WARNINGS:
%   (none)
```

`(none)` means verification was run and returned no warnings — not that
warnings weren't checked. Never write `(none)` without having run chktex or
compiled. A blank block is ambiguous; always write one of these three states.

**Verified with accepted warnings:**

```latex
% KNOWN WARNINGS:
%   [20260507] Overfull \hbox in line 84 (~4pt). Acceptable given fixed column
%              width constraint; revisit if layout changes.
%   [20260507] Package 'microtype' warning on draft mode. Suppressed in final.
```

Each accepted warning includes the date of the decision and a brief rationale.

### Revision trigger rule

Reset to `<unknown - new revision>` whenever an edit changes compiled output.
The same rule that governs VERSION increments governs this reset:

- **Triggers reset:** any change to document content, math, structure, packages,
  or anything else that affects the PDF
- **Does not trigger reset:** comment-only changes, header field updates
  (VERSION, PURPOSE, AUTHOR, CREATED), TODO resolution with no content change

This keeps the block meaningful. If every trivial edit reset it, the state
would always be unknown and nobody would read it.

### Rules for this block

- Add an accepted-warning entry only after a warning has been reviewed and a
  conscious decision made to accept it
- Each entry must include the date of the decision and a brief rationale
- Entries are diffs, not rewrites — add new lines; do not regenerate the block
- Resolved warnings are removed; do not leave stale entries

**What belongs here vs. what should be fixed:**

| Warning type | Disposition |
|---|---|
| Overfull `\hbox` ≤ 2pt | Usually acceptable; document if kept |
| Overfull `\hbox` > 5pt | Fix it — text is visibly outside the margin |
| Undefined reference | Always fix before sharing |
| Font substitution | Investigate; document if accepted |
| Package load order | Fix it — silent failures lurk here |
| `draft` mode reminders | Acceptable during drafting; remove before final |

See `warnings.md` for the full triage philosophy and Claude's verification
workflow. [C, R, M]

---

## Receiving Files Without Headers

When asked to edit or review an existing `.tex` file that has no header, do
not silently add one mid-task — it conflates two separate changes in the diff.
Instead:

1. Note the absence to the collaborator: *"This file has no header. I can add
   one before making your edits — want me to?"*
2. If the answer is yes, add the header as a distinct first diff, then make
   the requested edits as a second diff
3. If the answer is no, proceed with the requested edits only and leave a
   `% TODO: add file header` comment at the top of the file [R, M]

The header is not optional in files Claude *produces*. It is a conversation in
files Claude *inherits*.

---

## Non-Standalone Files

Files that are `\input`-ed or `\include`-d into a root document (i.e., they
don't have their own `\documentclass`) still get a header. The header is
identical in format, with two adaptations:

1. `PROJECT` names the root document (e.g., `"week3-handout"`)
2. `REQUIRES` lists the packages *this file introduces* — not the full preamble,
   just what this file adds. This makes the root preamble auditable: every
   package should be traceable to at least one file's `REQUIRES` field. If this
   file genuinely introduces no new dependencies, write `"none"` — meaning you
   checked, not that you skipped the field.

This ensures the header remains useful even when the file is read in isolation,
and keeps dependency traceability flowing upward to the root. [M, P]
