# Compile Warning Philosophy

A compile warning is not a formality. It is the compiler telling you something
is wrong, uncertain, or worth your attention. The correct response is never to
ignore it — it is to *evaluate* it and make a conscious decision. This file
covers how to make that decision well. [C, R, M]

For Claude's specific verification workflow — when to run chktex, when to
offer a full compile, and how to update the `KNOWN WARNINGS` block — see
`compilation.md`.

---

## The Core Distinction

There are two kinds of unreviewed warnings:

1. **Warnings you haven't seen** — you compiled, warnings appeared, you didn't
   read them. This is a gap in process.
2. **Warnings you've seen and accepted** — you read the warning, understood it,
   decided the tradeoff was acceptable, and documented it. This is engineering.

The goal of this skill is to eliminate category 1 entirely. Category 2 is
legitimate and sometimes unavoidable — but it must be *deliberate*, not
accidental. The `KNOWN WARNINGS` header block exists precisely to make this
distinction visible. → `headers.md`

---

## Triage: Fix, Accept, or Investigate

When a warning appears, ask three questions in order:

**1. Do I understand what this warning means?**
If no — investigate before doing anything else. A warning you don't understand
cannot be responsibly accepted. Look up the warning text, check the package
documentation, and reproduce it in isolation if needed. An unexplained warning
accepted on faith is a deferred bug.

**2. Does fixing it improve the document?**
Some warnings have trivially correct fixes. An undefined reference, a missing
label, a package load order issue — these should simply be fixed. The cost is
low; the risk of leaving them is not.

**3. Is the tradeoff genuinely acceptable?**
Some warnings reflect real constraints: a layout that can't be fully satisfied
within fixed column widths, a package conflict in a constrained environment, a
font substitution imposed by a submission template. These can be accepted — but
only with a documented rationale in the `KNOWN WARNINGS` block.

---

## Warning Triage Reference

| Warning | Severity | Default disposition |
|---------|----------|-------------------|
| Undefined reference (`\ref`, `\cite`) | High | Always fix — compile twice if needed |
| Overfull `\hbox` > 5pt | High | Fix — text is visibly outside margin |
| Overfull `\hbox` 2–5pt | Medium | Fix if possible; document if accepted |
| Overfull `\hbox` ≤ 2pt | Low | Usually acceptable; document if kept |
| Underfull `\hbox` / `\vbox` | Low–Medium | Investigate; often fixable with `\sloppy` or rewording |
| Font substitution | Medium | Investigate; document if accepted |
| Package load order | High | Fix — silent failures lurk here |
| Multiply defined label | High | Fix — reference targets are ambiguous |
| `draft` mode reminder | Informational | Acceptable during drafting; remove before final |
| Missing `\bibdata` / `\bibstyle` | High | Fix — bibliography won't compile correctly |
| Citation undefined | High | Fix before sharing |
| Marginpar on wrong side | Low | Cosmetic; document if accepted |

This table is a guide, not a decision tree. Context matters — a 3pt overfull
`\hbox` in a draft for personal use is different from the same warning in a
document going to a journal or a client. [C]

---

## Presenting Warnings to a Collaborator

When delivering a document that has accepted warnings, do not deliver the PDF
alone. Present the warnings alongside it:

> "The document compiled with two warnings. One is a 3pt overfull `\hbox` on
> line 84 — the text is marginally wide because the enzyme name in the
> decomposition reaction (Section 2.2) doesn't break cleanly across the column
> width. It's not visible at normal reading distance. The other is a font
> substitution warning from the department template; it's in the template
> itself and not something we control. Both are documented in the file header.
> Does this work for your purposes?"

The example above demonstrates the right level of specificity: line number
(code-level) *and* what the content is (human-level). A collaborator with
print or accessibility constraints needs both to make an informed decision.

This is the appropriate moment to make a collaborative decision. The
collaborator may have constraints (print reproduction, accessibility
requirements, submission standards) that change the calculus. Presenting
warnings honestly gives them the information to decide. [R, M]

If the collaborator confirms the warnings are acceptable, they remain
documented in the `KNOWN WARNINGS` block — the conversation has happened, the
decision is recorded. If they want them fixed, fix them before delivery.

---

## When to Stop and Ask

Do not silently accept warnings on a collaborator's behalf. Stop and ask when:

- Any warning is not fully understood
- A warning affects content correctness (undefined references, citation
  errors, multiply defined labels)
- A warning suggests a package conflict that may have produced incorrect output
- The warning count increases unexpectedly between versions

Stopping to ask is not inefficiency — it is triage done correctly. Attempting
to fix all warnings without discussion can consume significant effort on a
document that needs the collaborator's input before that work is warranted.
Triage is a collaborative act, not a unilateral one. [R, M]

Silently delivering a document with unreviewed warnings — even minor-seeming
ones — transfers unexamined risk to the collaborator. That is not acceptable
practice regardless of deadline pressure. [C, R]

---

## Warnings vs. Errors

LaTeX distinguishes errors (compilation stops or produces garbage) from
warnings (compilation continues, output may be imperfect). This distinction
is useful but should not create a false hierarchy where warnings are treated
as inconsequential simply because the PDF compiled.

Many of the most damaging LaTeX failures — wrong reference numbers, silent
mathematical spacing errors, incorrect bibliography entries — manifest as
warnings, not errors. Treat warnings with the same seriousness as errors until
you have evaluated them. [C]

---

## The `draft` Mode Exception

`draft` mode suppresses some visual output (figures replaced by boxes, etc.)
and may generate informational reminders. These are expected and acceptable
during drafting. Before final compilation:

1. Remove `draft` from `\documentclass` options
2. Compile in final mode
3. Review the full warning list from scratch — draft-mode warnings and
   final-mode warnings are not the same set
4. Update the `KNOWN WARNINGS` header block accordingly

Do not carry a `KNOWN WARNINGS` block populated during draft mode into a final
document without re-evaluating each entry. [C, M]

---

## Logging

LaTeX writes a `.log` file on every compilation. For any non-trivial document,
read the log — not just the terminal output, which truncates and summarizes.
The full log contains:

- Exact line numbers for every warning
- Package version information useful for diagnosing conflicts
- `hbox` dimensions for overfull/underfull decisions
- The full text of warnings that terminal output abbreviates

When using `latexmk` (see `structure.md`), the `.log` file is written to the
same directory as the root `.tex` file by default, or to the output directory
if `-outdir` is set. `latexmk`'s terminal output summarizes warnings across all
passes; the `.log` file contains the per-pass detail. Read the log from the
*final* pass — the one that produced the delivered PDF.

In a git-tracked project, the `.log` file should be in `.gitignore` — it is
build output, not source. Read it before committing a version you intend
to share. [C, M]
