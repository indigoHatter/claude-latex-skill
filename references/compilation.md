# Compilation and Verification Workflow

Claude cannot compile LaTeX natively — there is no typesetting pass, no box
measurement, no font loading. This means some categories of warnings are
structurally undetectable without running external tools. This file defines
what Claude *can* check, when to check it, and when to offer a full compile.

---

## What Claude Can and Cannot Detect

| Issue category | Detectable by Claude? | Tool needed |
|---|---|---|
| Syntax errors (malformed commands, missing braces) | Partially — obvious ones | chktex, pdflatex |
| Bad spacing (missing `\,`, `\quad` patches) | Yes — source inspection | — |
| Wrong environments (`eqnarray`, `$$`) | Yes — source inspection | — |
| Package antipatterns (load order, deprecated) | Yes — source inspection | — |
| Overfull / underfull `\hbox` | **No** | pdflatex |
| Undefined references | **No** | pdflatex (two passes) |
| Font substitution warnings | **No** | pdflatex |
| Label collisions | **No** | pdflatex |
| chktex lint warnings | **No** | chktex |

The first three rows are covered by reading the source against `antipatterns.md`
before generating. Everything below that line requires a tool.

---

## Default Workflow: chktex After Every Generation

After producing or editing any `.tex` file, run chktex automatically:

```bash
chktex -q filename.tex
```

`-q` suppresses the banner; output is warnings only. Parse the result and
report findings to the collaborator before delivering the file.

**What chktex catches that matters:**
- Mismatched delimiters and braces
- Wrong inter-word vs. inter-sentence spacing (`foo. Bar` vs. `foo.\ Bar`)
- Punctuation inside math mode
- Use of `$$` and other TeX primitives
- Missing `\,` before units (partially)
- Some label/reference issues
- Various typographic conventions

**What chktex does not catch:**
- Overfull/underfull `\hbox` — requires the typesetting pass
- Undefined references — requires aux file from a compile pass
- Font substitutions — requires font loading
- Any warning that depends on actual box dimensions

Always communicate this scope to the collaborator: *"chktex passed — structural
lint is clean. Overfull boxes and undefined references require a compile to
check."* Never imply the document is warning-free based on chktex alone. [C, R]

### Updating the KNOWN WARNINGS block after chktex

If chktex returns no warnings:

```latex
% KNOWN WARNINGS:
%   (none - chktex clean; compile to verify layout warnings)
```

This is more honest than bare `(none)` — it communicates what was checked
and what wasn't. [C, R]

If chktex returns warnings worth documenting:

```latex
% KNOWN WARNINGS:
%   [20260507] chktex W8 line 42: Wrong length of dash. Intentional em-dash
%              in running text; suppressed.
```

---

## Full Compile: When to Offer It

A full `pdflatex` compile (with log parsing) catches everything chktex
misses: overfull boxes, undefined references, font issues. It is slower and
produces a PDF that is usually discarded. Offer it in the following situations:

**Offer proactively when:**
- chktex returns zero warnings *and* the collaborator signals the file is
  near-final ("this looks good", "almost done", "ready to submit")
- The document contains complex floating content (many figures, tables,
  multi-column layouts) where overfull boxes are likely
- The file has cross-references, a bibliography, or a TOC — things that
  require multiple passes and where undefined references are plausible

**Do not offer on first generation.** The file is not ready for layout review
yet; chktex is sufficient for early drafts.

**Phrasing:** *"chktex is clean. Want me to do a full compile to check for
overfull boxes and undefined references? I'll read the log and discard the
PDF."*

### Running a full compile

```bash
pdflatex -interaction=nonstopmode filename.tex
```

`-interaction=nonstopmode` prevents hanging on errors. Run twice if the
document has references or a TOC (second pass resolves aux-file dependencies):

```bash
pdflatex -interaction=nonstopmode filename.tex
pdflatex -interaction=nonstopmode filename.tex
```

For documents with a bibliography:

```bash
pdflatex -interaction=nonstopmode main.tex
biber main          # or: bibtex main (if using BibTeX)
pdflatex -interaction=nonstopmode main.tex
pdflatex -interaction=nonstopmode main.tex
```

Or use `latexmk`, which handles pass management automatically (see
`structure.md`):

```bash
latexmk -pdf -interaction=nonstopmode main.tex
```

### Reading the log

After compilation, read the `.log` file — not just terminal output, which
truncates. Key patterns to search for:

```bash
grep -n "Overfull\|Underfull\|Warning\|Error\|undefined" filename.log
```

Report all findings before delivering. Update the `KNOWN WARNINGS` block:

```latex
% KNOWN WARNINGS:
%   [20260507] Overfull \hbox (4.2pt too wide) in paragraph at line 84.
%              Fixed-width column constraint; acceptable at this size.
%   [20260507] Underfull \hbox (badness 1247) in paragraph at line 112.
%              Sparse paragraph; acceptable for this layout.
```

If the compile is clean, update accordingly:

```latex
% KNOWN WARNINGS:
%   (none - chktex and pdflatex both clean)
```

### What to do with the PDF

Follow the user's lead:

- **User asked for a PDF** — deliver it alongside the `.tex`, regardless of
  warnings. Warnings belong in the `KNOWN WARNINGS` block; they don't gate
  delivery. The user can see the output and decide what's acceptable.
- **User asked for `.tex` only, and a compile was run for verification** —
  note in the response that a PDF was generated during verification and is
  available if wanted. Discard it by default, but don't silently vanish it
  without mentioning it.
- **Compile produced hard errors** (broken output, corrupted references,
  package failures that prevent a usable PDF) — discard unconditionally and
  report the errors. A malformed PDF has no value as a deliverable.

When warnings surface during a compile-for-verification, the PDF is often
exactly what the user needs to decide whether to accept them — particularly
for layout issues like overfull boxes that are only meaningful when seen. Offer
it in that case rather than discarding.

```bash
# Discard PDF and build artifacts when not delivering
rm -f filename.pdf filename.aux filename.log filename.out filename.bcf filename.run.xml
# Or with latexmk:
latexmk -C
```
